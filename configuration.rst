.. Gas ORM documentation [configuration]

Configuration
=============

Most likely, you won't need to configure anything. The config file is located at **application/config/gas.php**, and the available configuration optios are :

Models Path 
++++++++++++

Models path is a directory you are telling Gas ORM to look for your model classes. Generally, it would be on **application/models**. ::

	$config['models_path'] = array('Model' => APPPATH.'models');

If you use this default config, **Model** will be the namespace of your model(s).

Per-request Cache
+++++++++++++++++

Gas ORM has chosen an approach that will optimize your queries. But to make it even better, there are caching request options. Per-request caching shortly means : never use the same query twice within one server request. This is on by default. ::

	$config['cache_request'] = TRUE;

Set this options to **FALSE** to turn it off. 

You don't have to worry, that this caching mechanism might retrieves outdated resources/records. Gas ORM monitoring everythings, and it knows exactly what has been changed. But this assumes, you are using its native APIs ( **save** method for insert or update record(s) and **delete** for destroy record) when performing write operations on your database tables. This implies, if you have a **query** method within your Gas models which performs some write action (in a transaction block for example), you should flush the cache manually. Flushing cache means you reset the related cache resource. After that the cache mechanism will start caching your resource all over again.

Auto-create models
++++++++++++++++++

Gas ORM can auto-create models. This means you can convert your existing database scheme into Gas models by using the Gas ORM model convention. For security reason, this option is disabled by default. To enable it you can set the following option : ::

	$config['auto_create_models'] = TRUE;

This option will create all models in the basic state, meaning that you will need to tweak its relationships and / or validation rules. This mechanism will also create all model's siblings : migrations files respectively.

Since mechanism to auto-create models needs to access to the migrations configuration, you might need to enable the Migration class in your migration configuration.

.. note:: Auto-create models feature is intended to help you to make a jumpstart. After you enable this option and you successfully generated all files, please turn it back off.

Auto-create tables
++++++++++++++++++

Gas ORM can also auto-create the databaes tables. This mean you can convert your existed Gas models into database. For security reason, this option is disabled by default. To enable : ::

	$config['auto_create_tables'] = TRUE;

This option will create all necessary tables in the basic state, meaning that you will need to tweak its relationships, key/indexing or any other task to optimize your database schema. This mechanism will also create all model's siblings : migrations files respectively.

Since auto-create tables mechanism need to access migrations configuration and run some its method, you need to enable Migration class in your migration configuration.

.. note:: The auto-create tables feature is intended to help you to make a jumpstart. After you enable this option and successfully generate all tables, turn it off.
