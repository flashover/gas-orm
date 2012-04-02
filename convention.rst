.. Gas ORM documentation [convention]

Convention
==========

Gas ORM makes some assumptions about your database structure. Each table should have a primary key, by default called **id**. If your primary key isn't **id**, you can set the **primary_key** properties to change it. Each table should have the same name with its corresponding Gas model's name, otherwise you will need to set the **table** property.

Typically your Gas model look be something like this, let's say you have a user.php to hold the user table. ::

	<?php namespace Model;

	use \Gas\Core;
	use \Gas\ORM;

	class User extends ORM {

		function _init() 
		{
			// Relationship definition

			// Field definition
		}

	}

Notice that you will need to specify the field definition and relationship definition, and then you can start using finder or do write operations.

Model Properties
++++++++++++++++

But if somehow, your schema didn't allow you to follow the above convention, you can specify both **table** and **primary_key** properties, which would make it look something like : ::

	<?php namespace Model;

	use \Gas\Core;
	use \Gas\ORM;

	class User extends ORM {

		public $table = 'person';

		public $primary_key = 'person_id';

		function _init() 
		{
			// Relationship definition

			// Field definition
		}

	}

If you have a pivot table, which holds a composite key, you can specify **foreign_key** properties : ::

	 <?php namespace Model\Role;

	use \Gas\Core;
	use \Gas\ORM;

	class User extends ORM {

		public $foreign_key = array('\\model\\user' => 'user_id', '\\model\\role' => 'role_id');

		function _init() 
		{
			// Relationship definition

			// Field definition
		}

	}

And you are ready to go for the next step.
