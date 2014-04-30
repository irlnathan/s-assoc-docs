# Introduction to Model Associations

##Overview of the project

I want to create a way of retrieving, storing, and manipulating information in an application I'm going to build called -- _**sailsPeople**_.  Initially, the application will have **Users** (e.g. people that will use the application to track their sales activity) and **Profiles** (e.g. information realating to and describing a **User**).   I need some way of organizing **Users** and **Profiles** in my application and this is usually referred to as a **Data Model**.

### Getting ready for the project
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

##What are models?
The term [**model**]() is one of those over-loaded words that can cause a bunch of confusion.  Therefore, I want to provide some context for how I'm using the term "model" in this project.  For my purposes, a **model** is a representation of a set of structured data, usually corresponding with a table in a SQL database or a collection in databases like MongoDB, Redis, or Riak.

###When do I use models?
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


### Models, Instances of Models, and Collections

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

### Instances of Models: _Creating_ some Users
First, I want to create a couple of Users and a couple of Leads.  I'll lift my app using `sails lift`.

I'll create my first User by opening my browser and entering the following URL: [`http://localhost:1337/user/create?name=Nikola Tesla`]()

####How does this work?

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


### Instances of Models: _Finding_ Users

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


### Instances of Models: _Creating_ some Profiles

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

### Instances of Models: _Finding_ Profiles

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

##Why do we use _Associations_?
**Associations** provide a way to relate models so that finding, creating, updating, and destroying instances of the models requires less programming.  Using _Associations_ has two parts: configuring the model and then using dedicated methods which use the associations to make finding and changing models instances easier.

###Configuring the Model: a _one-way association_

Let's say I want to be able to find a particular User, and also the Profile she is associated with.  I could manage the relationship manually: i.e. find the User, find the Profile, then format the result. However, it would be a lot nicer to look up both a User and its associated Profile in one action.

To accomplish this, I'll configure the User _model_ with a new attribute called **Profile**-- but this time, instead of specifying a primitive type like "string" or "integer", I'll configure the **Profile** _attribute_ as an association:

<img src="http://i.imgur.com/DOZDdoG.jpg" />

