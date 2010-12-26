# WAPI

Django web API framework.

## Description

WAPI makes it easy to create a Django API for your project. All you have to do is create a class that represents part of your API and then map that to a URL.

## Installation

To install WAPI, you can [download it](https://github.com/danawoodman/wapi) or checkout the repository with GIT and put it on your `PythonPath`:

    git clone git://github.com/danawoodman/wapi.git wapi

... or install it with [PIP](http://pip.openplans.org/):

    pip install git+git://github.com/danawoodman/wapi.git#egg=wapi

I recommend using [`virtualenv`](http://pypi.python.org/pypi/virtualenv) and PIP together so you can have an isolated Python environment to try out your code.

## Usage

As an example, we are going to create a basic Event model in our `models.py` within our `my_app` app:

    from django.db import models
    
    class Event(models.Model):
        place_name = models.CharField(max_length=50)
        what = models.CharField(max_length=50)
        when = models.DatetimeField()

Next, we create an `EventApi` class that lists all the events happening today in an `api.py` file in our app:

        from wapi.responses import SerializableResponse

        class EventApi(object):
            def today(self, request, dct):
                d = date.today()
                return SerializableResponse(Event.objects.filter(when__year=d.year, when__month=d.month, when__day=d.day)
    
We pass in the event `QuerySet` into `SerializableResponse` and return it's result.

Now all we need to do is add a URL to the API in our project's `urls.py`:

    from django.conf.urls.defaults import *
    
    from wapi.bindings import RestBinding
    
    from my_app.api import EventsApi

    urlpatterns = patterns('',
        ...
        ...
        (r'^api/1.0/rest/%s$' % RestBinding.PATTERN, RestBinding(api=EventsApi())),
        ...
    )

If you point your browser to `http://example.com/api/1.0/rest/today.json` you should get a `JSON` response like so:

    [  { id: 7, place_name: "Foo Place", what: "Writing a tutorial", when: "2008-08-11 17:21:56" }]

Or, if you prefer, you could also open `http://example.com/api/1.0/rest/today.xml` or `http://example.com/api/1.0/rest/today.yaml`, WAPI also supports them. 


See [this blog entry](http://fi.am/entry/building-a-website-api-with-django-part-1-api-func/) for more details on how to use WAPI.
