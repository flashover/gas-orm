.. Gas ORM documentation [CRUD]

CRUD 
====

The hard part (was it?) is over. Setting up your model, field validation rules and relationship properties, once and for all (unless somehow, in the future, our recent schema doesn't fit anymore). 

Now it's time to see it in action. 

Creating record(s)
++++++++++++++++++++++

How can you create a new record? In a standard way, you will have to write it like below ::

	$user = new Model\User();
	$user->name = 'Mr Foo';
	$user->email = 'foo@world.com';
	$user->username = 'foo';
	$user->save();

	
There is the **create** method, for your convenience, especially when you are receive data from **$_POST**. ::

	Model\User::make($_POST)->save();

By default, Gas does not enforce you to run the validation process  before creating a record. But if you already set up your field validation rules, passing **TRUE** within the **save** method will do the job. ::

	Model\User::make($_POST)->save(TRUE);

What if you want to create a new record, but not by using the data from **$_POST** ? . Lets throw a full scenario in here : ::

	$data = array(
		'name' => 'Mrs Bar',
		'email' => 'bar@otherworld.com',
		'username' => 'bar',
	);

	$new_user = Model\User::make($data);

	if ( ! $new_user->save(TRUE))
	{
		echo 'The raw errors were : ';
		print_r($new_user->errors);
	}
	else
	{
		echo 'New user successfully created. And her id is '.Model\User::last_created()->id;
	}

Notice that you can immediately use the last created resource, by using the **last_created** method.

Reading record(s)
+++++++++++++++++++++

You already met the :doc:`Finder method <finder>`, and you already know how to specify the relations between you models. In :doc:`Finder section <finder>` you already know, how to fetch either one record or a set of records. Now, how can you access this relationship? This is as easy as : ::
	
	$someuser = Model\User::find(1);
	echo 'User 1 name is '.$someuser->name.' and his wife name is '.$someuser->wife()->name;

As you can see, you can directly get the user's Wife, by accessing **wife** method from the User instance. This method is reffered to as the **relationship name** , not as the table name. So even though the real table was **wifes** or **wf** or whatever you chose, only the **related relationship to the model's class name** you specified in the user's relations properties is what really matters.

For **one-to-many** relationship, you can iterate the child nodes by using something like : ::

	$someuser = Model\User::find(1);

	echo 'User 1 name is '.$someuser->name.' and he seems have several kids, with these details :';

	if ( ! empty($someuser->kid()))
	{
		foreach ($someuser->kid() as $kid)
		{
			echo 'Kid '.$kid->id.' name is '.$kid->name;
			echo "\n";
		}
	}
	
This applies to **many-to-many** relationships as well.

Gas supports eager loading, so you can improve your relationship queries, especially when you retrieve a child node from a set of parent instances. Eager loading works for all **relationships** properties you defined. You can eager load any type of relationship tables, using the **with()** method.

In short, instead doing this : ::

	$all_users = Model\user::all(); 

	foreach ($all_users as $some_user)
	{

		echo 'User 1 name is '.$someuser->name.' and he seems have several kids, with these details :';

		foreach ($someuser->kid() as $kid)
		{
			echo 'Kid '.$kid->id.' name is '.$kid->name;

			echo "\n";
		}

	}

Above, you actually will doing **SELECT** as many as your user counts, and this is bad for either you or your mother's health, especially for your grandfather. Eager loading alleviates this N+1 problem, and if you use it wisely, will tremendously increase your application's performance (both for execution time and memory usage). How to do eager load my related model? ::


	$all_users = Model\User::with('kid')->all(); 

	foreach ($all_users as $some_user)
	{
		echo 'User 1 name is '.$someuser->name.' and he seems have several kids, with these details :';

		foreach ($someuser->kid() as $kid)
		{
			echo 'Kid '.$kid->id.' name is '.$kid->name;
			echo "\n";
		}

	}

Now you're only doing two queries, one to **SELECT** all users and the other to **SELECT** all kids with the **WHERE IN** clause and the corresponding user's condition.

Updating record(s)
++++++++++++++++++++++

This section is actually much like the same, with the **create record** section. Instead of doing an INSERT, we will UPDATE the record(s) : ::
	
	$recent_user = Model\User::find(1);
	$recent_user->name = 'New name';

	if ( ! $recent_user->save(TRUE))
	{
		echo 'Something wrong';
	}
	else
	{
		echo 'User 1 successfully updated.';
	}

The beautiful part of using Gas ORM is that you can also update your model relationsips as well. Take a look at the following example : ::

	$someuser = Model\User::find(1);

	$related_wife = $someuser->wife();
	$related_wife->hair_colour = 'black';
	$related_wife->save();

This will remove all hassle and will seriously reduce your development time, and this is indeed good for your health.

Deleting record(s)
++++++++++++++++++

To delete a single record : ::
	
	$someuser = Model\User::find(1);
	$someuser->delete();

Or you can explicitly specify the **id** : ::

	Model\User::delete(1);

Passing **ids** is accepted as well : ::

	Model\User::delete(1, 2, 3, 4, 5, 1000);

When you're working with relational entity, cascading deletes are supported.  This means that you could use **unlink** (for exampthat the user entity from the role entity, without deleting both user record and role record. Instead, you could only delete some record on the pivot table, which links to the records, eg : ::

	$someuser = Model\User::with('role')->find(1);
	$someuser->delete();

This will only delete the record within the pivot table, since above relationship was a many-to-many relation.
