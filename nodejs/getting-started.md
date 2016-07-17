MEAN Stack Development
======================

The MEAN stack is a powerful, full-stack JavaScript solution that comprises four major building blocks: **MongoDB** as the database, **Express** as the web server framework, **AngularJS** as the web client framework, and **Node.js** as the server platform.

- Windows Installation and Configuration
- Creating an Express MVC Project


# Windows Installation and Configuration #

>**Command Line Interfaces (CLI):** While most commands will run just fine using the default Windows command prompt, some won't. In order to avoid the myriad nuances, it is recommend that a different CLI be used on Windows. Since Git should be installed anyway, it is recommended that the GitBash utility be used on Windows as the default CLI for the purposes of dealing with the tools and utilities described herein.

The installation path for these tools will be in `c:\apps\` unless otherwise specified. 

## Git/GitBash ##

>The entire [Pro Git book](http://www.amazon.com/Pro-Git-Scott-Chacon/dp/1484200772/ref=sr_1_1) written by Scott Chacon and Ben Straub is available to [read online for free](http://git-scm.com/book/en/v2)!

[Git](http://git-scm.com/) is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.

Allow Git to install into the default directory instead of the `apps` folder. Git will be used not only as the scm tool, but will also provide a convenient way to deploy to Heroku. GitBash will install along with it and will be the CLI of choice for MEAN development on Windows.

### GitBash and Sublime Text ###

Assuming you are using Sublime Text as your development environment, you can install [the Terminal plugin](http://wbond.net/sublime_packages/terminal) that will add an `Open Terminal Here...` option to the folders context menu. To force it to use GitBash as the terminal, open the user settings via `Preferences > Package Settings > Terminal > Settings - User` and use the following:

```javascript
{
	"terminal": "C:\\Program Files (x86)\\Git\\bin\\sh.exe",
	"parameters": ["--login", "-i"]
}
```

## Node.js ##

- The installer for Node.js can be found [on their website](https://nodejs.org/).
- Install to `c:\apps\nodejs\`.
- Test in GitBash using `node --version` and `npm --version`.

### npm ###

The npm Registry is a public collection of packages of open-source code for Node.js (and [io.js](https://iojs.org/en/index.html)), [front-end web apps](http://www.ember-cli.com/), [mobile apps](http://cordova.apache.org/), [robots](https://tessel.io/), [routers](https://linerate.f5.com/), and countless other needs of the JavaScript community.

Installed alongside Node.js, npm is the command line client that allows developers to install and publish those packages. 

### Express ###

[Express](http://expressjs.com/) is an unopinionated, minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications. Express is installed via npm.

```
npm install -g express
npm install -g express-generator@4
express --version
```

http://expressjs.com/starter/generator.html

### Nodemon (Development) ###

Nodemon is a utility that will monitor for any changes in your source and automatically restart your server - perfect for development. Just use `nodemon` instead of `node` to run your code, and now your process will automatically restart when your code changes.

```
npm install -g nodemon
```

### Forever (Production) ###

[Forever](https://www.npmjs.com/package/forever) is a simple CLI tool for ensuring that a given node script runs continuously (i.e. forever).

```
npm install -g forever
```


### LESS ###

[Less](http://lesscss.org/) is a CSS pre-processor, meaning that it extends the CSS language, adding features that allow variables, mixins, functions and many other techniques that allow you to make CSS that is more maintainable, themable and extendable.


```
npm install -g less
```

### Database Integration ###

After your databases are installed, use the guides here to integrate your Node/Express project with your data.

- [MySQL](http://expressjs.com/guide/database-integration.html#mysql)
- [MongoDB](http://expressjs.com/guide/database-integration.html#mongo)
- [Neo4j](http://expressjs.com/guide/database-integration.html#neo4j)

>Integrating with Neo4j will require Python to be installed?

## MongoDB ##

[MongoDB](http://www.mongodb.org/) is an open-source, document database designed for ease of development and scaling. Use with [Mongoose](http://mongoosejs.com/) - which provides a straight-forward, schema-based solution to modeling your application data and includes built-in type casting, validation, query building, business logic hooks and more, out of the box. 

- Downloaded the MongoDB installer
- Install to `C:\apps\mongo\server\[version]\`
- Create `MONGO` environment variable and add it to the `PATH` environment variable
- Create the data folders
- Install as service and start service 

Under the install folders, create a file named `mongod.cfg`, a folder called `data`, and two sub-folders called `db` and `log`.

**mongod.cfg**

```config
logpath=c:\apps\mongo\server\[version]\data\log\mongod.log
dbpath=c:\apps\mongo\server\[version]\data\db
```

Running `cmd.exe` as administrator, execute the following commands:

```
sc.exe create MongoDB binPath= "\"C:\apps\mongo\server\[version]\bin\mongod.exe\" --service --config=\"C:\apps\mongo\server\[version]\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"
net start MongoDB
```

If you ever need to remove the service, run `cmd.exe` as administrator again and execute these two commands:

```
net stop MongoDB
sc.exe delete MongoDB
```

## Client Side Libraries ##

Download - and extract as needed - the client side libraries needed in your application. You can also use a CDN instead of hosting them in your own project. We're going to take a hybrid approach, where we will first attempt to get them from their respective CDNs, and fallback on local copies if that fails.

- [AngularJS](https://angularjs.org/)
- [Bootstrap](http://getbootstrap.com/)
- [jQuery](http://jquery.com/)

## Heroku ##

[Heroku](https://www.heroku.com/) is a cloud platform as a service ([PaaS](http://en.wikipedia.org/wiki/Platform_as_a_service)). That means you do not have to worry about infrastructure; you just focus on your application.

- Instant Deployment with Git push - build of your application is performed by Heroku using your build scripts
- Plenty of Add-on resources (applications, databases etc.)
- Processes scaling - independent scaling for each component of your app without affecting functionality and performance
- Isolation - each process (aka dyno) is completely isolated from each other
- Full Logging and Visibility - easy access to all logging output from every component of your app and each process (dyno)

You will need to create an account on Heroku before you can start using it.

### Heroku Toolbelt ###

[Heroku Toolbelt](https://toolbelt.heroku.com/) contains the Heroku command line shell and a utility called Foreman. The shell is what you’ll use from terminal to manage your Heroku deployment, and Foreman is very useful for making sure what you’ve built on your machine is setup to run properly on Heroku.

>Choose a custom installation of Heroku Toolbelt and ensure that it isn't also trying to install Git - but install it to its default location.

Once you have signed up for an account and installed the toolbelt on your machine, the last step is to login to your account from terminal.

```
$ heroku login
```

This will prompt you for your username and password. When it's done you’re all setup and ready to go with Heroku.


# Creating an Express MVC Project #

Creating an Express project is easy. Simply create a new project folder and run the command `express` in that folder to create a default express project. Express will automatically create a folder structure for your project and populate it with a `package.json` and `app.js`. Next, run `npm install` to download and install into your project the dependencies included in the default `package.json`.

>You can modify how the Express project is set up - such as which template engine to use or which CSS preprocessor to require - with command line parameters. You can get a list of all command line arguments by running `express --help`. By default, Express will use the **Jade** templating engine.

You can start the project right away using either `npm start` or `nodemon`.


## package.json ##

Rather than explain in detail here, I would recommend using [this excellent interactive guide](http://browsenpm.org/package.json) for exploring various important properties of the `package.json` packaging format for node.js applications.

## External Middleware ##

The Express core is minimal, yet the team behind it provides various predefined middleware to handle common web development features. These types of middleware vary in size and functionality and extend Express to provide a better framework support. The popular Express middleware are as follows:

>Migrating from Express 3 to Express 4? [This guide](http://expressjs.com/guide/migrating-4.html) should help.

- [`morgan`](https://github.com/expressjs/morgan): This is an HTTP request logger middleware.

- [`body-parser`](https://github.com/expressjs/body-parser): This is a body-parsing middleware that is used to parse the request body, and it supports various request types.

- [`multer`](https://github.com/expressjs/multer): Middleware for handling `multipart/form-data` written on top of [`busboy`](https://github.com/mscdex/busboy).

- [`method-override`](https://github.com/expressjs/method-override): This is a middleware that provides HTTP verb support such as PUT or DELETE in places where the client doesn't support it.

- [`compression`](https://github.com/expressjs/compression): This is a compression middleware that is used to compress the response data using gzip/deflate.

- [`serve-static`](https://github.com/expressjs/serve-static): This middleware used to serve static files.

- [`cookie-parser`](https://github.com/expressjs/cookie-parser): This is a cookie-parsing middleware that populates the req.cookies object.

- [`express-session`](https://github.com/expressjs/session): This is a session middleware used to support persistent sessions.

There are many more types of Express middleware that enable you to shorten your development time, and even a larger number of third-party middleware.

>To learn more about the Connect and Express middleware, visit the Connect module's official repository page at https://github.com/senchalabs/connect#middleware. If you'd like to browse the third-party middleware collection, visit Connect's wiki page at https://github.com/senchalabs/connect/wiki.

## Jade Template Engine ##

[Jade](http://jade-lang.com/) is a terse language for writing HTML templates that supports dynamic code and reusability. To learn about the features of this language, explore the [language reference here](http://jade-lang.com/reference/), or see [the API documentation](http://jade-lang.com/api/) for details on how to render Jade using the JavaScript API. While Jade can also be used [installed globally and run from the command line](http://jade-lang.com/command-line/), this is not a use case that we will use. 

## Express Basics ##

Express presents three major objects that you'll frequently use. The **application** object is the instance of an Express application you created in the first example and is usually used to configure your application. The **request** object is a wrapper of Node's HTTP request object and is used to extract information about the currently handled HTTP request. The **response** object is a wrapper of Node's HTTP response object and is used to set the response data and headers.

### The application object ###

The application object contains the following methods to help you configure your application:

- `app.set(name, value)`: This is used to set environment variables that Express will use in its configuration.

- `app.get(name)`: This is used to get environment variables that Express is using in its configuration.

- `app.engine(ext, callback)`: This is used to define a given template engine to render certain file types, for example, you can tell the EJS template engine to use HTML files as templates like this: `app.engine('html', require('ejs').renderFile)`.

- `app.locals`: This is used to send application-level variables to all rendered templates.

- `app.use([path], callback)`: This is used to create an Express middleware to handle HTTP requests sent to the server. Optionally, you'll be able to mount middleware to respond to certain paths.

- `app.VERB(path, [callback...], callback)`: This is used to define one or more middleware functions to respond to HTTP requests made to a certain path in conjunction with the HTTP verb declared. For instance, when you want to respond to requests that are using the `GET` verb, then you can just assign the middleware using the `app.get()` method. For `POST` requests you'll use `app.post()`, and so on.

- `app.route(path).VERB([callback...], callback)`: This is used to define one or more middleware functions to respond to HTTP requests made to a certain unified path in conjunction with multiple HTTP verbs. For instance, when you want to respond to requests that are using the `GET` and `POST` verbs, you can just assign the appropriate middleware functions using `app.route(path).get(callback).post(callback)`.

- `app.param([name], callback)`: This is used to attach a certain functionality to any request made to a path that includes a certain routing parameter. For instance, you can map logic to any request that includes the userId parameter using `app.param('userId', callback)`.

There are many more application methods and properties you can use, but using these common basic methods enables developers to extend Express in whatever way they find reasonable.

### The request object ###

The request object also provides a handful of helping methods that contain the information you need about the current HTTP request. The key properties and methods of the request object are as follows:

- `req.query`: This is an object containing the parsed query-string parameters.

- `req.params`: This is an object containing the parsed routing parameters.

- `req.body`: This is an object used to retrieve the parsed request body. This property is included in the `bodyParser()` middleware.

- `req.param(name)`: This is used to retrieve a value of a request parameter. Note that the parameter can be a query-string parameter, a routing parameter, or a property from a JSON request body.

- `req.path`, `req.host`, and `req.ip`: These are used to retrieve the current request path, host name, and remote IP.

- `req.cookies`: This is used in conjunction with the `cookieParser()` middleware to retrieve the cookies sent by the user-agent.

The request object contains many more methods and properties, but these methods are what you'll usually use in a common web application.

### The response object ###

The response object is frequently used when developing an Express application because any request sent to the server will be handled and responded using the response object methods. It has several key methods, which are as follows:

- `res.status(code)`: This is used to set the response HTTP status code.

- `res.set(field, [value])`: This is used to set the response HTTP header.

- `res.cookie(name, value, [options])`: This is used to set a response cookie. The options argument is used to pass an object defining common cookie configuration, such as the `maxAge` property.

- `res.redirect([status], url)`: This is used to redirect the request to a given URL. Note that you can add an HTTP status code to the response. When not passing a status code, it will be defaulted to 302 Found.

- `res.send([body|status], [body])`: This is used for non-streaming responses. This method does a lot of background work, such as setting the Content-Type and `Content-Length` headers, and responding with the proper cache headers.

- `res.json([status|body], [body])`: This is identical to the `res.send()` method when sending an object or array. Most of the times, it is used as syntactic sugar, but sometimes you may need to use it to force a JSON response to non-objects, such as null or undefined.

- `res.render(view, [locals], callback)`: This is used to render a view and send an HTML response.

The response object also contains many more methods and properties to handle different response scenarios.

## Implementing MVC ##

The Express framework is pattern agnostic, which means it doesn't support any predefined syntax or structure as do some other web frameworks. Applying the MVC pattern to your Express application means that you can create specific folders where you place your JavaScript files in a certain logical order.

To simplify this discussion, it would be reasonable to divide it into two major approaches: a horizontal structure for smaller projects and a vertical structure for feature-rich applications.

### Horizontal Structure ###

A horizontal project structure is based on the division of folders and files by their functional role rather than by the feature they implement, which means that all the application files are placed inside a main application folder that contains an MVC folder structure. This also means that there is a single controllers folder that contains all of the application controllers, a single models folder that contains all of the application models, and so on.

```
- app
	- controllers
	- modesl
	- routes
	- views
