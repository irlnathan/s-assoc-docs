# Introduction to Model Associations
	##Overview of the project?
		### Getting ready for the project
	## What are models?
		### When do I use models?
		### Defining models
			>**Task:** Configure a model to hold my _User_ data.
		### Models, Records, and Collections
		### Records: _creating_ some Users and Profiles
			>**Task:** Create a couple of _**Users**_ and what will be there associated _**Profiles**_. 
				#### How does this work?
		### Records: _finding_ Users and Profiles
			>**Task:** Get a list of _User_ and _Profile_ Records
	## Why do I use _Associations_?
	##One-Way Associations: _Configuring_, _Finding_, _Creating_, _Updating_ and _Removing_ Associations
		###Configuring the _Model_: a _one-way association_
			>**Task:** Configure an association where each User can be associated with one Profile
		### Updating a _Record's_ association: a one-way association
			>**Task:** Create an association between _Nikola Tesla_ and his Profile.
				####How does this work? 
		### Finding associated _Records_: using "populate" in a _one-way association_
			>**Task:** Find _Nikola Tesla's_ profile using the _.populate()_ method.
				####How does this work?
		###Creating a **new** _Profile_ and associating it with an existing _User_
			>**Task:** Create a new _Profile_ for _Neal Stephenson_.
				#### How does this work?
		### Creating a **new** _User_ and associating it with an existing _Profile_
			>**Task:** Create a new user _Aimee Mann_ and associate it with her _Profile_.
				#### How does this work?
	    ### Removing the association between a _User_ and a _Profile_: a _one-way associations_
			>**Task:** Remove the association between _Neal Stephenson_ and his _Profile_. 
				####How does this work?
	##Two-way Associations: Configuring, Finding, Creating, Updating and Removing 
		###Configuring the _Model_: a _two-way association_
			>**Task:** Create a new association between the _Profile_ model and the _User_ model.
				####How does this work?
		###Updating a Record's association: a two-way association.
			>**Task:** Associate _Charles Ponzi's_ user **record** with his _Profile_.
				####How does this work?
		###Validating Two-way Associations
		###Unqiue Validation: Enforcing a one-to-one association
			**Task:** Prevent more than one _User_ record being associated with a particular _Profile_.
	##One to Many Associations: Configuring, Finding, Creating, Updating and Removing 
		###Configuring the Model: a one-to-many association
			>**Task:** Create a model called _Lead_ and configure an _association attribute_ of _Leads_ for the _User_ model.
		###Creating a new _Lead_ and adding an association with an existing _User_
			>**Task:** Create a _Lead_ with the name _George Jetson_ and associate him with _Nikola Tesla_. 
				####How does this work?
		###Finding _associated_ Records: using "populate" in a one-to-many association
			>**Task:** Find all of the _Lead_ **records** associated with _Nikola Tesla_.
				####How does this work?
		###Removing the association between a User and a Lead: one-to-many associations
			>**Task:** Remove the association between _Nikola Tesla_ and _Guy Montag_.
				####How does this work?
	##Many to Many Associations: Configuring, Finding, Creating, Updating and Removing 
		###Configuring the Model: a many-to-many association
			>**Task:** Configure the _User_ model so that each user _record_ can be assocated with one or more sailsTeam _records_ through an _association attribute_ called **sailsTeams**, with an _association type_ of collection.  Configure the _sailsTeam_ model so that each sailsTeam _record_ can be associated with one or more user _records_ through an _association attribute_ called **members*, with an _association type_ of collection. 
		###Configuring the Model: a many-to-many association with _via_
			>**Task:** Configure the _User_ and _sailsTeam_ models so that changing the association in one _model_ syncs with the other _model_.
		###Creating a new sailsTeam and add an association with an existing User
			>**Task**: Create a new _sailsTeam_ with the name **Mid-West** and make _Nikola Tesla_ a member.
				####How does this work?
		###Removing an association between an existing sailsTeam and an existing User
			>**Task:** Remove _Nikola Tesla_ from the _Mid-West_ sailsTeeam.
				####How does this work?


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
