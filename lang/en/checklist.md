## Simplicity
- [ ] Write sample client code in the README
E.g. Pendulum's README starts with [sample code](https://github.com/sdispater/pendulum)
- [ ] Reduce boilerplate code: count the number of lines up to first really useful API call
E.g. urllib2 has [lots of boilerplate code](https://gist.github.com/kennethreitz/973705) to do an HTTP request compared to Requests library
- [ ] Document use cases
E.g. [python-social-auth document common use-cases](http://python-social-auth-docs.readthedocs.io/en/latest/use_cases.html)
- [ ] Practice [Progressive disclosure](https://en.wikipedia.org/wiki/Progressive_disclosure): have sane and smart defaults
  * - [ ] Have default arguments and decide their values according to the most common use cases
  * - [ ] Have default settings and decide their values according to the most common use cases
  * - [ ] Decide argument order by listing the most used ones first and grouping related ones together
  E.g. JavaScript [`history.pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History_API#The_pushState()_method) has bad default argument order: `state, title, URL`. Most API clients just want to add a URL to the history and they're forced to specify state and title.
  * - [ ] Don't require copy and pasting of code snippets
- [ ] Avoid cumbersome inputs:
  * - [ ] Check if parameter name is misleading
  E.g. In Scrapy 1.2, the send method accepts a "to" parameter as a list of strings. If the client passes a single string, the method will iterate over the string, trying to send emails to each character of it. Scrapy 1.3 fixed this by accepting both a single string and a list of strings
  * - [ ] Check if the user is instantiating something just to call the API. If so, consider accepting the wrapped value
  E.g. a function that only accepts file-like objects will force clients to use `StringIO` if they want to pass strings.
  * - [ ] Check if it's possible to replace a custom type with a built-in one, or support both
- [ ] Respect the [Principle of least astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment): "If a feature has a high astonishment factor, it may be necessary to redesign it"
  * - [ ] The default behavior does what clients expect?
  * - [ ] The default behavior matches client expectations about performance, side-effects, safety, security, restrictions, etc.?
- [ ] Create abstractions
  * - [ ] Draw away from the physical HOW nature of the solution to focus on the WHAT nature
  E.g. to make a simple Celery task, devs don't need to worry much about task queues, workers, message brokers, serialization, etc. They just need to use the `@app.task` decorator. The API is focused on the task definition, not on the tasks inner-workings.
  * - [ ] Is the API abstracting something it shouldn't be? Be careful with [The Law of Leaky Abstractions](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/)
  E.g. [RPC is almost naturally bad](https://www.joelonsoftware.com/2000/08/22/three-wrong-ideas-from-computer-science/) because it abstracts remote resources as local ones, but those should really be handled differently from local ones
- [ ] Be Pythonic
  * - [ ] Strive for common Python idioms, try to make API calls look like built-in Python API calls
  E.g. in Python 2, [`ConfigParser.get`](https://docs.python.org/2/library/configparser.html#ConfigParser.RawConfigParser.get) receives a `section` and `option`. This isn't natural in Python since `get` of `dict` receives `key` and `default` value. In Python 3, this was fixed by introducing a [dict-like interface to `ConfigParser`](https://docs.python.org/3.6/library/configparser.html#mapping-protocol-access)

## Consistency
- [ ] Naming: is there naming consistency between the API and the Python ecosystem?
  Follow [PEP8](https://www.python.org/dev/peps/pep-0008/), the official style guide for Python code. To guarantee adherence to [Naming Conventions](https://www.python.org/dev/peps/pep-0008/#naming-conventions) and other style constraints, validate the API code with [flake8](http://flake8.pycqa.org/en/latest/)
- [ ] Naming: is there naming consistency inside the API?
  * - [ ] Order of terms: `string_encode` vs `decode_string`.
  * - [ ] Abbreviations: `activate_prev` vs `fetch_previous`, `bin2hex` vs `strtolower`.
  * - [ ] With or without underscore: `gettype` vs `get_class`
  * - [ ] Plurals: `values_list` vs `value_list`
  * - [ ] Negatives: `button.enabled == True` vs `button.disabled == True`
- [ ] Emptiness: is there consistency in what means the empty value? Is it the best representation?
  * - [ ] Decide what will be used as empty: `None`, `False`, `[]`, `''`, `0`, etc.
  * - [ ] Be careful with unexpected falsy values: `bool(datetime.time(0)) == False` in Python < 3.5
- [ ] Parameters: is there consistency in the order of parameters?
  E.g. `datetime.datetime(year, month, day, minute, second, microsecond)` vs `datetime.timedelta(days, seconds, microseconds, milliseconds, minutes, hours, weeks)`
- [ ] Behavior: are similar behaviors represented similar and different behaviors represented differently? Asymmetry of behavior should reflect in asymmetry of form
  E.g. Good: `numbers.sort()` in-place vs `sorted(numbers)` not-in-place.


## Flexibility
- [ ] Reduce [integration discontinuities](https://mollyrocket.com/casey/stream_0028.html)
  * - [ ] Check if classes have a [single responsibility](https://en.wikipedia.org/wiki/Single_responsibility_principle). If not, break them into multiple classes
  E.g. a class to get/set items from/to a cache should separate the behavior to connect to the cache server into another class
  * - [ ] Check if function name has "and" or implies multiple operations. If so, consider breaking into multiple functions. However, keep the combined one if it's used by most clients:
  E.g. `print_formatted` function should be broken into two: `print` and `formatted`
  * - [ ] Check if the API client has to copy-and-paste code to change some behavior. Provide hooks, methods to be overridden, instead.
  * - [ ] Check if an attribute used internally in class behavior could be a `get_something` method
  E.g. on Django REST Framework, the CursorPagination class supported only a fixed `page_size`, because it didn't have the `get_page_size` method. This was [changed](https://github.com/encode/django-rest-framework/pull/3147) by introducing the method.
  * - [ ] Avoid hiding parameters that could be useful
  E.g. the API is calling another lower-level one, but it isn't exposing some useful parameters the lower-level API supports
  * - [ ] Return everything the API client might need
  * - [ ] Pass everything the API client might need when calling it's code (when [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control) is in place)
  * - [ ] Evaluate every `mock.patch` in the API tests. This indicates something isn't flexible to be changed at runtime. Consider having parameters to change those things
  E.g. Python's built-in [sched.scheduler](https://docs.python.org/3.6/library/sched.html) accepts `timefunc` and `delayfunc`, so the tests don't need to mock `time.monotonic` nor `time.sleep` and the clients can change those behaviors
- [ ] Create abstractions
  * - [ ] Wrap resources and functionalities common to multiple low-level implementations
  E.g. Beautiful Soup provides the same API for [multiple parsers](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-a-parser)
  * - [ ] Provide multiple abstraction levels, from the most simple to the most customizable
  E.g. Celery supports both the `@app.task` decorator and a [custom task class](http://docs.celeryproject.org/en/latest/userguide/tasks.html#custom-task-classes) that inherits from `celery.Task`
  * - [ ] Provide the ability to get out of the abstraction cage and use directly the abstracted resources and functionalities
  E.g. Django querysets support `.extra` to combine custom SQL with ORM-generated one. It also supports `.raw`, which allow completely raw SQL queries
  * - [ ] Wrap errors that are common to multiple low-level implementations and avoid leaking them directly to the API client
  E.g. a database connection error should be the same if the API supports multiple database engines. This helps API clients to change engines without changing much code
- [ ] Be Pythonic
  * - [ ] Use properties for simple getters and setters
  * - [ ] Use magic methods for operator overloading
  * - [ ] Use `__repr__` magic method for simpler debug
  * - [ ] Use with-statement contexts for open-close/start-end lifecycle
  * - [ ] Use decorators to easily compose common behaviors or register something
  * - [ ] Use iterators for lazy iterables
  * - [ ] Use generators to intertwine logic
  * - [ ] Use asyncio for async code
  * - [ ] Use built-in collections instead of custom repositories
  * - [ ] Accept simple high-order functions for simple Inversion of Control
  E.g. `list.sort` accepts `key` as the ranker function to calculate the order
  * - [ ] For complex flows, consider function/class pipelines, inheritance or generators, in that order.
  E.g. pipelines: [python-social-auth pipelines](python-social-auth), inheritance: [Django class-based views](https://docs.djangoproject.com/en/1.11/topics/class-based-views/), generators: [Scrapy spiders](https://doc.scrapy.org/en/latest/intro/tutorial.html)
  * - [ ] Respect [duck-typing and "Easier to ask for forgiveness than permission"](https://docs.python.org/3/glossary.html#term-duck-typing)
- [ ] Internationalize (I18N) the strings the end-user will see


## Safety
- [ ] Check for warning words like "warning", "careful", "remember to", "don't forget" in docs while describing a behavior. If they are there, consider changing behavior to be safer
- [ ] Detect common mistakes and use Python's built-in [warnings](https://docs.python.org/3.6/library/warnings.html) module to log warnings about them
- [ ] Require unsafe behavior to be explicit
  * Don't show all if something isn't set, e.g., `fields = None` shouldn't mean all fields
- [ ] Don't connect code implicitly by name or module. Use a registry or a registry-decorator
  * E.g. django-admin [registry](https://docs.djangoproject.com/en/1.11/ref/contrib/admin/), which supports both register by function or by decorator
- [ ] Avoid depending on order of method calls, try to use with-statement contexts instead
- [ ] Fail-fast: crap in crap out is not a Pythonic idea
  * - [ ] Raise `ValueError` if the library receives an invalid argument, like something that overflows, has a wrong format or is in a wrong state
  * - [ ] Raise `TypeError` if the library receives an argument with incompatible type, like a `duck` that doesn't `quack`. But don't do it inside a `if isinstance(duck, LibDuck)` or `if type(duck) == LibDuck`)! First try to call `quack`, then raise `TypeError` if it fails to give a clearer error

## Conclusion
- [ ] My API tries to make the simple easy, the complex possible and the wrong impossible
