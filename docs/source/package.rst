.. _package:

=============================
tasa_website folder (package)
=============================

\__\init\__\.py
----------------

::

    import os
    import string
    import sqlite3
    import yaml

    from contextlib import closing
    from flask import Flask
    from flask import g

    # These 2 import statements are at the bottom of the file
    import tasa_website.views
    import tasa_website.auth


Imports
~~~~~~~

1. ``import os``

  a. From the `Python docs <https://docs.python.org/3/library/os.html>`__, "this module provides a 
     portable way of using operating system dependent functionality," e.g. getting the current working 
     directory with the :func:`getcwd()` function.
     
2. ``import string``

  a. From the `Python docs <https://docs.python.org/2/library/string.html>`__, "the :class:`string` 
     module contains a number of useful constants and classes, as well as some deprecated legacy 
     functions that are also available as methods on strings."

3. ``import sqlite3``

  a. Lets you use SQLite

  b. “SQLite is a C library that provides a lightweight disk-based database that doesn’t require a 
     separate server process and allows accessing the database using a nonstandard variant of the SQL 
     query language.”

    - John Denero’s lectures on YouTube, specifically 61A Fall 2018 Lectures `32`_, `33`_, `34`_,
      `35`_ provide a brief explanation on SQL and declarative programming.

    - `Python docs <https://docs.python.org/2/library/sqlite3.html>`__ for SQLite3

.. _32: https://youtube.com/playlist?list=PL6BsET-8jgYUzGbLeB0UjKjJnGAtxO43h
.. _33: https://youtube.com/playlist?list=PL6BsET-8jgYVk_CBkIP9WbxckgwMIWuTk
.. _34: https://youtube.com/playlist?list=PL6BsET-8jgYVSWsg45Zm-aSb7it_mvuOZ
.. _35: https://youtube.com/playlist?list=PL6BsET-8jgYWoTuANOebuQVct9mAovzga

4. ``import yaml``

  a. "**YAML** ("YAML Ain't Markup Language") is a `human-readable`_ `data-serialization language`_.
     It is commonly used for `configuration files`_, but could be used in many applications where
     data is being stored (e.g. debugging output) or transmitted (e.g. document headers)." (Wikipedia)

  b. "YAML may be the most human friendly format for structured data invented so far.”
     `(Python Wiki for YAML) <https://wiki.python.org/moin/YAML>`__

  c. YAML files are used to store data in a readable manner. In the case of the website, they store 
     secure login information and API keys. We store the website login, the application secret key, 
     and the Facebook API key on :file:`config.yaml`.

    - **This is secure information and therefore, should never be pushed to the public tasa_website 
      GitHub. You should have a version of it on your machine from the instructions on how to set 
      up the website locally and there’s a copy on the server. It is added to the .gitignore so 
      that you don’t accidentally stage, commit, or push it to the public repository.**

  d. The website uses PyYAML to parse stuff in :file:`config.yaml`.

  e. `PyYAML Documentation <https://pyyaml.org/wiki/PyYAMLDocumentation>`__

.. _human-readable: https://en.wikipedia.org/wiki/Human-readable
.. _data-serialization language: https://en.wikipedia.org/wiki/Serialization
.. _configuration files: https://en.wikipedia.org/wiki/Configuration_file


