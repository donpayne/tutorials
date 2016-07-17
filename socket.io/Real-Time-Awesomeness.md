![](https://github.com/donpayne/tutorials/raw/master/socket.io/img/post_05.png)

# Real Time APIs with Node.js, MongoDB, and Socket.io
So I had a thought, "Can't I implement Two-Way Binding on my server like I can on my client".  

Oh Yeah!  In the client I would use Two-Way Binding (in an MVC paradigm) to ensure that any change made in a view would persist to the model, handled by controllers, and sent back to the view to update any other elements that may need to react to those changes.

Well, we can also do this on our Node.js server to keep changes made by one client bound to all other connected clients.  To do this we need to ensure that we are first persisting our changes to the database (in this case MongoDB).  In the interest of ensuring that any change to a collection's documents are handled in the same manner, we will attach an event emitter to the database event (in this case, post change) and emit the change out to all of our connected clients via Socket.io.

This is my implementation of server-side Two-Way Binding.

## Initializing Socket.io Connections
Load Event Emitters from the MongoDB models and pass in the io listener and socket.
```javascript
// Server
var server = http.createServer(app).listen(app.get('port'), function ()
{
	console.log("Express server listening on port " + app.get('port'));
});

// Socket.io
var io = require('socket.io').listen(server);

// Socket.io Connection
io.sockets.on('connection', function (socket) 
{
	// Load event emitters from each connected socket into the DB models
	require('./app/models/products').loadEventEmitter(io, socket);
	require('./app/models/tests').loadEventEmitter(io, socket);
	require('./app/models/results').loadEventEmitter(io, socket);
});
```

## Create Event Emitters on the MongoDB model
Setup a post event triggered on the save action on the schema.  Anytime this collection has a document saved (explicitly using the save method) it will trigger this event that will in turn emit the document out to all connected sockets.
```javascript
// Product Model
var	mongoose = require('mongoose'),
	Schema   = mongoose.Schema;

var schema = new Schema(
{
	id	: { type: Number },
	type	: { type: String },
	project	: { type: String },
	summary	: { type: String }
},
{ collection: 'products' });

// Expose the Event Emitter
module.exports.loadEventEmitter = function (io, socket)
{
	// The POST event processes after the change has been commited to the DB
	schema.post('save', function (doc)
	{
		// Emits to all connected sockets except the one that initiated the event
		socket.emit('updated-product', doc);
	});
};

// Expose the model as a module interface
mongoose.model('Product', schema, 'products');
```

## Listen for the Socket Events in the Client
Since we are emitting the 'updated-product' event with the document that just got updated, we had better create a listener on our client to deal with it.
```javascript
$(function ()
{
	// Client Side Socket.io
	var socket = io.connect();

	socket.on('updated-product', function (doc)
	{
		// Update your Models
		// Process your Controllers
		// Render your Views
	});
});
```