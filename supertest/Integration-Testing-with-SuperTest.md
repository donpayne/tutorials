![](https://github.com/donpayne/tutorials/raw/master/supertest/img/post_04.png)
Inspired by examples found around the web.

# Automation or Bust
Node web application routes can be tested using the Supertest library.  Supertest can start up a node app, make http requests to routes, and run assertions on http responses.  It can pass parameters to routes and run assertions on things like the response http status code, content type, and execute regular expressions against the response body.

If your routes depend on accessing a database or api I recommend mocking that data for your route tests.  This way your database or api won’t influence your route test results.

More examples on [GitHub](https://github.com/visionmedia/supertest)

## Installing Gulp and Testing Tools
- Gulp - Install locally and globally to allow you to run Gulp from the command line.  
- Gulp-Nodemon - (A popular utility that restarts your server anytime a file is updated)
- Gulp-Mocha - (A popular unit testing framework)
- Should - (A popular assertion framework)
- Sinon - (A popular spying and mocking framework)
- SuperTest - (A popular integration testing framework)
- Gulp-Env - (A popular environment variable management tool)
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
```

## Export the App
In order have the application visible to the integration tests you will need to export it.  This is done at the bottom of the app.js file.
```
module.exports = app;
```

## Setup Tests
Setup the Tests by including dependent modules, the application, and necessary database models.
```javascript
var	should   = require('should'),
	request  = require('supertest'),
	app      = require('../app.js'),
	db       = require('mongoose'),
	Model    = db.model('someModel'),
	agent    = request.agent(app);
```

## Example 1 - Expect returned property and id
```javascript
describe('POST /items', function()
{
	it('should allow a resource to be posted and return someProperty and id', function (done)
	{
		var modelPost = 
		{
			id           : '12345', 
			someProperty : true
		};

		agent.post('/api/items')
			.set('Accept', 'application/json')
			.send(modelPost)
			.expect('Content-Type', 'application/json; charset=utf-8')
			.expect(200)
			.end(function (err, res)
			{
				should.not.exist(err);
				res.body.someProperty.should.not.equal(false);
				res.body.should.have.property('id');
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

## Example 2 - Expect returned Array
```javascript
describe('GET /items', function()
{
	it('should respond with a successful array', function (done) 
	{
		agent.get('/api/items')
			.set('Accept', 'application/json')
			.expect('Content-Type', 'application/json; charset=utf-8')
			.expect(200)
			.end(function(err, res) 	
			{
				should.not.exist(err);
				res.should.have.status(200);
				res.body.should.be.array;
				done();
			});
	});
});
```

## Example 3 - Expect Order
Expectations are run in the order of definition. This characteristic can be used to modify the response body or headers before executing an assertion.  If you pass done in the .expect method, you don't have to use the .end method.
```javascript
describe('GET /user', function()
{
	it('user.name should be an case-insensitive match for "tobi"', function (done)
	{
		agent.get('/api/user')
		.set('Accept', 'application/json')
		.expect(function (res) 
		{
			res.body.id   = 'some fixed id';
			res.body.name = res.body.name.toUpperCase();
		})
		.expect(200, 
		{
			id: 'some fixed id',
			name: 'TOBI'
		}, done);
	});
});
```

## Example 4 - Expect Attachment Upload
```javascript
describe('upload', function() 
{
	it('a file', function(done) 
	{
		agent.post('/api/attachments')
			.field('extra_info', '{"in":"case you want to send json along with your file"}')
			.attach('image', 'path/to/file.jpg')
			.end(function(err, res) 
			{
				should.not.exist(err);
				res.should.have.status(200);
				done();
			});
	});
});
```

## Example 5 - Expect Multiple Attachment Uploads
```javascript
describe('create album', function () 
{
	it('valid ', function (done) 
	{
		agent.post('/api/albums')
			.set('Authorization', 'Token eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.IjkxMTg3NTk1ODg2MCI.gq32xfcOhv5AiZXJup5al1DGG0piyGWnrjZ5NouauCU')
			.field('Content-Type', 'multipart/form-data')
			.field('name', 'moni')
			.field('description', 'Nature+Pics')
			.field('caption', 'nature')
			.field('contacts', '["' + 911354971564 + '","' + 919092888819 + '"]')
			.field('dimensions', 
				'{"photo1":{"height": 10, "width": 10}, ' + 
				'"photo2":{"height": 20, "width": 20}, ' + 
				'"photo3":{"height": 20, "width": 20}, ' + 
				'"photo4":{"height": 20, "width": 20}, ' + 
				'"photo5":{"height": 20, "width": 20}}'
			)
			.attach('photo1', '/home/monica/Desktop/pic/1.jpeg')
			.attach('photo2', '/home/monica/Desktop/pic/2.jpeg')
			.attach('photo3', '/home/monica/Desktop/pic/3.jpeg')
			.attach('photo4', '/home/monica/Desktop/pic/4.jpeg')
			.attach('photo5', '/home/monica/Desktop/pic/5.jpeg')
			.end(function (err, res) 
			{
				should.not.exist(err);
				res.should.have.status(200);
				done();
			});
	});
});
```

## Example 6 - Calling an External Service
It can be helpful to have tests for an external service used by your app. If you rely on external services and you have tests for them then when your app breaks it easier to determine if the problem is with your code base or the external service.

Mocha makes it easily to asynchronously test an api endpoint. Here’s a test I have for flickr’s public feed api:
```javascript
// flickr public feed api
function API (params) 
{
	this.uri = 'http://api.flickr.com/services/feeds/photos_public.gne';
	this.qs = 
	{
		tags: params.tags || '',
		tagmode: params.tagmode || '',
		format: params.format || 'json'
	};
}

// test
describe('flickr public feed api', function () 
{
	it('should return expected json format', function (done) 
	{
		var apiTest = new API(
		{
			tags: 'california',
			tagmode: 'all'
		});

		request.get(apiTest, function (error, response, body) 
		{
			response.statusCode.should.equal(200, 'Invalid http status code');

			var json = jsonpHelper.parseJSONP(body);
			json.should.have.property('items');

			var photos = json.items;
			photos.should.be.an.instanceOf(Array);

			photos.forEach(function (photo) 
			{
				photo.should.have.keys(
					'title',
					'link',
					'media',
					'date_taken',
					'description',
					'published',
					'author',
					'author_id',
					'tags'
				);
			});

			done();
		});
	});
});
```

## Example 7 - Sending and Receiving Cookies
This tests depends on the CookieParser module
```javascript
describe('Cookie Handling', function ()
{
	app.use(express.cookieParser());

	app.get('/', function (req, res)
	{
		res.cookie('cookie', 'hey');
		res.send();
	});

	app.get('/return', function (req, res)
	{
		if (req.cookies.cookie) res.send(req.cookies.cookie);
		else res.send(':(')
	});	

	it('should save cookies', function (done)
	{
		agent.get('/')
			.expect('set-cookie', 'cookie=hey; Path=/', done);
	});

	it('should send cookies', function(done)
	{
		agent.get('/return')
			.expect('hey', done);
	});
});
```

## Example 8 - Multiple Agents and Cookies
This test is insane!
```javascript
var express = require('express')
	, app = express()
	, request = require('../../')
	, assert = require('assert')
	, should = require('should')
	, cookieParser = require('cookie-parser')
	, session = require('express-session');

app.use(cookieParser());
app.use(session({
	secret: 'secret',
	resave: true,
	saveUninitialized: true
}));

app.post('/signin', function(req, res) {
	req.session.user = 'hunter@hunterloftis.com';
	res.redirect('/dashboard');
});

app.post('/setcookie', function(req, res) {
	res.cookie('cookie', 'jar');
	res.sendStatus(200);
});

app.get('/getcookie', function(req, res) {
	res.status(200).send(req.cookies.cookie);
});

app.get('/dashboard', function(req, res) {
	if (req.session.user) return res.status(200).send('dashboard');
	res.status(401).send('dashboard');
});

app.all('/signout', function(req, res) {
	req.session.regenerate(function() {
		res.status(200).send('signout');
	});
});

app.get('/', function(req, res) {
	if (req.session.user) return res.redirect('/dashboard');
	res.status(200).send('home');
});

app.post('/redirect', function(req, res) {
	res.redirect('/simple');
});

app.get('/simple', function(req, res) {
	res.status(200).send('simple');
});

app.listen(4000);

describe('request', function() {
	describe('persistent agent', function() {
		var agent1 = request.agent();
		var agent2 = request.agent();
		var agent3 = request.agent();
		var agent4 = request.agent();

		it('should gain a session on POST', function(done) {
			agent3
				.post('http://localhost:4000/signin')
				.end(function(err, res) {
					should.not.exist(err);
					res.should.have.status(200);
					should.not.exist(res.headers['set-cookie']);
					res.text.should.include('dashboard');
					done();
				});
		});

		it('should start with empty session (set cookies)', function(done) {
			agent1
				.get('http://localhost:4000/dashboard')
				.end(function(err, res) {
					should.exist(err);
					res.should.have.status(401);
					should.exist(res.headers['set-cookie']);
					done();
				});
		});

		it('should gain a session (cookies already set)', function(done) {
			agent1
				.post('http://localhost:4000/signin')
				.end(function(err, res) {
					should.not.exist(err);
					res.should.have.status(200);
					should.not.exist(res.headers['set-cookie']);
					res.text.should.include('dashboard');
					done();
				});
		});

		it('should persist cookies across requests', function(done) {
			agent1
				.get('http://localhost:4000/dashboard')
				.end(function(err, res) {
					should.not.exist(err);
					res.should.have.status(200);
					done();
				});
		});

		it('should have the cookie set in the end callback', function(done) {
			agent4
				.post('http://localhost:4000/setcookie')
				.end(function(err, res) {
					agent4
						.get('http://localhost:4000/getcookie')
						.end(function(err, res) {
							should.not.exist(err);
							res.should.have.status(200);
							assert(res.text === 'jar');
							done();
						});
				});
		});

		it('should not share cookies', function(done) {
			agent2
				.get('http://localhost:4000/dashboard')
				.end(function(err, res) {
					should.exist(err);
					res.should.have.status(401);
					done();
				});
		});

		it('should not lose cookies between agents', function(done) {
			agent1
				.get('http://localhost:4000/dashboard')
				.end(function(err, res) {
					should.not.exist(err);
					res.should.have.status(200);
					done();
				});
		});

		it('should be able to follow redirects', function(done) {
			agent1
				.get('http://localhost:4000/')
				.end(function(err, res) {
					should.not.exist(err);
					res.should.have.status(200);
					res.text.should.include('dashboard');
					done();
				});
		});

		it('should be able to post redirects', function(done) {
			agent1
				.post('http://localhost:4000/redirect')
				.send({ foo: 'bar', baz: 'blaaah' })
				.end(function(err, res) {
					should.not.exist(err);
					res.should.have.status(200);
					res.text.should.include('simple');
					res.redirects.should.eql(['http://localhost:4000/simple']);
					done();
				});
		});

		it('should be able to limit redirects', function(done) {
			agent1
				.get('http://localhost:4000/')
				.redirects(0)
				.end(function(err, res) {
					should.exist(err);
					res.should.have.status(302);
					res.redirects.should.eql([]);
					res.header.location.should.equal('/dashboard');
					done();
				});
		});

		it('should be able to create a new session (clear cookie)', function(done) {
			agent1
				.post('http://localhost:4000/signout')
				.end(function(err, res) {
					should.not.exist(err);
					res.should.have.status(200);
					should.exist(res.headers['set-cookie']);
					done();
				});
		});

		it('should regenerate with an empty session', function(done) {
			agent1
				.get('http://localhost:4000/dashboard')
				.end(function(err, res) {
					should.exist(err);
					res.should.have.status(401);
					should.not.exist(res.headers['set-cookie']);
					done();
				});
		});
	});
});
```