# Introduction to Model Associations
	##Overview of the project?
		### Getting ready for the project
	## What are models?
		### When do I use models?
		### Defining models
		### Models, Instances of Models and Collections
		### Instances of Models: _Creating_ some Users
		#### How does this work?
		### Instances of Models: _Finding_ Users
		### Instances of Models: _Creating_ some Profiles
		### Instances of Models: _Finding_ Profiles
	## Why do we use _Associations_?
		### Configuring the Model: a _one-way association_
		### Updating instances of a model: a _one-way association_
		### Finding instances of a model: using "populate" in a _one-way association_
		### Create new instances of a model: a **new** _Profile_ with an existing _User_
			#### How does this work?
		### Create new instances of a model: a **new** _User_ with an existing _Profile_
			#### How does this work?
	    ### Removing the association of a model instance: _one-way association_ 
		### Configuring the Model: a _one-to-one association_ between User and Profile
		### Finding instances of a model: using "populate" in a _one-to-one association_
		### Create new instances of a model: add a new _User_ and _Profile_ at one time
		### Updating instances of a model: a _one-to-one association_
	    ### Removing the association of a model instance: one-to-one association_





Change _attribute_ to _association attribute_
Possibly change instance of a model to record

Models
  Configuring
  Creating Instances
  Finding Instances
  Updating Instances
  Deleting Instances
Associations
  One-way Associations
  Two-way Associations
  One to Many Associations
  Many to Many Associations

 What is the difference in a one to many or many to many association between a single model versus two models.  That is, is there a difference between:

 One-way
 -------

 > User.js

 module.exports = {

  attributes: {

  	name: 'string',

	following: {
		collection: 'user'
	}
  }
};

-AND-

 > User.js

 module.exports = {

  attributes: {

  	name: 'string',

	leads: {
		collection: 'lead'
	}
  }
};
