aiohttp_jinja2
==============


**This is a fork of** `aio-libs/aiohttp-jinja2 Pull Request 154 <https://github.com/aio-libs/aiohttp-jinja2/pull/154>`_ **which adds support for calling asyncio coroutines from jinja scripts.**

This feature is disabled by default, to enable it you must set enable_async=True when calling the setup function. This will set enable_async in the jinja2 Environment.

.. image:: https://travis-ci.org/aio-libs/aiohttp-jinja2.svg?branch=master
    :target: https://travis-ci.org/aio-libs/aiohttp-jinja2
.. image:: https://codecov.io/gh/aio-libs/aiohttp-jinja2/branch/master/graph/badge.svg
    :target: https://codecov.io/gh/aio-libs/aiohttp-jinja2
.. image:: https://img.shields.io/pypi/v/aiohttp-jinja2.svg
    :target: https://pypi.python.org/pypi/aiohttp-jinja2
.. image:: https://readthedocs.org/projects/aiohttp-jinja2/badge/?version=latest
    :target: http://aiohttp-jinja2.readthedocs.io/en/latest/?badge=latest


jinja2_ template renderer for `aiohttp.web`__.


.. _jinja2: http://jinja.pocoo.org

.. _aiohttp_web: https://aiohttp.readthedocs.io/en/latest/web.html

__ aiohttp_web_

Installation
------------
Install from PyPI::

    pip install aiohttp-jinja2


Developing
----------

Install requirement and launch tests::

    pip install -r requirements-dev.txt
    py.test tests


Usage
-----

Before template rendering you have to setup *jinja2 environment* first:

.. code-block:: python

    app = web.Application()
    aiohttp_jinja2.setup(app,
        loader=jinja2.FileSystemLoader('/path/to/templates/folder'))

Import:

.. code-block:: python

    import aiohttp_jinja2
    import jinja2


`Async rendering feature <http://jinja.pocoo.org/docs/2.9/api/#async-support>`_
is enabled by default. You *might want* to disable that providing ``enable_async=False``
keyword argument to ``setup`` method. There is no difference in usage between two of
them. In async mode enabled if you try to invoke sync Jinja2 methods (e.g. ``render``)
it will internally invoke corresponding sync method (e.g. ``render_async``) and run it
as part of the current event loop until the execution finished.

After that you may want to use template engine in your *web-handlers*. The
most convenient way is to decorate a *web-handler*.

Using the function based web handlers:

.. code-block:: python

    @aiohttp_jinja2.template('tmpl.jinja2')
    def handler(request):
        return {'name': 'Andrew', 'surname': 'Svetlov'}

Or for `Class Based Views
<https://aiohttp.readthedocs.io/en/stable/web_quickstart.html#class-based-views>`:

.. code-block:: python

    class Handler(web.View):
        @aiohttp_jinja2.template('tmpl.jinja2')
        async def get(self):
            return {'name': 'Andrew', 'surname': 'Svetlov'}


On handler call the ``aiohttp_jinja2.template`` decorator will pass
returned dictionary ``{'name': 'Andrew', 'surname': 'Svetlov'}`` into
template named ``tmpl.jinja2`` for getting resulting HTML text.

If you need more complex processing (set response headers for example)
you may call ``render_template`` function.

Using a function based web handler:

.. code-block:: python

    async def handler(request):
        context = {'name': 'Andrew', 'surname': 'Svetlov'}
        response = aiohttp_jinja2.render_template('tmpl.jinja2',
                                                  request,
                                                  context)
        response.headers['Content-Language'] = 'ru'
        return response

Or, again, a class based view:

.. code-block:: python

    class Handler(web.View):
        async def get(self):
            context = {'name': 'Andrew', 'surname': 'Svetlov'}
            response = aiohttp_jinja2.render_template('tmpl.jinja2',
                                                      self.request,
                                                      context)
            response.headers['Content-Language'] = 'ru'
            return response


License
-------

``aiohttp_jinja2`` is offered under the
`Apache 2 license <https://tldrlegal.com/license/apache-license-2.0-(apache-2.0)>`_.