5. ``from contextlib import closing``

  a. “This module provides utilities for common tasks involving the with statement.”
     (`Python docs <https://docs.python.org/2/library/contextlib.html>`__ for the Context Manager
     library)
    
  b. Allows for usage of with statements which is not a built-in functionality of Python.

  c. See: http://book.pythontips.com/en/latest/context_managers.html

  d. From the `Python Tips <https://book.pythontips.com/en/latest/context_managers.html>`__ book:

      - Context managers allow you to allocate and release resources precisely when you want to. 
        The most widely used example of context managers is the ``with`` statement. Suppose you have 
        two related operations which you’d like to execute as a pair, with a block of code in between. 
        Context managers allow you to do specifically that. For example::

         with open('some_file', 'w') as opened_file:
             opened_file.write('Hola!')

       - The above code opens the file, writes some data to it and then closes it. If an error 
         occurs while writing the data to the file, it tries to close it. The above code is 
         equivalent to::

            file = open('some_file', 'w')
                try:
                    file.write('Hola!')
            finally:
                file.close()

       - While comparing it to the first example we can see that a lot of boilerplate code is 
         eliminated just by using with. The main advantage of using a with statement is that it 
         makes sure our file is closed without paying attention to how the nested block exits.

  e. We specifically import :class:`closing` for use with methods that can’t be used as context managers, 
     specifically, the return object of :func:`connect_db()`.

      - Functionally the same as :func:`open()`.
    
6. ``from flask import Flask``

  a. Lets us use Flask app initialization which is what runs this website

7. ``from flask import g``

  a. Lets us use the :class:`g` object (short for “global” *within a context* meaning that the data stored in the 
     object is lost after the context ends) of Flask.

  b. “To share data that is valid for one request only from one function to another, a global 
     variable is not good enough because it would break in threaded environments. Flask provides you 
     with a special object that ensures it is only valid for the active request and that will return 
     different values for each request. In a nutshell: it does the right thing, like it does for the 
     ``request`` and ``session`` objects.”

  c. While using the website, or doing anything in the application context, the data of the website 
     and request can be accessed through the :class:`g` object. That way, we don’t have to pass around 
     the whole application object through each method that we need to run.

  d. `Flask doc <http://flask.pocoo.org/docs/1.0/appcontext/>`__ on App Contexts

8. ``import tasa_website.views`` and ``import tasa_website.auth``

  a. These are imports that aren’t actually ‘used’ but are needed

  b. From Flask documentation:

      - Circular Imports: Every Python programmer hates them, and yet we just added some: circular 
        imports (That’s when two modules depend on each other. In this case :file:`views.py` depends on 
        :file:`__init__.py`). Be advised that this is a bad idea in general but here it is actually fine. 
        The reason for this is that we are not actually using the views in :file:`__init__.py` and just 
        ensuring the module is imported and we are doing that at the bottom of the file.

  c. Essentially, in :file:`views.py` and :file:`auth.py`, decorators are used to help fulfill what Flask is useful 
     for. To use those decorators, the modules (the other Python files) have to be imported in 
     :file:`__init__.py`. Additionally, in :file:`views.py` and :file:`auth.py`, we need to import 
     :file:`__init__.py` to access vital functions, making them dependent on each other. This is fine 
     because in :file:`__init__.py`, we import at the end of the file, after the application object 
     is created and we don’t use anything from those modules.

Global Variables and Statements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following are all declared for configuration purposes and can be accessed as attributes of the 
Flask app config::

    CWD = os.getcwd()
    ROOT = 'tasa_website/'
    DATABASE = 'tasa_website/tasa_website.db'
    CONFIG = 'tasa_website/config.yaml'
    DEBUG = True
    IMAGE_FOLDER = 'static/images/events/'
    OFFICER_IMAGE_FOLDER = 'static/images/officers/'
    FAMILY_IMAGE_FOLDER = 'static/images/families/'
    FILES_FOLDER = 'static/files/'
    SCRAPBOOK_FOLDER = 'static/images/scrapbook/'

    secrets = {}
    with open(CONFIG, 'r') as config:
        secrets = yaml.load(config)

    SECRET_KEY = secrets['secret']

    app = Flask(__name__)
    app.config.from_object(__name__)
    app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024 # 16 megabytes

