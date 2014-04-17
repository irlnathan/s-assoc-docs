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
The term model is one of those over-loaded words that can cause a bunch of confusion.  Therefore, I want to provide some context for how I'm using the term `model` in this discussion.  For my purposes a `model` is a group of attributes that describes some thing.  For example, I want to create a User `model` that consists of a single attribute:  **name**. 

<table>
<tr>
  <th align="center">User model</th>
</tr>
<tr>
  <td align="center">name</td>
</tr>
</table>

In sails, I can create a **User** `model` as a javascript object:

```javascript
attributes: {
  name: {
    type: 'string'  }}
```

The model is contained in a file named `User.js` and resides in `\sailsPeople\api\models\`. When sails starts using `sails lift`, sails uses the model as a template to describe Users, but I'm getting ahead of myself.

> **Note:** I can generate an initial model from the command-line using `sails generate api user`.  This will create a file in the `sailsPeople/api/models` folder under `User.js`.

I'm also going to do something similar with my second `model` called **Lead**.  A Lead within my app, is a person or entity that I'm interested in selling a particular product or service.  From the command line I'll enter:

 `sails generate api lead` 
 
 I now have two models configured:

```javascript
sailsPeople
       |_api
           |_models
                 |_User.js
                 |_Lead.js
```

<table>
<tr>
  <th align="center">User model</th>
</tr>
<tr>
  <td align="center">name</td>
</tr>
</table>

```javascript
attributes: {
	name: {
		type: 'string'	}}
```

<table>
<tr>
  <th align="center">Lead model</th>
</tr>
<tr>
  <td align="center">name</td>
</tr>
</table>

```javascript
attributes: {
	name: {
		type: 'string'	}}
```

##Model, Model Instance, and Collections
Throughout this discussion I'm going to differentiate between a model, a model instance and a  collection.  Whereas a model is a description of a thing, a model instance is an example of one of those things.  So with my User `model` I can create an instance of a User from a variety of sources in sails: 

- the sails console
- via sails blueprint shortcut routes
- via sails blueprint rest routes
- via a life cycle callback in a model
- via a custom controller

I'm going to use most of these methods through-out this discussion.  I'll first use the sails `blueprint shortcut routes` to create a couple of Users and a couple of Leads.  If you'd like a more detailed explanation of `blueprint shortcut routes` check out: [How do blueprint routes work in sails?](http://irlnathan.github.io/sailscasts/blog/2014/01/17/sailscasts-answers-ep8-how-do-blueprint-actions-and-blueprint-routes-work-in-sails/) 

I'll start this project using:

 `~> sails lift`
 
I'll then open a browser using the url: `localhost:1337`

Next, I'll create my first User by entering the following request in the browser: 

`localhost:1337/user/create?name=Nikola Tesla`.  

**What's happening here?**  

- I'm making a GET request to the path `locahost:1337/user/create`
- That request matches up to a sails blueprint shortcut route
- The shortcut route triggers a blueprint create action connected to a user controller
- Which triggers a create method
- Which uses the url params `name=Nikola Tesla` for the name property in the User model
- to create an instance of the User model. 

>**Note:** sails automatically creates `id`, `createdAt`, and `updatedAt` attributes without having to explicitly define them in the model.

Sails returns the following json:

```javascript
{
name: "Nikola Tesla",
createdAt: "2014-04-14T17:18:49.439Z",
updatedAt: "2014-04-14T17:18:49.439Z",
id: 1
}
```

>**Note:** in this case sails returns json, not a view.

I'm going to create an additional User using the same shortcut route with the name `Neal Stephenson`

So now I have two instances of the User `model`:

<table>
<tr>
  <th align="center" colspan="2">Lead model</th>
</tr>
<tr>
  <th align="center">id</th>
  <th align="center">name</th>
</tr>
<tr>
  <td align="center">1</td>
  <td align="center">'Nikola Tesla'</td>
  </tr>
  <tr>
  <td align="center">2</td>
  <td align="center">'Neal Stephenson'</td>
  </tr>
</table>

I can access a `collection` of User `model` instances via the browser using the following GET request to the path: `localhost:1337/user`.  Sails returns the following json:

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

Next I'll create two instance of the Lead `model` with the names "Thomas Edison" and 
"Hero Protagonist" using the blueprint shortcut routes.  Like the sails `model` I now have two instances of the Lead `model`:

<table>
<tr>
  <th align="center" colspan="2">Lead model</th>
</tr>
<tr>
  <th align="center">id</th>
  <th align="center">name</th>
</tr>
<tr>
  <td align="center">1</td>
  <td align="center">'Thomas Edison'</td>
  </tr>
  <tr>
  <td align="center">2</td>
  <td align="center">'Hero Protagonist'</td>
  </tr>
</table>

I can access a `collection` of Lead `model` instances via the browser using the following GET request to the path: `localhost:1337/lead`.  Sails returns the following json:


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

##Configuring a <u>_one-way association_</u> between User and Lead.

Let's say I want to be able to find, create, update and delete the User who has a particular Lead.  To accomplish this I could configure the User `model` to look like this:
 
```javascript
attributes: {
	name: {
		type: 'string'	},
	lead: {
		model: 'user'	}
}
```

Here, I've added a `Lead` parameter to the User `model` attributes.  This Lead parameter sets-up a one-way relationship between the User and a particular `Lead model instance` via a foreign key.

<img src="http://i.imgur.com/4ZDSy3F.jpg" />

It really helps to see this visually so I'll set that up in a table:

<img src="http://i.imgur.com/R50DQM0.jpg" />

> Don't confuse the above diagram with a schema database.  Sails supports both schema and schema-less databases and this diagram references the model relationship and not where or how the model instances are persisted (stored).

So after the association I can deduce that **Nikola Tesla** has a Lead that points to **Thomas Edison**. Therefore, each `User model instance` will be capable of associating a single `Lead model instance`.  This relationship can also be considered a one way relationship.

So now that I have this association between the Lead and User `models`, **_what can I do with it?_**

##Associating models in a one-way relationship

For this example, I'm going to use the sails console to make the association.  

>**Note:** I can use the same code I use in the sails console in a custom controller action.

I'll open the sails console using `sails console`.  From the command prompt I'll enter:

```javascript
sails> User.update({id: 1}, {lead: 1}).exec(function(err, user){console.log(user);});}
```

That's hard to read so let's look at the same code formatted in a more readable style:

```javascript
User.update({id: 1}, {lead: 1})
  .exec(function(err, user){
    console.log(user);
  });
}
```

Here, I'm updating the **Lead** attribute of the first instance of the **User* model whose `id:` is 1, better known as Nikola Tesla, with a value of 1, better known as Thomas Edison.  This leaves me with:

User model|||
| id  | name| lead |
|:--:   |:--:       | :--:|
| 1  | 'Nikola Tesla'  | 1|
| 2  | 'Neal Stephenson'  | | 

Lead model||
| id  | name|
|:--:   |:--:       |
| 1  | 'Thomas Edison'  | 
| 2  | 'Hero Protagonist'  | 

##Using "populate" to find stuff with a one-way association.

 

