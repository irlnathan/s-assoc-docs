#Getting ready for the project
Create a sails project using:

`sails new sailsPeople`

Sails creates the project using the following folder structure:

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

Now that the project is created I'll move on to models.

#What are models?
The term model is one of those over-loaded words that can cause a bunch of confusion.  Therefore, I want to provide some context for how I'm using the term `model` in this dicussion.  For my purposes a `model` is a group of attributes that describes some thing.  For example, I want to create a user `model` that consists of a single attribute:  **name**. 

user model ||
| name|
|:--:     |
||

In sails, I can create a **user** `model` as a javascript object:

```javascript
attributes: {
  name: {
    type: 'string'  }}
```

The model is contained in a file named `User.js` and resides in `\sailsPeople\api\models\`. When sails starts using `sails lift`, sails uses the model as a template to describe users, but I'm getting ahead of myself.

 **Note:** I can generate an initial model from the command-line using `sails generate api user`.  This will create a file in the `sailsPeople/api/models` folder under `User.js`.

I'm also going to do something similiar with my second `model` called **lead**.  A lead within my app, is a person or entity that I'm intersted in selling a particular product or service.  From the command line I'll enter:

 `sails generate api lead` 
 
 I now have two models configured:

```javascript
sailsPeople
       |_api
           |_models
                 |_User.js
                 |_Lead.js
```

User model ||
| name|
|:--:     |
||

```javascript
attributes: {
	name: {
		type: 'string'	}}
```

Lead model ||
| name|
|:--:     |
||

```javascript
attributes: {
	name: {
		type: 'string'	}}
```

##Model, Model Instance, and Collections
Throughout this discussion I'm going to differentiate between a model, a model instance and a  collection.  Whereas a model is a description of a thing, a model instance is an example of one of those things.  So with my user `model` I can create an instance of a user from a variety of sources in sails: 

- the sails console
- via sails blueprint shortcut routes
- via sails blueprint rest routes
- via a life cycle callback in a model
- via a custom controller

I'm going to use most of these methods through-out this discussion.  I'll first use the sails `blueprint shortcut routes` to create a couple of users and a couple of leads.  If you'd like a more detailed explanation of `blueprint shortcut routes` check out: [How do blueprint routes work in sails?](http://irlnathan.github.io/sailscasts/blog/2014/01/17/sailscasts-answers-ep8-how-do-blueprint-actions-and-blueprint-routes-work-in-sails/) 

I'll start this project using:

 `~> sails lift`
 
I'll then open a browser using the url: `localhost:1337`

Next, I'll create my first user by entering the following request: `localhost:1337/user/create?name=Nikola Tesla`.  **What's happening here?**  

- I'm making a GET request to the path `locahost:1337/user/create`
- That request matches up to a sails blueprint shortcut route
- The shortcut route triggers a blueprint create action connected to a user controller
- Which triggers a create method
- Which uses the url params `name=Nikola Tesla` for the name property in the user model
- to create an instance of the user model. 

**Note:** sails automatically creates `id`, `createdAt`, and `updatedAt` attributes without having to explicitedly define them in the model.

Sails returns the following json:

```javascript
{
name: "Nikola Tesla",
createdAt: "2014-04-14T17:18:49.439Z",
updatedAt: "2014-04-14T17:18:49.439Z",
id: 1
}
```

**Note:** in this case sails returns json, not a view.

I'm going to create an addiitonal user using the same shortcut route with the name `Neal Stephenson`

So now I have two instances of the `user` model:

User model||
| id  | name|
|:--:   |:--:       |
| 1  | 'Nikola Tesla'  | 
| 2  | 'Neal Stephenson'  | 

I can access a `collection` of user `model` instances via the browser using the following GET request to the path: `localhost:1337/user`.  Sails returns the following json:

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

Next I'll create two instance of the lead `model` with the names "Thomas Edison" and 
"Hero Protagonist" using the blueprint shortcut routes.  Like the sails `model` I now have two instances of the lead `model`:

Lead model||
| id  | name|
|:--:   |:--:       |
| 1  | 'Thomas Edison'  | 
| 2  | 'Hero Protagonist'  | 

I can access a `collection` of lead `model` instances via the browser using the following GET request to the path: `localhost:1337/lead`.  Sails returns the following json:


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

#Why do we use associations?
**Associations** provide a way to relate models together so finding, creating, updating, and deleting **instances** of them require less programming.  

##Configuring a _one-way association_ between user and lead.

Let's say I want to be able to find, create, update and delete the user who has a particular lead.  To accomplish this I could coinfigure the user `model` to look like this:
 
```javascript
attributes: {
	name: {
		type: 'string'	},
	lead: {
		model: 'user'	}
}
```

Here, I've added a `lead` parameter to the user `model` attributes.  This lead parameter sets-up a one-way relationship between the user and a particular `lead model instance` via a foreign key.

<img src="http://i.imgur.com/4ZDSy3F.jpg" />

It really helps to see this visually so I'll set that up in a table:

<img src="http://i.imgur.com/R50DQM0.jpg" />

So after the association I can deduce that **Nikola Tesla** has a lead that points to **Thomas Edison**. Therefore, each `user model instance` will be capable of associating a single `lead model instance`.  This relationship can also be considered a one way relationship.

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

	