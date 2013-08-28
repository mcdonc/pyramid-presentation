Pyramid Web Framework
=====================

.. include:: <s5defs.txt>

.. class:: center

   Chris McDonough

   Agendaless Consulting

..  footer:: ZPUGDC/2011

Speaker Info
------------

- Chris McDonough (<chrism@plope.com>)

- Zope developer (1999-2008)

- Chief BFG/Pyramid Bottlewasher (2008-present)

Pyramid History
---------------

- 2008 through 2010, existed as  ``repoze.bfg``.

- ``repoze.bfg`` Zope-inspired.  Features cadged from Pylons and Django.

- October 2010, ``repoze.bfg`` was forked to become Pyramid, a Pylons Project
  subproject.

- More Pylons-esque features added after fork.

- "Pay only for what you eat" web framework.

Pyramid Status
--------------

- Currently at 1.0a8.

- "alpha" is a CYA for bw compatibilites, not a code quality judgment.

Pylons Project
--------------

- Merger of the Pylons, ``repoze.bfg``, and (to a lesser extent) TurboGears
  web frameworks.

- Pylons 1.x web framework shifted into “legacy” status.  Maintained
  indefinitely.

- v1.3 will be the last release of ``repoze.bfg``.  Maintained
  indefinitely.

- "Repoze" now contains only software derived mostly from Zope
  (e.g. ``repoze.who``, ``repoze.tm2``, ``repoze.profile``, etc).

- Hoping to bring some TurboGears folks into Pyramid development soon.

Pyramid Features
----------------

- Map URLs to code.

- Authentication and authorization.

- Internationalization.

- Single-file apps or apps as packages.

- Easy unit, integration, functional testing.

- WSGI deployment.

- Comprehensive `docs <http://docs.pylonshq.com/pyramid/dev/index.html>`_
  (~650 pages of narrative docs, online cookbook and tutorials), updated
  frequently.