- config
	- env
	  config.js
	  express.js
- public
	- config
	- controllers
	- css
	- directives
	- filters
	- img
	- services
	- views
	  application.js
  server.js
  package.json
```

Let's review the folder structure:

The `app` folder is where you keep your Express application logic and is divided into the following folders that represent a separation of functionality to comply with the MVC pattern:
- The `controllers` folder is where you keep your Express application controllers
- The `models` folder is where you keep your Express application models
- The `routes` folder is where you keep your Express application routing middleware
- The `views` folder is where you keep your Express application views

The `config` folder is where you keep your Express application configuration files. In time you'll add more modules to your application and each module will be configured in a dedicated JavaScript file, which is placed inside this folder. Currently, it contains several files and folders, which are as follows:
- The `env` folder is where you'll keep your Express application environment configuration files
- The `config.js` file is where you'll configure your Express application
- The `express.js` file is where you'll initialize your Express application

The `public` folder is where you keep your static client-side files and is divided into the following folders that represent a separation of functionality to comply with the MVC pattern:
- The `config` folder is where you keep your AngularJS application configuration files
- The `controllers` folder is where you keep your AngularJS application controllers
- The `css` folder is where you keep your CSS files
- The `directives` folder is where you keep your AngularJS application directives
- The `filters` folder is where you keep your AngularJS application filters
- The `img` folder is where you keep your image files
- The `views` folder is where you keep your AngularJS application views
- The `application.js` file is where you initialize your AngularJS application

The `package.json` file is the metadata file that helps you to organize your application dependencies.

The `server.js` file is the main file of your Node.js application, and it will load the express.js file as a module to bootstrap your Express application.

As you can see, the horizontal folder structure is very useful for small projects where the number of features is limited, and so files can be conveniently placed inside folders that represent their general roles. Nevertheless, to handle large projects, where you'll have many files that handle certain features, it might be too simplistic. In that case, each folder could be overloaded with too many files, and you'll get lost in the chaos.

### Vertical Structure ###

A vertical project structure is based on the division of folders and files by the feature they implement, which means each feature has its own autonomous folder that contains an MVC folder structure.

```
- core
	- client
		- config
		- controllers
		- css
		- directives
		- filters
		- img
		- services
		- views
		  client.application.js
	- server
		- config
		- controllers
		- models
		- routes
		- views
