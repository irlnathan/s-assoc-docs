# Introduction to Associations

## Getting ready for the project
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

##What are models?
The term [**model**]() is one of those over-loaded words that can cause a bunch of confusion.  Therefore, I want to provide some context for how I'm using the term "model" in this discussion.  For my purposes, a **model** is a representation of a set of structured data, usually corresponding with a table in a SQL database or a collection in databases like MongoDB, Redis, or Riak.

##When do I use models?
Models are useful any time I want to store, retrieve, update, and/or delete data in my app.  For instance, my app might need to save the data entered into a web form to a model.  Or I might find some [records]() stored in one of my models in order to display a dynamic list of orders or receipts.


### Defining models

A model is defined by group of [attributes]() that describe a particular _thing_ or _entity_.

For example, I want to create a `User` model that consists of a single attribute:  `name`.

<table>
<tr>
  <th align="center">User model</th>
</tr>
<tr>
  <td align="center">name</td>
</tr>
</table>

In Sails, I can define a **User** model as a javascript object:

```javascript
{
  attributes: {
    name: {
      type: 'string'
    }
  }
}
```

The model is contained in a file named `User.js` and resides in the `sailsPeople/api/models/` folder. When I start my app using `sails lift`, Sails will connect to the database where my user data is stored and provide an interace for me to interact with it.  By default, Sails stores data in a temporary file on my hard disk, but I can change this to MySQL, PostgreSQL, MongoDB, Redis, or CouchDB at any time down the road.

I'm going to do something similar with my second model called **Lead**.  In my app, a Lead is a person or entity that I'm interested in selling a particular product or service. 

This time, however, I'll cheat a little bit.  The Sails command-line tool ships with a few simple [generators]() for quickly generating common code files.  This keeps me from having to manually create new files and put together the boilerplate code every time I want to add something new to my app.

To define my **Lead** model, I'll open the command line and enter:

 ```sh
 $ sails generate api lead
 ```
 
When I open `sailsPeople/api/models/Lead.js`, an empty model has been created for me.  All I have to do is add a **name** attribute.

At this point, I have defined and configured two models:

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
		type: 'string'
	}
}
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
		type: 'string'
	}
}
```

## Working with Data

Throughout this discussion I'm going to differentiate between a **model**, a **record** (or _model instance_) and a collection.  Whereas a model is a _definition_ of a thing (e.g. **User**), a record is an _instance_ of one of those things (e.g. `{name: "robert", id: 4}`). 

So with my User model, I can create new records in any number of different places in Sails:

- from the [`sails console`]()
- by hitting one of my [blueprint routes]()
- from a [lifecycle callback]() defined in the User model itself
- from a custom action in a [controller]()

I'll use a few different methods from the list above to work with data throughout this discussion, starting with **blueprint routes**.  If you'd like a more detailed explanation of blueprint routing, particularly the **shortcut blueprint routes** below, check out: [How do blueprint routes work in sails?](http://irlnathan.github.io/sailscasts/blog/2014/01/17/sailscasts-answers-ep8-how-do-blueprint-actions-and-blueprint-routes-work-in-sails/) 

### Creating some users
First, I want to create a couple of Users and a couple of Leads.  I'll lift my app using `sails lift`.

I'll create my first User by opening my browser and entering the following URL: [`http://localhost:1337/user/create?name=Nikola Tesla`]()

**What's happening here?**  

- My browser is sending a GET request to my running Sails app at [localhost:1337]()
- That request matches up to a **shortcut blueprint** route, [/user/create]()
- The shortcut blueprint triggers an action, `create()` defined in `api/controllers/UserController.js`.
- I haven't defined a custom `create` action, so the implicit [**blueprint action**]() will be run in its place.
- This inspects the [parameters]() I passed in (`?name=Nikola Tesla`) and passes them to [`User.create()`]().
- This creates a record in my User model for a new user named "Nikola Tesla". 

Sails returns the following json:

```javascript
{
name: "Nikola Tesla",
createdAt: "2014-04-14T17:18:49.439Z",
updatedAt: "2014-04-14T17:18:49.439Z",
id: 1
}
```

>**Note:**
> 
> By default, Sails adds `id`, `createdAt`, and `updatedAt` attributes without me having to explicitly define them in the model.  This behavior [is configurable]().


Next, I'm going to create an additional User with the name `Neal Stephenson`.  I'll visit  exactly the same URL as before, but change out the `name` parameter: [`http://localhost:1337/user/create?name=Neal Stephenson`]()

So now I have two records stored in my User model:

<table>
<tr>
  <th align="center" colspan="2">User (records)</th>
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


### Finding users

So now that I have some users stored, let's talk about how to retrieve them.


Using the blueprint API, I can access my User records without writing any code.  I simply open up my browser again and visit: [`http://localhost:1337/user`]()
(remember: this means my browser is sending a GET request to Sails at the `/user` path)

