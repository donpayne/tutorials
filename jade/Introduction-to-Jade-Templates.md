![](https://github.com/donpayne/tutorials/raw/master/jade/img/post_03.png)

# What is a Template Engine?
First let's explain the MVC pattern.  MVC stands for Model (data), View (presentation), Controller (logic).  The idea is to create a separation of concerns.  A template engine focuses on abstracting the responsibility of presenting the view that the user will interact with.  It is very important to strictly separate the presentation from the logic!  

The template engine merely provides the developer a method to cleanly present data from the model so that the code used to manipulate the DOM does not get intermingled with model data or business logic.  This makes it much easier to maintain complex business logic and model data because the code is more focused. Another benefit is that templates are reusable.  Reusing code reduces redundancy and the errors associated with code that that is copied  or not maintained in all of its instances.

A few recommendations to get the most out of Jade
- Create a jade layout to handle all markup that every page will consume.
- Create jade files for each route that will inherit the layout markup.
- Create jade fragments that can be included as needed (i.e., headers, footers, etc)
- Create client-side jade fragments to render changes to the DOM
- Pass data to the template that is clean and ready to present
- NEVER EDIT THE DOM DIRECTLY, NO MATTER HOW SMALL THE CHANGE!!!

## Installing Jade
Install Jade in your project directory via NPM.
```
npm install jade --save
```

## Configuring your Server
In your main app.js file require the jade module and set it as your default template engine.
```javascript
var jade = require('jade');

app.set('view engine', 'jade');
app.set('views', __dirname + '/app/views');
```

## Layout Template
Setup a layout.jade file with all of the boilerplate markup, styles and javascript that will be consumed by all of your pages.
```jade

doctype html
html(lang="en")
	head

		//- Basic Page Needs
		//- ==================================================
		meta(charset="utf-8")
		meta(http-equiv="x-ua-compatible" content="ie=edge")
		meta(http-equiv="Content-Type" content="text/html; charset=UTF-8")
		meta(http-equiv="Cache-control" content="public")
		meta(name="viewport" content="width=device-width, initial-scale=1.0")
		meta(name="description" content="Don Payne - MongoDB Express JQuery Node.js Template.")
		meta(name="keywords" content="don payne, node, express, jade, mongo, jquery, responsive, bootstrap, font awesome, html5, css3, less")
		meta(name="author" content="Don Payne")
		//- The above meta tags *must* come first in the head; any other head content must come *after* these tags

		title= title
		link(rel="shortcut icon" href="/img/favicon-32x32.png")
		link(rel="apple-touch-icon" href="/img/apple-icon-57x57.png")
		link(rel="apple-touch-icon" sizes="72x72" href="/img/apple-icon-72x72.png")
		link(rel="apple-touch-icon" sizes="114x114" href="/img/apple-icon-114x114.png")

		//- CSS
		//- ==================================================
		link(rel="stylesheet" media="all" href="http://fonts.googleapis.com/css?family=Raleway:400,100,200,300,600,700,500")
		link(rel="stylesheet" media="all" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css")
		link(rel="stylesheet" media="all" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap-theme.min.css")
		link(rel="stylesheet" media="all" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css")

		block styles

	body

		include ./includes/header.jade

		block content

		include ./includes/footer.jade	

		//- Javascript
		//- ==================================================
		mixin ie(condition)
			!= "<!--[" + condition + "]> -->"
			block
			!= "<!-- <![endif]-->"

		+ie('if lt IE 9')
			script(src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.3/jquery.min.js")
			script.
				if (typeof jQuery === 'undefined')
				{
					document.open();
					document.write('<script src="/js/bower_components/jquery/dist/jquery-1.11.3.min.js">\x3C/script>');
					document.close();
				}

		+ie('if gte IE 9')
			script(src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js")
			script.
				if (typeof jQuery === 'undefined')
				{
					document.open();
					document.write('<script src="/js/bower_components/jquery/dist/jquery.min.js">\x3C/script>');
					document.close();
				}

		script(src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js")
		script.
			if (typeof $().emulateTransitionEnd !== 'function')
			{
				document.open();
				document.write('<script src="/js/bower_components/bootstrap/dist/js/bootstrap.min.js">\x3C/script>');
				document.close();
			}

		script(src="https://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.8.3/underscore-min.js")
		script.
			if (typeof _ === 'undefined')
			{
				document.open();
				document.write('<script src="/js/bower_components/underscore/underscore-min.js">\x3C/script>');
				document.close();
			}

		//- HTML5 Shim and Respond.js IE8 support of HTML5 elements and media queries
		//- WARNING: Respond.js doesn't work if you view the page via file:
		+ie('if lt IE 9')
			script(src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js")
			script(src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js")
			script(src="https://oss.maxcdn.com/excanvas/r3/excanvas.compiled.js")

		//- Retina Images
		script.
			if ((window.devicePixelRatio === undefined? 1 : window.devicePixelRatio) > 1)
				document.cookie='HTTP_IS_RETINA=1;path=/';
				
		block scripts
```

## Page Specific Template
Now that our layout is complete we only need to add markup, styles and javascript that will be specific to each page on our site.  Notice how little code is needed to augment each page when using a layout.
```jade
extends ./layout.jade

block styles
	link(rel="stylesheet" href="/css/animations.css")
	link(rel="stylesheet" href="/css/carousel.css")
	link(rel="stylesheet" href="/css/theme.css")

block content

	//- Logo
	//- Navigation
	//- Hero
	//- Featured Content

block scripts
	script(src="/_components/cookies.js")
	script(src="/js/jade-runtime.js")
	script(src="/js/jade-templates.js")
	script(src="http://cdnjs.cloudflare.com/ajax/libs/jquery-easing/1.3/jquery.easing.min.js")
	script(src="https://maps.googleapis.com/maps/api/js")
	script(src="/js/page-loader.min.js")
	script(src="/js/carousel.min.js")
	script(src="/js/custom.js")
```

## Client-side Jade Templating
Jade is a powerful JavaScript HTML template engine, which is concise and powerful. Due to its awesome syntax and powerful features, it almost become the default template engine for node.js web servers.

Jade is well known as a server-side HTML template, but actually it can also be used as a client-side template engine, which is barely known by people!

I prefer to dynamically compile all of my client-side templates anytime a change is made to a jade file.  To do this I have created a compile script to do the actual processing.  I am using file system watchers to monitor when files are created or edited.  When this occurs I then process the compile script that will deliver a single javascript file with all of my jade files converted into functions that are then available to the client.
```javascript
// Modules
var jade = require('jade');
var fs   = require('fs');
var q    = require('q');

// Jade Compiling
module.exports = function ()
{
	var folder = './app/views/client';

	var compiler = function ()
	{
		var p = q.defer();
		fs.readdir(folder, function (err, files)
		{
			if (err) throw err;

			var templates = [];
			files.forEach(function (file)
			{
				// Compile the template to a function string
				var template = (file.split('.'))[0];
				templates.push(jade.compileFileClient(folder + '/' + file, { name: template }));
			});

			p.resolve(templates);
		});

		// Maybe you want to compile all of your templates to a templates.js file and serve it to the client
		p.promise.then(function (templates)
		{
			console.log('Compiled Jade Templates');
			fs.writeFileSync('./public/js/jade-templates.js', templates.join('\n\n'));
		});
	};

	// File System Watcher
	fs.readdir(folder, function (err, files)
	{
		if (err) throw err;

		files.forEach(function (file)
		{
			fs.watchFile(folder + '/' + file, function (curr, prev)
			{
				// File has changed
				if (curr !== prev) setTimeout(function () { compiler(); }, 1000);
			});
		});
	});
};
```

## Expose Templates to the Client
Remember to include the jade-runtime.js file in the client-side markup.  This file can be copied from the node-modules folder where jade was installed.  Then also include the output of the jade compiler, jade-templates.js.
```javascript
script(src="/js/jade-runtime.js")
script(src="/js/jade-templates.js")
```