- feature
	- client
		- config
		- controllers
		- css
		- directives
		- filters
		- img
		- services
		- views
		  feature.client.module.js
	- server
		- config
			- env
			  feature.server.config.js
		- controllers
		- models
		- routes
		- views
	  server.js
	  package.json
```

As you can see, each feature has its own application-like folder structure. In this example, we have the core feature folder that contains the main application files and the feature folder that include the feature's files. An example feature would be a user management feature that includes the authentication and authorization logic. To understand this better, let's review a single feature's folder structure:

The `server` folder is where you keep your feature's server logic and is divided into the following folders that represent a separation of functionality to comply with the MVC pattern:
- The `controllers` folder is where you keep your feature's Express controllers
- The `models` folder is where you keep your feature's Express models
- The `routes` folder is where you keep your feature's Express routing middleware
- The `views` folder is where you keep your feature's Express views
- The `config` folder is where you keep your feature's server configuration files
	- The `env` folder is where you keep your feature's environment server configuration files
	- The `feature.server.config.js` file is where you configure your feature

The `client` folder is where you keep your feature client-side files and is divided into the following folders that represent a separation of functionality to comply with the MVC pattern:
- The `config` folder is where you keep your feature's AngularJS configuration files
- The `controllers` folder is where you keep your feature's AngularJS controllers
- The `css` folder is where you keep your feature's CSS files
- The `directives` folder is where you keep your feature's AngularJS directives
- The `filters` folder is where you keep your feature's AngularJS filters
- The `img` folder is where you keep your feature's image files
- The `views` folder is where you keep your feature's AngularJS views
- The `feature.client.module.js` file is where you initialize your feature's AngularJS module

As you can see, the vertical folder structure is very useful for large projects where the number of features is unlimited and each feature includes a substantial amount of files. It will allow large teams to work together and maintain each feature separately, and it can also be useful to share features between different applications.

### File Naming Conventions ###

Because an application's feature is usually implemented using several JavaScript files, each having a different role, several files might end up having the same name, but be located in different folders. To solve this issue, it is recommended to extend file names with with their functional role (model, view, controller, route) and execution destination.

**E.g.**

- `feature.controller.server.js`
- `feature.controller.client.js`

### Request Routing ###

Express supports the routing of requests using either the `app.route(path).VERB(callback)` method or the `app.VERB(path, callback)` method, where `VERB` should be replaced with a lowercase HTTP verb. Express also enables you to define a single route and then chain several middleware to handle different HTTP requests.

```javascript
app.get('/', function(req, res)
{
	res.send('This is a GET request');
});

app.post('/', function(req, res)
{
	res.send('This is a POST request');
});

app.route('/')
.get(function(req, res)
{
	res.send('This is a GET request');
})
.post(function(req, res)
{
	res.send('This is a POST request');
});
```

Another cool feature of Express is the ability to chain several middleware in a single routing definition. This means middleware functions will be called in order, passing them to the next middleware so you could determine how to proceed with middleware execution. This is usually used to validate requests before executing the response logic.

```javascript
var hasName = function(req, res, next)
{
	if (req.param('name'))
	{
		next();
	}
	else
	{
		res.send('What is your name?');
	}
};

var sayHello = function(req, res, next)
{
	res.send('Hello ' + req.param('name'));
};

app.get('/', hasName, sayHello);
```

https://nodejs.org/docs/latest/api/modules.html#modules_module_exports