Sails returns the following json:

```json
[  {
  "name": "Nikola Tesla",
  "createdAt": "2014-04-14T17:18:49.439Z",
  "updatedAt": "2014-04-14T17:18:49.439Z",
  "id": 1
},
{
  "name": "Neal Stephenson",
  "createdAt": "2014-04-14T20:47:01.703Z",
  "updatedAt": "2014-04-14T20:47:01.703Z",
  "id": 2
}  ]
```


### Creating some leads

Next I'll create two Leads with the names "Thomas Edison" and "Hero Protagonist" using the `/lead/create` shortcut blueprint route.  Now I have two leads:

<table>
<tr>
  <th align="center" colspan="2">Lead (records)</th>
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

### Finding leads

Accessing stored leads from the browser works pretty much like accessing stored users. I just send a GET request to [`http://localhost:1337/lead`] and Sails returns the following json:


```json
[ {
  "name": "Thomas Edison",
  "createdAt": "2014-04-14T20:47:25.571Z",
  "updatedAt": "2014-04-14T20:47:25.571Z",
  "id": 1
},
{
  "name": "Hero Protagonist",
  "createdAt": "2014-04-14T20:48:07.190Z",
  "updatedAt": "2014-04-14T20:48:07.190Z",
  "id": 2
} ]
```

With our models defined, attributes configured, and some initial data created, it's time to address associations.

##Why do we use associations?
**Associations** provide a way to relate models so that finding, creating, updating, and destroying their records requires less programming.

###Configuring a <u>_one-way association_</u> between User and Lead

Let's say I want to be able to find a particular User, and also the Lead she is associated with.  I could manage the relationship manually: i.e. find the User, find the Lead, then format the result. However, it would be a lot nicer to look up both a user and its associated lead in one action.

To accomplish this, I'll configure the User model with a new attribute called `currentLead`-- but this time, instead of specifying a primitive **type** like "string" or "integer", I'll configure the `currentLead` attribute as an association:
 
```javascript
// User
attributes: {
	name: {
		type: 'string'
	},
	currentLead: {
		model: 'Lead'
	}
}
```

The `currentLead` attribute represents a one-way relationship between the User model and the Lead model.  What this means is that I can now associate a User's (e.g. Nikola's) `currentLead` with one of my leads (e.g. Thomas.)



### How does this work?
Sails abstracts away a lot of the complexity of associations for us, and it supports both "schema-ful" and "schema-less" databases. The underlying implementation of associations is slightly different in each case, but since it can be helpful to examine what's going on under the covers, I'll provide a quick overview using terminology from the classic relational model.

Sails implements one-way relationships using something called a _foreign key_.   Whenever a `currentLead` is set on a user record, a hidden _foreign key_ is created.  A record's foreign key is simply the _primary key_ value of the _**other**_ record in the relationship.  A _primary key_ is the one particular attribute in a model which is always guaranteed to be unique for every record.  By default, Sails sets this up for me as `id`.  

So for example, if a User record has a `currentLead`, it implicitly maps to the `id` of the associated Lead (for instance, `1`).

<img src="http://i.imgur.com/4ZDSy3F.jpg" />

It really helps to see this in action, so I'll set up an example using a table to visualize the records stored in my User and Lead models before and after:

<img src="http://i.imgur.com/R50DQM0.jpg" />

> Note: This _logical_ diagram reflects the the model relationship and not where or how the model instances are persisted (stored).

After creating this association, I can deduce that **Nikola Tesla** has a `currentLead` attribute that points to **Thomas Edison**. As you might expect, the `currentLead` attribute in each User record is now capable of referring to a particular Lead record.  It's important to point out that `currentLead` doesn't necessarily have to be set-- a user might not have a lead!  Also, multiple users can have the same `currentLead`-- so Nikola and Neal might both have Thomas Edison as their `currentLead`.  That's why this type of association is referred to as a **one-way relationship**.

So! Now that I have this association between the Lead and User `models`, **_what can I do with it?_**


## Associating models in a one-way relationship

For this example, I'm going to use the sails console to make an association between a particular User and a particular Lead.  

>**Note:** I can use the same code I use to communicate with my models in the `sails console` in a custom controller action.