Pyramid Features (cont'd)
-------------------------

- Chameleon and Mako templating out of the box; Jinja2 as an add-on.
  Multiple templating systems can be used at the same time.

- Easy "REST API" creation and JSON rendering.

- Runs on CPython 2.4+, GAE, Jython, and PyPy (no Python 3 support).

- Multiple modes of configuration.

- Extensible configuration ("plugins", reuse apps without forking).

- Static asset features.

Pyramid Features (cont'd)
-------------------------

- Sessioning, flash messaging, CSRF protection.

- Event system.

- Exception views.

- WSGI middleware-provided exception debugging (WebError) and profiling
  (repoze.profile).

- `Fast
  <http://blog.curiasolutions.com/2010/11/the-great-web-technology-shootout-%E2%80%93-round-4-pyramid-vs-django-vs-tg-vs-rails-2-3/>`_.

- 100% statement coverage via unit tests.

Pyramid Non-Features
--------------------

- Not a "full-stack" framework; persistence-system agnostic, no admin
  interface.

- Not a "microframework".  Has ~ 16 distribution dependencies.
  Microframework-like single-file apps possible though.

- No "restricted execution" code ala Zope 2 TTW coding.

Technologies
------------

- WebOb

- Paste

- ``zope.component``

- Chameleon

- Mako

- Venusian (scanning library)

Not Zope
--------

- Pyramid != Zope.  Only uses ``zope.component`` as a library.

- Has many Zopelike features though (traversal, declarative authorization).

Not Pylons
----------

- Pyramid shares no DNA with Pylons.

- But supports many Pylons-esque features.

- Similar, but not identical routing syntax.

- Plugpoints implemented via composition rather than subclassing.

- Has an analogue of Pylons-style "controllers" called "handlers".

"MVC"
-----

- Labels.

- To me, it's not MVC.  To you, it might be.

Sample Application
-------------------

::

   from pyramid.config import Configurator
   from pyramid.response import Response
   from paste.httpserver import serve

   def hello_world(request):
       return Response('Hello world!')

   if __name__ == '__main__':
       config = Configurator()
       config.add_view(hello_world)
       app = config.make_wsgi_app()
       serve(app, host='0.0.0.0')

Sample Application (Using Decorators)
-------------------------------------

::

   # imports elided

   @view_config()
   def hello_world(request):
       return Response('Hello world!')

   if __name__ == '__main__':
       config = Configurator()
       config.scan()
       app = config.make_wsgi_app()
       serve(app, host='0.0.0.0')

Sample Application (Using ZCML)
-------------------------------

- Supported, but we won't show an example.  If ZCML turns you on, read the
  docs.

Application Generation via Paster Template
------------------------------------------

::

   [chrism@thinko env26]$ bin/paster create -t pyramid_starter starter
   Selected and implied templates:
       # .. many more lines ...

Basic Layout of a Created Application
-------------------------------------

::

   starter
   |-- development.ini
   |-- setup.py
   `-- starter
       |-- __init__.py
       |-- resources.py
       |-- static
       |   `-- favicon.ico # and other static assets
       |-- templates
       |   `-- mytemplate.pt
       |-- tests.py
       `-- views.py

Running Paster-Generated App
----------------------------

::

  $ bin/paster serve development.ini

A View
------

- Views represent the code mapped to via a request.  Return a response.

::

   from pyramid.response import Response

   def hello_world(request):
       return Response('Hello world!')

- Can also return something other than a response, if a "renderer" (template,
  json, string, xmlrpc) is used.

Mapping A URL to a View
------------------------


::

   # imports elided

   if __name__ == '__main__':
       config = Configurator()
       config.add_route('home', '/', view='starter.views.my_view')
       app = config.make_wsgi_app()
       serve(app, host='0.0.0.0')

Wait, What?
-----------

- Earlier example used traversal.

- Last example used URL dispatch (routing).

- Pyramid supports both.

Route Predicates
----------------

::

   # imports elided

   if __name__ == '__main__':
       config = Configurator()
       config.add_route('home_GET', '/', view='starter.views.my_view'.
                        request_method='GET')
       config.add_route('home_GET', '/', view='starter.views.my_view'.
                        request_method='POST')
       app = config.make_wsgi_app()
       serve(app, host='0.0.0.0')

View Predicates
---------------

- Multiple views can be associated with the same set of request
  circumstances.

- The "best" is chosen.

- View predicates are like route predicates but are input to view
  configuration statements (like ``@view_config``).

- Make it easy to create "RESTy" APIs and choose different views based on
  request data.

Templating and Renderers
------------------------

- Templates are "renderers".

::

   @view_config(renderer='templates/mytemplate.pt')
   def my_view(request):
       return {'project':'starter'}

Templating and Renderers (cont'd)
---------------------------------

- JSON rendering is supported.

::

   @view_config(renderer='json')
   def my_view(request):
       return {'project':'starter'}

Templating and Renderers (cont'd)
---------------------------------

- Renderers extensible as necessary (you can create your own and distribute
  them for others' usage).

Handlers
--------

- Pylons-style "controller" analogue.  Macro for a single "add_route" and a
  bunch of "add_view" statements.

::

   from pyramid.view import action

   class MyHandler(object):
       def __init__(self, request):
           self.request = request

       @action(renderer='templates/mytemplate.pt')
       def index(self):
           return {'hello':'world'}

Handlers (cont'd)
-----------------

::

    # imports elided

    if __name__ == '__main__':

        config = Configurator()
        config.add_handler('myhandler', '/my/{action}', 
                            handler='myapp.handlers.MyHandler')
        config.add_handler('myhandler', '/my', 
                            handler='myapp.handlers.MyHandler', action='index')


Authentication and Authorization
--------------------------------

::

  # some imports elided
  from pyramid.authentication import AuthTktAuthenticationPolicy

  class Root(object):
      __acl__ = [(Allow, 'chris', 'view')]

  if __name__ == '__main__':
     authn = AuthTktAuthenticationPolicy('seekRit!')
     config = Configurator(authentication_policy=authn,
                           root_factory=Root)

Authentication and Authorization (cont'd)
-----------------------------------------

- In a view, use the Pyramid API in a login handler to "remember"
  authentication credentials.

- Use the headers it returns as input to a response.

Authentication and Authorization (cont'd)
-----------------------------------------

::

  from pyramid.security import remember
  from pyramid.httpexceptions import HTTPFound

  def login_form_handler(request):
      username =  request.POST['username']
      password =  request.POST['password']
      userid = password_check(username, password) # app-defined
      if userid is not None:
          headers = remember(request, userid)
          return HTTPFound('/', headers=headers)
      # otherwise render the login form

Paster Templates
-----------------

- Pyramid itself has very few opinions.

- Paster templates have lots of opinions.

- Templates use (and disuse) specific Pyramid features and provide
  integrations with specific persistence systems (SQLAlchemy, ZODB, MongoDB,
  etc).

Exception Views
---------------

Application exceptions can be caught.

::

  from myapp import MyException

  @view_config(renderer='templates/oops.pt', context=MyException)
  def onerror(request):
      return {'exception':request.exception}

Exception Debugging
-------------------

- WebError: interactive debugging in the browser.

Including Configuration
-----------------------

- User-developed framework extensions can be included declaratively
  (alternate templating languages, alternate sessioning factories, entire
  applications).

::

  import pyramid_jinja2

  if __name__ == '__main__':
     config = Configurator()
     config.include(pyramid_jinja2.includeme)

- (ZCML also good for this and supported).

Including Configuration (cont'd)
--------------------------------

- Configuration is done in two phases.

- This allows for conflict detection and out-of-order execution of
  configuration.

- Good for ensuring a redistributable plugin doesn't clobber the
  configuration done by another.

Event System
------------

::

  from pyramid.events import NewRequest

  def add_attr(event):
      event.request.called = True

  if __name__ == '__main__':
     config = Configurator()
     config.add_subscriber(add_attr, NewRequest)

Hook Points
-----------

- Authentication and authorization policies.

- Session factory.

- Subscribe to well-known events (``BeforeRender``, ``ContextFound``, etc).

- Finished and response callbacks.

- Pluggable request factory.

- Pluggable traverser.

- View execution and argument mapping.

- Create new configuration decorators and ZCML statements.

Futures
-------

- Pyramid 1.0 due out by Pycon US 2011 (~2 months from now).

- Continue collapsing Python web framework world by cooperating rather than
  competing.

- More extension packages (XMLRPC, routing helpers, etc).

- Fuller-stack layer written atop Pyramid (hopefully with help from TGers,
  kicking off some PyCon sprints).

- Join ``#pylons`` channel on ``irc.freenode.net`` to hang out.