``CWD = os.getcwd()`` gets the current working directory of where the process is being run, which is 
:file:`__init__.py`.
``ROOT = 'tasa_website/'`` is the root directory of the website.
``DATABASE = 'tasa_website/tasa_website.db'`` is the location of the database file.
``CONFIG = 'tasa_website/config.yaml'`` is the location of the YAML config file.
``DEBUG = True`` enables debug mode for the local version of the website. When there is an error while using the Flask application in your local environment, an interactive error traceback with console will be displayed.
``IMAGE_FOLDER = 'static/images/events/'`` is the location of the event images.
``OFFICER_IMAGE_FOLDER = 'static/images/officers/'`` is the location of the officer images.
``FAMILY_IMAGE_FOLDER = 'static/images/families/'`` is the location of the family images.
``FILES_FOLDER = 'static/files/'`` is the location of the files.

1. ``secrets = {}``

  - Declares the secret variable as an empty dictionary

  - We need it as a dictionary for ease of indexing

2. ``with open(CONFIG, 'r') as config:``

  - Opens :file:``CONFIG`` (earlier declared) file in read mode ``r`` as the variable config

  - Closes config file when execution of block is finished

3. ``secrets = yaml.load(config)``

  - The :meth:`yaml.load()` method loads the given YAML file and converts it to a Python object

  - Because the YAML file is formatted to be like a dictionary, the file is converted to a 
    dictionary

  - The :class:`secrets` variable now contains all the YAML key-value pairs

4. ``SECRET_KEY = secrets['secret']``

  - Gets the secret key of the application from the secrets dictionary

5. ``app = Flask(__name__)``

  - Declares the :class:`Flask` application object of the website and sets it to the app variable

6. ``app.config.from_object(__name__)``

  - :class:`app.config` is a dictionary

  - Sets the configurations to that of the website

  - We declared these configurations earlier in __init__.py in global statements and variables

7. ``app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024``

  - Sets the max file size for both images and files to 16 megabytes

Methods
~~~~~~~

::

    def connect_db():
        return sqlite3.connect(app.config['DATABASE'])

    def init_db():
        with closing(connect_db()) as db:
            with app.open_resource('schema.sql', mode='r') as f:
                db.cursor().executescript(f.read())
            db.commit()

    @app.before_request
    def before_request():
        g.db = connect_db()
        g.db.row_factory = sqlite3.Row

    @app.teardown_request
    def teardown_request(exception):
        db = getattr(g, 'db', None)
        if db is not None:
            db.close()

    def query_db(query, args=(), one=False):
        cur = g.db.execute(query, args)
        rv = cur.fetchall()
        g.db.commit()
        cur.close()
        return (rv[0] if rv else None) if one else rv

- :func:`connect_db()` returns a Connection object that represents the database. ``'DATABASE'`` is a 
  configuration key pointing at the database file that was defined earlier.

- :func:`init_db()` initializes the database.

  - ``with closing(connect_db()) as db:``

    - The variable :class:`db` is set to the ``Connection`` object which represents our database.
      
  - ``with app.open_resource('schema.sql', mode='r') as f:``

    - Opens :file:`schema.sql` in read mode

    - The variable ``f`` is set to the text inside :file:`schema.sql`

  - :meth:`db.cursor().executescript(f.read())` takes the contents of :file:`schema.sql`, which
    creates the empty tables within the database, and executes it

    - :meth:`db.cursor()` returns a :class:`Cursor` object that can execute database queries

    - :meth:`executescript()` will execute the given params

    - :meth:`f.read()` will read the contents of ``f``

  - :meth:`db.commit()`

    - Any changes written to the database are pushed to the actual file

- :func:`before_request()` opens the database connection.

- :func:`teardown_request(exception)` calls the :func:`getattr()` function to get the attributes of 
  an object. In this case, it will be the attributes of any objects from the database.

- :func:`query_db(query, args=(), one=False)` is a query function that retrieves the cursor, then 
  executes and fetches the results from the database. It is used in views.py to retrieve a list of 
  objects created from the database (e.g. the officer objects that are passed to :file:`officers.html`).


