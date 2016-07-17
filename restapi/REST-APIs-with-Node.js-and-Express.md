![](https://github.com/donpayne/tutorials/raw/master/restapi/img/post_02.png)
Inspired by Jonathan Mills, *[www.pluralsight.com](http://www.pluralsight.com/courses/node-js-express-rest-web-services)*

# What Is REST?
REST is the underlying architectural principle of the web. The amazing thing about the web is the fact that clients (browsers) and servers can interact in complex ways without the client knowing anything beforehand about the server and the resources it hosts. The key constraint is that the server and client must both agree on the media used, which in the case of the web is HTML.

## Uniform Interface
The interface should manage requested actions against one or more Resources.  A Resource is a 'Noun' or 'Thing'.  In coming requests use 'Verbs' to determine what the user wants to do with the Resource: 
- POST
- GET
- PUT
- PATCH
- DELETE

## Install Body Parser
```
npm install body-parser --save
```

Include it in your app.js file and run it as middleware against every request.  If you are using a headless requests tool, don't forget to include a Header for 'Content-Type = application/json' to let the server know that you are sending JSON data.
```javascript
// Modules
var	express        = require('express'),
	bodyParser     = require('body-parser');

// Middleware
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json()); 
```

## Setup Express Routes
I like to setup routes dynamically at start up based on the route files residing in my Routes folder.
```javascript
// Modules
var	fs   = require('fs'),
	path = require('path');

// Routing
module.exports = function (app)
{
	var folder = './app/routes';

	fs.readdir(folder, function (err, files)
	{
		if (err) return err;

		files.forEach(function (file)
		{
			var route    = parseRoute(file);
			var resource = '.' + folder + '/' + file;
			app.use(route, require(resource));

			if (route === '/index')
			{
				app.use('/', require(resource));
				app.use('/index.html', require(resource));
			}
		});
	});

	function parseRoute (file)
	{
		var name = path.parse(file).name.split('.');

		if (name.slice(-1)[0] === 'route')
			name.pop()

		return '/' + name.join('/');
	}
};
```

## Base API Route
In order to have the API somewhat self-documenting. I created a base route '/api' that will list all of my resources.  In this example I will be using data from MongoDB via the Mongoose ODM.
```javascript
// Modules
var	express    = require('express'),
	router     = express.Router(),
	controller = require('../controllers/api.controller')();

// Routes
router.route('/')
	.get(controller.get);

module.exports = router;
```

## Base API Controller
The GET route will get the models (resources) from MongoDB and display them with links.
```javascript
// Modules
var	db = require('mongoose'),
	_  = require('underscore');

// API Controller
module.exports = function ()
{
	return {
		get : function (req, res)
		{
			var resources = [];
			_.each(db.models, function (model)
			{
				var resource = {};
				resource.resource = model.modelName.toLowerCase();
				resource.links = {};
				resource.links.self = 'http://' + req.headers.host + req.baseUrl + '/' + resource.resource;
				resources.push(resource);
			});

			res.status(200);
			res.json(resources);
		}
	};
}
```

## Resource API Routes
I created a resource route '/api/someResource' that will list all of this items in a single resource.  I am injecting the Mongoose Model into the controller to facilitate both modularity of the controller as well as enabling mocking when for test purposes.
```javascript
// Modules
var	express    = require('express'),
	router     = express.Router(),
	db         = require('mongoose'),
	controller = require('../controllers/api.resource.controller')(db.model('someResource'));

// Routes
router.route('/')
	.get(controller.get)
	.post(controller.post);

// Middleware to get document from the db
router.use('/:id', controller.cacheId);

router.route('/:id')
	.get(controller.getById)
	.put(controller.putById)
	.patch(controller.patchById)
	.delete(controller.deleteById);

module.exports = router;
```

## Resource API Controller
The API methods are listed out as functions that accept the request and response as arguments.  I like this layout as it keeps the methods very organized.  For the routes that expect a parameter, I am caching the data from the database as middleware in order to streamline the code and reduce repetition.  Also, I am implementing links similar to the Base API route to facilitate self-documenting navigation through the api.
```javascript
// API Controller
module.exports = function (model)
{
	return {
		post : function (req, res)
		{
			var doc = new model(req.body);

			doc.save(function (err)
			{
				if (err) return res.status(500).send(err);
				res.status(201);
				res.json(doc);
			});
		},
		get : function (req, res)
		{
			model.find({}, function (err, docs)
			{
				if (err) return res.status(500).send(err);

				// Create links
				var _docs = [];
				docs.forEach(function (doc, i)
				{
					doc = doc.toJSON();
					doc.links = {};
					doc.links.self = 'http://' + req.headers.host + req.baseUrl + '/' + doc._id;
					_docs.push(doc);
				});

				res.status(200);
				res.json(_docs);
			});
		},
		cacheId : function (req, res, next)
		{
			model.findById(req.params.id, function (err, doc)
			{
				if (err) return res.status(500).send(err);

				if (doc)
				{
					req.doc = doc;
					next();
				}
				else
				{
					res.status(404);
					res.send('Document not found.');
				}
			});
		},
		getById : function (req, res)
		{
			res.status(200).json(req.doc);
		},
		putById : function (req, res)
		{
			for (var key in req.doc)
			{
				if (req.body[key] !== undefined)
					req.doc[key] = req.body[key];
			}

			req.doc.save(function (err)
			{
				if (err) return res.status(500).send(err);
				res.status(200);
				res.json(req.doc);
			});
		},
		patchById : function (req, res)
		{
			if (req.body._id !== undefined)
				delete req.body._id;

			for (var key in req.doc)
			{
				if (req.body[key] !== undefined)
					req.doc[key] = req.body[key];
			}

			req.doc.save(function (err)
			{
				if (err) return res.status(500).send(err);
				res.status(200);
				res.json(req.doc);
			});
		},
		deleteById : function (req, res)
		{
			req.doc.remove(function (err)
			{
				if (err) return res.status(500).send(err);
				res.status(204);
				res.send('Removed.');
			});
		}
	};
}
```

## Optional Filtering on Get
When handling a general Get request on all items in a Resource, you may want to allow the end user to filter the requested items.  This can easily be done with Mongoose since the filter argument is in JSON format.
```javascript
get : function (req, res)
{
	var query = {};

	if(req.query.someProperty)
	{
		query.someProperty = req.query.someProperty;
	}
	model.find(query, function(err, docs)
	{
		if (err) return res.status(500).send(err);
		res.json(docs);
	});
}
```

## Optional Validation on Post
When handling a Post request you may want to sanitize the data prior to submitting it to the database or model.  This can be done by evaluating the body of the request .
```javascript
post : function (req, res)
{
	// Validate that a property Exists
	if(!req.body.someProperty)
	{
		res.status(400).send('someProperty is required');
	}
	else
	{
		var doc = new model(req.body);

		doc.save(function (err)
		{
			if (err) return res.status(500).send(err);
			res.status(201);
			res.json(doc);
		});
	}
}
```

# What is Gulp?

## Gulp vs Grunt
*Excerpt from [www.sitepoint.com](http://www.sitepoint.com/introduction-gulp-js)*

You’ve probably heard about Grunt. Grunt is a Node.js-based task runner.

Gulp is the new kid on the block: it’s a task runner which uses Node.js.

Grunt and Gulp do exactly the same thing. Grunt has been around longer and you’ll find far more help, plug-ins and resources. It’s a great project — if you’re successfully using it now, there’s little reason to switch.

However, nothing is ever perfect and Gulp.js has been developed to solve issues you may have encountered with Grunt:
- Grunt plug-ins often perform multiple tasks; Gulp plug-ins are designed to do one thing only.
- Grunt requires plug-ins for basic functionality such as file watching; Gulp has them built-in.
- Grunt uses JSON-like data configuration files; Gulp uses leaner, simpler JavaScript code.

Not everyone will agree with this last point, but I suggest you view the Gulp presentation slides and decide for yourself.

The most important Gulp concept is streams. Think of your files passing through a pipe; at one or more points along that pipe, an action is taken. For example, we could insert all our JavaScript files into a scripts pipe which:
- concatenates files into one
- removes console and debugger statements
- minifies the code
- puts the resulting file in a specific location.

Data is input into one method. That method outputs new data — which is used as input for the next method. It’s reminiscent of jQuery chaining which applies different actions in sequential order, e.g.
```javascript
$("#element").text("hello world!").addClass("myclass").fadeIn();
```

## Installing Gulp and Testing Tools
Install Gulp locally.  Additionally install Gulp globally to allow you to run Gulp from the command line.  
Install Gulp-Nodemon (A popular utility that restarts your server anytime a file is updated)
Install Gulp-Mocha (A popular unit testing framework)
Install Should (A popular assertion framework)
Install Sinon (A popular spying and mocking framework)
Install SuperTest (A popular integration testing framework)
Install Gulp-Env (A popular environment variable management tool)
```
npm install gulp --save
npm install gulp -g
npm install gulp-nodemon --save
npm install gulp-mocha --save
npm install should --save
npm install sinon --save
npm install supertest --save-dev
npm install gulp-env --save-dev
```

## Initialize and Run Gulp
In the project folder create a gulpfile.js file.  This will handle starting our server and running tasks.  The following code will create a task to launch the application and run all of our Mocha Tests.
```javascript
// Modules
var	gulp      = require('gulp'),
	nodemon   = require('gulp-nodemon'),
	mocha     = require('gulp-mocha'),
	env       = require('gulp-env'),
	supertest = require('supertest');

gulp.task('default', function ()
{
	nodemon(
	{
		script : 'app.js',
		ext    : 'js',
		env    : { PORT: 3000 },
		ignore : [ './node_modules/**' ]
	})
	.on('restart', function ()
	{
		console.log('Restarting');
	});
});

gulp.task('test', function ()
{
	env({ vars: { ENV: 'Test'}});
	gulp.src('./app/tests/*.js', { read: false })
		.pipe(mocha({ reporter: 'nyan' }));
});
```

## Separate PROD and TEST environments
In the app.js file handle the database connection depending on what environment the application is being run in.
```
var db;

if (process.env.ENV == 'Test')
	db = mongoose.connect('mongodb://localhost/db_test');
else
	db = mongoose.connect('mongodb://localhost/db');

module.exports = app;
```

## Export the App
In order have the application visible to the integration tests you will need to export it.  This is done at the bottom of the app.js file.
```
module.exports = app;
```

## Mocha - Unit Testing the Controller
This is a unit test that will mock a database resource model and inject it into our controller code.  The test expects the controller to throw an error when someProperty does not exist in the request body.
```javascript
var	should = require('should'),
	sinon  = require('sinon');

describe('Resource Controller Tests:', function()
{
	describe('Post', function()
	{
		it('should not allow an empty someProperty on post', function()
		{
			var Model = function (model) { this.save = function () {} };

			var req = 
			{
				// body: { someProperty: 'Blah' }
				body: {}
			};

			var res = 
			{
				status : sinon.spy(),
				send   : sinon.spy()
			};

			var Controller = require('../app/controllers/api.resource.controller.js')(Model);
			Controller.post(req,res);

			res.status.calledWith(400).should.equal(true, 'Bad Status ' + res.status.args[0][0]);
			res.send.calledWith('someProperty is required').should.equal(true);
		});

		// Run another Test
		it('should do something', function()
		{
			// Another Condition
		});
	});

	// Run another Suite of Tests
	describe('Get', function()
	{
		// Run another Test
		it('should do something', function()
		{
			// Another Condition
		});
	});
});
```

## SuperTest - Integration Testing the API
This is an integration test.  It will handle the API call all the way down to the database and back up.  Make sure to clean up the database by resetting it back to a clean state. 
```javascript
var	should   = require('should'),
	request  = require('supertest'),
	app      = require('../app.js'),
	db       = require('mongoose'),
	Model    = db.model('someModel'),
	agent    = request.agent(app);

describe('Model Crud Tests', function()
{
	it('Should allow a resource to be posted and return someProperty and _id', function (done)
	{
		var modelPost = 
		{
			_id          : '12345', 
			someProperty : true
		};

		agent.post('/api/someResource')
			.send(modelPost)
			.expect(200)
			.end(function (err, results)
			{
				results.body.someProperty.should.not.equal(false);
				results.body.should.have.property('_id');
				done();
			});
	});

	afterEach(function (done)
	{
		Model.remove().exec();
		done();
	});
});
```