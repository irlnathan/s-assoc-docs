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

##Overview of the project?

I want to create a way of retrieving, storing, and manipulating information in an application I'm going to build called -- _**sailsPeople**_.  Initially, the application will have **Users** (e.g. people that will use the application to track their sales activity) and **Profiles** (e.g. information realating to and describing a **User**).   I need some way of organizing **Users** and **Profiles** in my application and this is usually referred to as a **Data Model**.

##What are models?
The term [**model**]() is one of those over-loaded words that can cause a bunch of confusion.  Therefore, I want to provide some context for how I'm using the term "model" in this project.  For my purposes, a **model** is a representation of a set of structured data, usually corresponding with a table in a SQL database or a collection in databases like MongoDB, Redis, or Riak.

##When do I use models?
Models are useful any time I want to store, retrieve, update, and/or delete data in my app.  For instance, my app might need to save the data entered into a web form to a model.   The distinction between the **model** and the underlying place it's stored is important because with sails once I define the **model** I have a common set of methods to interact with the data.  Therefore, regardless if I'm using an SQL database (e.g. mySQL, PostgreSQL), a schema-less database (e.g. MongoDB, Redis, or Riak) or combination of databases, sails removes the need to address each of the database's unique api.

I can simply configure my **models**, choose an [**Adapter**]() via a [**Connection**](), and rely upon [**Waterline**]() methods to access and manipulate my data.

<img src="http://i.imgur.com/zzZP42z.jpg" />
 
### Defining models

A model is defined by group of [attributes]() that describe a particular _thing_ or _entity_.

For example, I want to create a `User` model that consists of a single attribute:  `name`.

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

The model is contained in a file named `User.js` and resides in the `sailsPeople/api/models/` folder. When I start my app using `sails lift`, Sails will connect to the database where my user data is stored and provide an inteface for me to interact with it.  By default, Sails stores data in a temporary file on my hard disk, but I can change this to MySQL, PostgreSQL, MongoDB, Redis, or CouchDB at any time down the road.

I'm going to do something similar with my second model called **Profile**.  In my app, a **Profile** contains attributes that describe a particular **User**. One might ask, why would I separate information about a user into a separate model?  Mostly I'm doing this to show how I created a one-to-one relationship between the models, however, some of the factors to consider whether to use the same or multiple models for a set of data are:

* ADD REASONS
* to reduce the amount data in a model
* for performance reasons

To create this second model I'll cheat a little bit.  The Sails command-line tool ships with a few simple [generators]() for quickly generating common code files.  This keeps me from having to manually create new files and put together the boilerplate code every time I want to add something new to my app.

To define my **Profile** model, I'll open the command line and enter:

 ```sh
 $ sails generate api profile
 ```
 
When I open `sailsPeople/api/models/Profile.js`, an empty model has been created for me.  All I have to do is add a **name** attribute.


```javascript
attributes: {
  aboutMe: {
    type: 'string'
  },
  gender: {
    type: 'string'
  }
}
```

At this point, I have defined and configured two models:

<img src="http://i.imgur.com/0OWp9gy.jpg" />


## Working with Data

Throughout this project I'm going to differentiate between a **model**, an **instance of a model** and a **collection**.  

A **model** is a _definition_ of a thing (e.g. User.js): 

```javascript
attributes: {
  name: {
    type: 'string'
  }
}
```

An **instance of a model** is _one_ of those things:

```json
{
"name": "Nikola Tesla",
"createdAt": "2014-04-14T17:18:49.439Z",
"updatedAt": "2014-04-14T17:18:49.439Z",
"id": 1
}
```

 A **collection** is a _group_ of model instances:

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

> Don't confuse my use of the collection with a collection in MongoDB or a Backbone collection, although they share some similar qualities they are not the same thing.

So with my User model, I can create new instances of the model in any number of different places in Sails:

- from the [`sails console`]()
- by hitting one of my [blueprint routes]()
- from a [lifecycle callback]() defined in the User model itself
- from a custom action in a [controller]()