First I'll open the [Sails console]() by running the follwing command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.update({id: 1}).set({lead: 1}).exec(function(err, user){console.log(user);});}
```

That's hard to read so let's look at the same code formatted in a more readable style:

```javascript
User.update({id: 1})
  .set({lead: 1})
  .exec(function(err, user){
    console.log(user);
  });
}
```

<!-- the following paragraph is really nice- it's an excellent way to do examples: ~mike -->

Here, I'm updating the **currentLead** of the first user instance (the record where `id:` is 1).  This particular user is better known as Nikola Tesla, and his **currentLead** has a value of 1, referring to the Lead known as Thomas Edison.  This leaves me with:

<table>
<tr>
  <th align="center" colspan="3">User</th>
</tr>
<tr>
  <th align="center">id</th>
  <th align="center">name</th>
  <th align="center">lead</th>
</tr>
<tr>
  <td align="center">1</td>
  <td align="center">'Nikola Tesla'</td>
  <td align="center">1</td>
  </tr>
  <tr>
  <td align="center">2</td>
  <td align="center">'Neal Stephenson'</td>
   <td align="center"></td>
  </tr>
</table>

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

So now that I've created the association, what can I do with it?

##Using "populate" to find stuff with a one-way association.

<img src=http://i.imgur.com/G0JExWe.jpg />

There are several methods I'll be using to **populate** (i.e. look up), **add**, and **remove** associated records.  Here is a general description of each method:



<!--
example of how to do this with md tables
(I think it's harder to read honestly- like yours better, but I'm just leaving it here as a note)  Only issue is that when you use HTML tables, you can't use markdown syntax for bolding, code snippets, etc.  Tough call


~mike
#### Association Methods

|  method  | description |
|-----------------------------------|
|    `User.find().populate()` |  todo |
|    `nikola.add()     | todo |
|    `nikola.remove()`     |   todo |
-->


<table>
<tr>
  <th align="center" colspan="2">Association Methods</th>
</tr>
<tr>
  <th align="center">method</th>
  <th align="center">description</th>
</tr>
 <tr>
  <td align="center">`User.populate()`</td>
  <td align="center">todo</td>
 </tr>
<tr>
  <td align="center">`nikola.add()`</td>
  <td align="center">todo</td>
 </tr>
 <tr>
  <td align="center">`nikola.remove()`</td>
  <td align="center">todo</td>
 </tr>
</table>




<!-- I got to about here and had to stop ~mike -->

The first of these methods is `.populate` which I can use to have sails return all instances of an association.  Given this example:

```javascript
User.findOne(1).populate('lead')
  .exec(function(err, user){ console.log(user); 	
});
```

I first find 'Nikola Tesla' using the `.findOne` method.  I then chain `.populate` to look up the `lead`, in this case 'Thomas Edison'. Sails returns the following json:

```javascript
{
  lead: {
    name: "Thomas Edison",
    id: 1,
    createdAt: "2014-04-14T21:31:17.000Z",
    updatedAt: "2014-04-14T21:31:17.000Z"
  },
  name: "Nikola Tesla",
  id: 1,
  createdAt: "2014-04-14T21:31:04.000Z",
  updatedAt: "2014-04-14T21:31:40.000Z"
}
```
 
**Let's say I want to create a new lead with an existing user?** For example, I want to associate the existing user 'Neal Stephenson' with a new lead named 'Aimee Mann'.

<img src=http://i.imgur.com/JDohTeu.jpg />

So here I'm going to create the lead an connect it to an existing user, however, the formatting is not exactly easy to read.

```javascript
sails> Lead.create({name: 'Aimee Mann'}).exec(function(err, lead) {User.findOne(2).exec(function(err, user) { user.lead = lead.id; user.save(function(err){}); }); });
```

Here is the same code but in a more readable format:

```javascript
sails> Lead.create({name: 'Aimee Mann'})
  .exec(function(err, lead) {
    User.findOne(2).exec(function(err, user) { 
      user.lead = lead.id;
  	  user.save(function(err){});
    });
  });
```

**What's happening here...**

1. First I create the lead using the `.create` method and passing in one attribute object `{name: 'Aimee Mann'}`.
2. I find the user I want to assocate with the lead via the `.findOne` method and passing in the `id:` of that user.
3. Next, I asign the `lead` parameter of the returned user with the returned lead `id:` from step one using `user.lead = lead.id;`
4. Finally I save the user instance by using the `.save` method.

What about if I want to create a new user with an existing lead? For example. I want to associate an existing lead 'Hero Protagonist' with a new user I'm about to create named 'Boris Karloff'.

<img src=http://i.imgur.com/BrrQ4BR.jpg />

I could use the same strategy I used in the previous example, however, because the user "knows" about the lead I can use a different less verbose approach.

Again here is how I did it in the sails console, however, the formatting is not great for reading.

```javascript
sails> User.create({name: 'Boris Karloff', lead: 2}).exec(function(err, user { console.log(user);  });
```

So here's the same code but in a more readable format:

```javascript
sails> User.create({name: 'Boris Karloff', lead: 2})
  .exec(function(err, user {
    console.log(user);
  });
```

**What's happening here...**

I'm creating the user, passing in the `name` of the user, but also passing the `id:` of the lead into the lead attribute as `User.create({name: 'Boris Karloff', lead: 2})`.