The **Profile** _**association** attribute_ represents a one-way relationship between the User _model_ and the Profile _model_.  I could name this attribute 'foo', however, since it relates to the **Profile** _model_ I'm going to stay consistent and name the _attribute_ Profile. What this means is that I can now associate a particular User's (e.g. Nikola Tesla's) **Profile** _attribute_ with one of my **Profile** _model_ instances (e.g. id: 1).


Sails abstracts away a lot of the complexity of associations for me, and it supports both "schema-ful" and "schema-less" databases. The underlying implementation of associations is slightly different in each case, but since it can be helpful to examine what's going on under the covers, I'll provide a quick overview using terminology from the classic relational model.
	
Each instance of the model contains a _**Primary Key**_ which is an attribute whose value is guaranteed to be unique between the instances of the model.  For example, the default _**Primary Key**_ in MongoDB would look something like `_id: ObjectId("52752fd8a61584b30c000001")` versus PostgreSQL which would look something like `id: 1`.  

In this project, I have an instance of the **User** model -- _Nikola Tesla_ with an **id:** of 1.  The _id:_ **is** the _Primary Key_ and this _primary key_ becomes very useful when I want to associate one instance of the _model_ with another instance of a _model_.  

<img src="http://i.imgur.com/zO6TSzC.jpg" />

It really helps to see this in action, so I'll set up an example using a table to visualize the instances of the models stored in my **User** and **Profile** models before and after the association is made.

<img src="http://i.imgur.com/5oku524.jpg" />

> Note: This _logical_ diagram reflects the the model relationship and not where or how the records are persisted (stored).

Before I make the association _Nikola Tesla_ and _Profile_ exist independently of each other.  However, a relationship will be created when I update the instances of the _model_ to include an association.

### Updating instances of a model: a _one-way association_

I'm going to use the sails console to make an association between _Nikola Tesla_ and a particular Profile.  

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

So now that I've placed the Profile's _primary key_  as the value of the **Profile** _attribute_ in the User _model_ , sails can deduce that **the profile** is associated with **Nikola Tesla**.  

<img src="http://i.imgur.com/bKGYcIm.jpg" />


As you might expect, the **Profile** _attribute_ in each instance of the **User** _model_ is now capable of referring to a particular instance of the **Profile** _model_.  It's important to point out that the **Profile** _attribute_ doesn't necessarily have to be set-- a user might not have a Profile!  Also important is that at this point an instance of the Profile _model_ can belong to more than one User.

So now that I've created the association, what can I do with it?

### Finding instances of a model: using "populate" in a _one-way association_

<img src="http://i.imgur.com/VjlXM84.jpg" />

There are several methods I'll be using to **populate** (i.e. look up), **add**, and **remove** associated instances of the models.  Here is a general description of each method:


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
User.findOne(1)
  .populate('profile')
  .exec(function(err,user){
    console.log(user); 
  });
```

I first find 'Nikola Tesla' using the `.findOne` method.  I then chain `.populate` to look up the `profile`. Sails returns the following json:

```javascript
{ 
  profile: 
   { aboutMe: 'I was born on July 10 1856 in Serbia.  I\'m an inventor, electrical engineer, mechanical engineer, physicist, and futurist best known for my contributions to the design of the modern alternating current (AC) electricity supply system.',
     gender: 'male',
     id: 1,
     createdAt: Thu Apr 24 2014 17:26:54 GMT-0500 (CDT),
     updatedAt: Thu Apr 24 2014 17:26:54 GMT-0500 (CDT),
     owner: null },
  name: 'Nikola Tesla',
  id: 1,
  createdAt: Thu Apr 24 2014 17:26:54 GMT-0500 (CDT),
  updatedAt: Fri Apr 25 2014 18:39:25 GMT-0500 (CDT) 
}
```
 
###Create new instances of a model: a **new** _Profile_ with an existing _User_

<img src=http://i.imgur.com/vEqilgL.jpg />

First I'll open the [Sails console]() by running the follwing command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:


```javascript
sails> Profile.create({aboutMe: "I'm an American author and game designer known for my work in speculative fiction. My novels have been variously categorized as science fiction, historical fiction, cyberpunk, and 'postcyberpunk.", gender: "male"}).exec(function(err, profile) { User.findOne(2).exec(function(err, user) {user.profile = profile.id; user.save(function(err){}); }); });
```

Here is the same code but in a more readable format:

```javascript
Profile.create({aboutMe: "I'm an American author and game designer known for my work in speculative fiction. My novels have been variously categorized as science fiction, historical fiction, cyberpunk, and 'postcyberpunk.", 
  gender: "male"})
  .exec(function(err, profile) { 
    User.findOne(2).exec(function(err, user) {
      user.profile = profile.id; 
      user.save(function(err){}); 
    }); 
  });
```

####How does this work?

1. First I create the **new** _Profile_ using the `.create` method and pass in two attributes _aboutMe_ and _gender_.
2. Next, I find the _User_ **Neal Stephenson** who I want to assocate with the new _Profile_ via the `.findOne` method and passing in the **Neal Stephenson's** _Primary Key_ of `1`.
3. Next, I asign the Profile _attribute_ of **Neal Stepheson** with the Profile `id:` returned from step one using `user.profile = profile.id;`
4. Finally I save the model instance of **Neal Stephenson** by using the `.save` method.

### Create new instances of a model: a **new** _User_ with an existing _Profile_

<img src=http://i.imgur.com/1gDOtgW.jpg />

I could use the same strategy I used in the previous example, however, because the User _model_ is aware of the Profile _model_ through its association, I can also use an additional, less verbose, approach.

For this example, I have a previously created instance of the Profile _model_:

```json
{ 
  "aboutMe": "I'm an American rock singer-songwriter, guitarist and bassist. In the 1980s, I sang in the Boston new wave band 'Til Tuesday until I left to begin a solo career in the early 1990s.",
  "gender": "female",
  "id": "3",
  "createdAt": "Sat Apr 26 2014 22:22:56 GMT-0500 (CDT)",
  "updatedAt": "Sat Apr 26 2014 22:22:56 GMT-0500 (CDT)"" 
}
```


First I'll open the [Sails console]() by running the follwing command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:


```javascript
sails> User.create({name: 'Aimee Mann', profile: 3}).exec(function(err, user { console.log(user); });
```

Here is the same code but in a more readable format:

```javascript
User.create({name: 'Aimee Mann', profile: 3})
  .exec(function(err, user {
    console.log(user);
  });
```

####How does this work?

1. First I create the **new** _User_ using the `.create` method and pass in the name  along with Profile _attributes_.
2. Because my User _model_ is already aware of the Profile _association_, I simply had to pass the Profile _attribute_ and value and sails automatically assigns the Profile's primary id to the Profile _attribute_ of the User _model_.

### Removing the association of a model instance: one-way associations

I can remove Nikola Tesla's association with its Profile by assigning `null` to the Profile _attribute_ of Nikola Tesla.

<img src="http://i.imgur.com/oA9DUmV.jpg" />

###Configuring the Model: a _one-to-one association_ between User and Profile

So far, I've been using a one-way association. I’m able to find out which Profiles are associated with a User from the User _model's_ Profile _attribute_.  I’m unable to do this from the Profile _model_ because the Profile _model_ doesn’t have an Association _attribute_ to store the primary key of the associated User.  But that’s easy to fix.

<img src="http://i.imgur.com/pjLX2Pk.jpg" />

Similar to how I configured the User _model_, I'll configure the Profile _model_ with a new Association attribute called **Owner**.  

<img src="http://i.imgur.com/8VPMYjA.jpg" />

I now have a place in the User _model_ for the primary key of an instance of the Profile _model_.  

<img src ="http://i.imgur.com/AJkVupC.jpg" />


**Now that I have a two-way relationship, what can I do with it?**

### Finding instances of a model: using "populate" in a _two-way association_

Not surprisingly finding an instance of the User _model_ and populating its associated instance of the Profile _model_ is identical to the one-way association.  Where things get interesting is in Creating, Updating, and Deleting Associations.

<img src="http://i.imgur.com/3QHbFXb.jpg" />

### Create new instances of a model: add a new _User_ and _Profile_ at one time

<img src="http://i.imgur.com/JNJJn97.jpg" />

### Updating instances of a model: a _two-way association_

Updating an instance of a the Profile _model_ is identical to a one-way association.

<img src="http://i.imgur.com/DafMu45.jpg" />

### Removing the association of a model instance: _two-way associations_


<img src="http://i.imgur.com/V9RNJMA.jpg" />

Here there are two associations to remove -- Nikola Tesla's association with the Profile through the Profile _association attribute_ and the Profile model instance through the Owner _association attribute_.  I can remove...