I'll use a few different methods from the list above to work with data throughout this project, starting with **blueprint routes**.  If you'd like a more detailed explanation of blueprint routing, particularly the **shortcut blueprint routes** below, check out: [How do blueprint routes work in sails?](http://irlnathan.github.io/sailscasts/blog/2014/01/17/sailscasts-answers-ep8-how-do-blueprint-actions-and-blueprint-routes-work-in-sails/) 

### Creating some users
First, I want to create a couple of Users and a couple of Leads.  I'll lift my app using `sails lift`.

I'll create my first User by opening my browser and entering the following URL: [`http://localhost:1337/user/create?name=Nikola Tesla`]()

**What's happening here?**  

- My browser is sending a GET request to my running Sails app at [localhost:1337]()
- That request matches up to a **shortcut blueprint** route, [/user/create]()
- The shortcut blueprint triggers an action, `create()` defined in `api/controllers/UserController.js`.
- **Note:** I haven't defined a custom `create` action, so the implicit [**blueprint action**]() will be run in its place.
- This inspects the [parameters]() I passed in (`?name=Nikola Tesla`) and passes them to [`User.create()`]().
- This creates a instance of my User model for a new user named "Nikola Tesla". 

After creating the instance of the User model, Sails returns the following json:

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

Next, I'm going to create an additional User with the name `Neal Stephenson`.  I'll visit exactly the same URL as before, but change out the `name` parameter: [`http://localhost:1337/user/create?name=Neal Stephenson`]()

So now I have two instances of my User model:

<table>
<tr>
  <th align="center" colspan="2">Instances of User Model</th>
</tr>
<tr>
  <th align="center">id</th>
  <th align="center">name</th>
  <th align="center">createdAt</th>
  <th align="center">updatedAt</th>
</tr>
<tr>
  <td align="center">1</td>
  <td align="center">'Nikola Tesla'</td>
  <td align="center">'2014-04-14T17:18:49.439Z'</td>
  <td align="center">'2014-04-14T17:18:49.439Z'</td>
  </tr>
  <tr>
  <td align="center">2</td>
  <td align="center">'Neal Stephenson'</td>
  <td align="center">'2014-04-14T20:47:01.703Z'</td>
  <td align="center">'2014-04-14T20:47:01.703Z'</td>
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


### Creating some Profiles

Next I'll create two Profiles using the `/profile/create?aboutMe=This is stuff about me&gender=male` shortcut blueprint route.

<table>
<tr>
  <th align="center" colspan="5">Instances of Profile Model</th>
</tr>
<tr>
  <th align="center">id</th>
  <th align="center">aboutMe</th>
  <th align="center">gender</th>
  <th align="center">createdAt</th>
  <th align="center">updatedAt</th>
</tr>
<tr>
  <td align="center">1</td>
  <td align="center">I was born on July 10 1856 in Serbia. I'm an inventor, electrical engineer, mechanical engineer, physicist, and futurist best known for my contributions to the design of the modern alternating current (AC) electricity supply system.</td>
  <td align="center">male</td>
  <td align="center">'2014-04-14T20:47:25.571Z'</td>
  <td align="center">'2014-04-14T20:47:25.571Z'</td>
  </tr>
  <td align="center">2</td>
  <td align="center">I'm an American author and game designer known for my work in speculative fiction. My novels have been variously categorized as science fiction, historical fiction, cyberpunk, and 'postcyberpunk.</td>
  <td align="center">male</td>
  <td align="center">'2014-04-14T20:47:25.571Z'</td>
  <td align="center">'2014-04-14T20:47:25.571Z'</td>
  </tr>
</table>

### Finding profiles

Accessing stored profiles from the browser works pretty much like accessing stored users. I just send a GET request to [`http://localhost:1337/profile`] and Sails returns the following json:


```json
[{
  "aboutMe": "I was born on July 10 1856 in Serbia. I'm an inventor, electrical engineer, mechanical engineer, physicist, and futurist best known for my contributions to the design of the modern alternating current (AC) electricity supply system.",
  "gender": "male",
  "id": 1,
  "createdAt": "2014-04-24T22:26:54.000Z",
  "updatedAt": "2014-04-24T22:26:54.000Z"
},
{
  "aboutMe": "I'm an American author and game designer known for my work in speculative fiction. My novels have been variously categorized as science fiction, historical fiction, cyberpunk, and 'postcyberpunk.'",
  "gender": "male",
  "id": 2,
  "createdAt": "2014-04-24T22:26:54.000Z",
  "updatedAt": "2014-04-24T22:26:54.000Z"
}]
```

With our models defined, attributes configured, and some initial data created, it's time to address associations.

##Why do we use associations?
**Associations** provide a way to relate models so that finding, creating, updating, and destroying instances of the models requires less programming.

###Configuring a <u>_one-way association_</u> between User and Profile

Let's say I want to be able to find a particular User, and also the Profile she is associated with.  I could manage the relationship manually: i.e. find the User, find the Profile, then format the result. However, it would be a lot nicer to look up both a User and its associated Profile in one action.

To accomplish this, I'll configure the User _model_ with a new attribute called **Profile**-- but this time, instead of specifying a primitive type like "string" or "integer", I'll configure the **Profile** _attribute_ as an association:

<img src="http://i.imgur.com/U6FgUdi.jpg" />

The **Profile** _attribute_ represents a one-way relationship between the User _model_ and the Profile _model_.  I could name this attribute 'foo', however, since it relates to the **Profiel** _model_ I'm going to stay consistent and name the _attribute_ Profile. What this means is that I can now associate a particular User's (e.g. Nikola Tesla's) **Profile** _attribute_ with one of my **Profile** _model_ instances (e.g. id: 1).



### How does this work?
Sails abstracts away a lot of the complexity of associations for me, and it supports both "schema-ful" and "schema-less" databases. The underlying implementation of associations is slightly different in each case, but since it can be helpful to examine what's going on under the covers, I'll provide a quick overview using terminology from the classic relational model.
	
Each instance of the model contains a _**Primary Key**_ which is an attribute whose value is guaranteed to be unique between the instances of the model.  For example, the default _**Primary Key**_ in MongoDB would look something like `_id: ObjectId("52752fd8a61584b30c000001")` versus PostgreSQL which would look something like `id: 1`.  

In this project, I have an instance of the **User** model -- _Nikola Tesla_ with an **id:** of 1.  The _id:_ **is** the _Primary Key_ and this _primary key_ becomes very useful when I want to associate one _record_ with another _record_.  

<img src="http://i.imgur.com/zO6TSzC.jpg" />

It really helps to see this in action, so I'll set up an example using a table to visualize the instances of the models stored in my **User** and **Lead** models before and after the association is made.

<img src="http://i.imgur.com/2BBPptx.jpg" />

> Note: This _logical_ diagram reflects the the model relationship and not where or how the records are persisted (stored).

Before I make the association _Nikola Tesla_ and _Profile_ exist independently of each other.  However, if I place the profile's _primary key_  as the value of the **Profile** _attribute_ in the User _model_ , I can deduce that **the profile** is associated with **Nikola Tesla**. 

As you might expect, the **Profile** _attribute_ in each instance of the **User** _model_ is now capable of referring to a particular instance of the **Profile** _model_.  It's important to point out that the **Profile** _attribute_ doesn't necessarily have to be set-- a user might not have a Profile!  

So, now that I have this association between the Profile and User _models_, **_what can I do with it?_**


## Associating models in a one-way relationship

I'm going to use the sails console to make an association between a particular User and a particular Profile.  

>**Note:** I can use the same code I use to communicate with my models in the `sails console` as in a custom controller action.

First I'll open the [Sails console]() by running the follwing command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.update({id: 1}).set({profile: 1}).exec(function(err, user){console.log(user); });
```

That's hard to read so let's look at the same code formatted in a more readable style:

```javascript
User.update({id: 1})
  .set({profile: 1})
  .exec(function(err, user){
    console.log(user);
  });
```

<!-- the following paragraph is really nice- it's an excellent way to do examples: ~mike -->

Here, I'm updating the **Profile** _attribute_ of the first user instance (where `id:` is 1).  This particular user is better known as Nikola Tesla, and his **Profile** has a primary key of 1, referring to the Lead known as Thomas Edison.  This leaves me with:

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