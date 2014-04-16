#Getting ready for this project
Create a sails project using:

`sails new sailsPeople`

This create a project with the following general folder structure:

```javascript
sailsPeople
       |_api
       |_assets
       |_config
       |_node_modules
       |_tasks
       |_views                
.gitignore
.sailsrc
app.js
Gruntfile.js
package.json
README.md
                
```

#What are models?
The term model is one of those over-loaded words that can cause a bunch of confusion.  Therefore, I want to provide some context for how I'm using the term model in this dicussion.  For my purposes a model is a group of attributes that describes a thing.  For example, a user `model` can consist of a single attribute:  **name**

I'm going to represent this **user** `model` in sails as a `model` object:

```javascript
attributes: {
	name: {
		type: 'string'	}}
```

**Note:** I can generate an initial model from the command-line using `sails generate api user`.  This will create a file in the `api/models` folder under `user.js`.

I'm also going to do something similiar with my second model called `lead`.  A lead within my app, is a person/entity that I want to sell my product or service.  From the command line I'll enter:

 `sails generate api lead` 
 
 I now have two models configured:

```javascript
sailsPeople
       |_api
           |_models
                 |_User.js
                 |_Lead.js
```

Similar to the `user` model, I'm adding the attribute name to my `lead` model:

```javascript
attributes: {
	name: {
		type: 'string'	}}
```

##Model, Model Instance, and Collections
Throughout this discussion I'm going to differentiate between a model, a model instance and a  collection.  Whereas a model is a description of a thing, a model instance is an example of one of those things.  So with my `user` model I can create an instance of that `user` model from the:

- sails console
- via sails blueprint shortcut routes
- via sails blueprint rest routes
- via a custom controller

I'm going to use the sails `blueprint shortcut routes`, so I'll start this project using:

 `~> sails lift`
 
I'll open a browser using the url: `localhost:1337`

Next, I'll create my first user by entering: `localhost:1337/user/create?name=Nikola Tesla`.  I get back the following json:

```javascript
{
name: "Nikola Tesla",
createdAt: "2014-04-14T17:18:49.439Z",
updatedAt: "2014-04-14T17:18:49.439Z",
id: 1
}
```

**Note:** sails automatically creates `id`, `createdAt`, and `updatedAt` attributes.

I'm going to create an addiitonal user using the name `Neal Stephenson`

So now I have two instances of the `user` model and I can access a `collection` of model instances via the browser using: `localhost:1337/user`:

```javascript
[  {
name: "Nikola Tesla",
createdAt: "2014-04-14T17:18:49.439Z",
updatedAt: "2014-04-14T17:18:49.439Z",
id: 1
},
{
name: "Neal Stephenson",
createdAt: "2014-04-14T20:47:01.703Z",
updatedAt: "2014-04-14T20:47:01.703Z",
id: 2
}  ]
```
Next I'll create two instance of the `lead` model with the names "Thomas Edison" and 
"Hero Protagonist".  Here is the `lead` model collection:

```javascript
[ {
name: "Thomas Edison",
createdAt: "2014-04-14T20:47:25.571Z",
updatedAt: "2014-04-14T20:47:25.571Z",
id: 1
},
{
name: "Hero Protagonist",
createdAt: "2014-04-14T20:48:07.190Z",
updatedAt: "2014-04-14T20:48:07.190Z",
id: 2
} ]
```

With our models defined, instances created, and collections defined, it's time to address associations.

#Why do we have associations?
`Associations` provide a way to relate models together so finding, creating, updating, and deleting them require less programming.  

For example, let's say I want to be able to find, create, update and delete the user who has a particular lead.  To accomplish this I could coinfigure the `user` model to look like this:
 
```javascript
attributes: {
	name: {
		type: 'string'	},
	owner: {
		model: 'user'	}
}
```

Here, I've added an `owner` parameter to the `user` model attributes.  This owner parameter "points" to a single  `user model instance`.    Therefore, each `vehicle model instance` will be capable of associating a single `user model instance`.  This relationship can also be considered a one way relationship -- one user/owner per vehicle instance.

So now that I have this association between the `vehicle` and `user` models, **_what can I do with it?_**

Let's say I have an instance of the `user` model like this: 

```javascript
{
	id: '1',
	name: 'John Galt',
	emailAddress: 'john@galtgulch.com',
	encryptedPassword: 'jeidDDJkadsfDDjaoejfjDFjjasdkjfajjfapqeirjasdkfjdfjdf',
	gravatar: '0bc83cb571cd1c50ba6f3e8a78ef1346'}
```

I also have an instance of the `vehicle` model like this:

```javascript
{
	id: '1',
	manufacturer: 'toyota',
	color: 'red',
	type: 'truck',
}
```

So even though I've set-up the configuration of the association between these two models they remain unrelated.  It's easy, however, to relate them.  I just need to add an `owner` parameter to the `vehicle` instance with the id of 1. I can do this a bunch of different ways.  The easiest way during development is to use `blueprint shortcut` routes.  These shortcut routes provide a way to access model methods like find, create, update, destroy, add, and remove.  

So I'm going to start my sails project by using `sails lift` and then open a browser to http://localhost:1337/vehicle.

This will return my single instance of the `vehicle` model.  Just for the heck of it, I'll also take a look at the single instance of my `user` model by opening http://localhost:1337/user.  So I want to update my vehicle instance with `owner=1`.  I can do this by hitting:

http://localhost:1337/vehicle/1/update?owner=1

	