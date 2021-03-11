.. _nonpackage:

=================================
tasa_website folder (non-package)
=================================

run.py
------

When the :command:`make run` command is called in your command line, :file:`run.py` is used to start
the application on a local development server. In :file:`run.py`, :command:`tasa_website` package (aka
folder is located and the :file:`__init__.py` file is executed. This binds the objects it defines to
names in the :command:`tasa_website` package's namespace (e.g. ``ROOT = 'tasa_website/'`` is used in
:file:`views.py` when building file paths to reference any images used in the :command:`static`
folder). ::

    from tasa_website import app

    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=5000)

Imports
~~~~~~~

``import app`` lets us call *app* directly in :file:`run.py`. Writing only ``import tasa_website``
would mean we'd have to write ``tasa_website.app`` to access the *app* object inside the :command:`
tasa_website` namespace.

Read the section for :file:`__init__.py` before continuing.

Statements
~~~~~~~~~~

::

    if __name__ == '__main__':

- When executing a program in Python, several variables, two of which are ``__name__`` and ``__main__``,
  are immediately set. ``__name__`` is evaluated as the name of the current module (aka file). If the 
  module is run directly (from the command line), ``__name__`` is instead set to the string ``'__main__'``.
- One such exception to this is seen in our code when a file is implicitly executed like :file:`__init__.py`
  is. In this case, ``__name__`` will be set to the package name because when :command:`make run` is
  called in the command line, it is run as the main file.

::

    app.run(host='0.0.0.0', port=5000)

- Runs the Flask app on the host on port 5000
