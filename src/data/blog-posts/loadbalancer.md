---
title: Implementing a Load Balancer in Python
slug: my-first-post
description: Learn how to make a simple http load balancer in python
tags:
  - loadbalancer
published_at: 2024-05-31T13:58:46.857Z
last_modified_at: 2024-05-31T13:58:46.857Z
image: /assets/blog/imgg.png
---

# Creating an HTTP Load Balancer in Python

## Part 1: Theoretical Concepts

### Chapter 1: Theoretical Concepts

Chapter one mainly covers the theoretical concepts of load balancers: why we need them and their use cases.

In the end, you should be able to explain what a load balancer is and have developed a skeleton HTTP server with tests. We'll also discuss what Test-Driven Development (TDD) is along with the problem at hand and look at the tools that we'll be using to solve said problem.

#### What's a Load Balancer?

A load balancer is a networking component that's used for distributing network traffic across multiple servers in order to horizontally scale web-based applications. There are many popular ones out there such as Nginx, HAProxy, and Traefik, to name a few. We'll touch on open source load balancers in the final chapter of this course. Until then, we'll implement our own.

Why do load balancers play a big part in networking infrastructure? Because they allow engineers to scale and improve reliability of web applications.

Let's go over an example.

##### HTTP Load Balancer Example

As users visit our imaginary website that people purchase mangos from, `www.mango.com`, during peak time, one server can struggle to keep latency low while also staying available for traffic. The server itself has a finite amount of memory and CPU, so as traffic increases the single server starts to struggle. In order to cope with the additional traffic, we can add two more servers and front them with a load balancer.

```
HTTP Load Balancer Example
```

With the above architecture, the load is distributed across the servers and we can keep scaling by adding more servers or even start splitting functionality by adding microservices into the mix. In practice, the load balancer acts like a proxy fronting the underlying servers (or backends). As you continue working your way through this course, we'll go through more concepts related to load balancers and their usage.

#### What's TDD?

TDD is a development methodology that focuses on writing tests before writing your code. The benefit of this approach is that tests are not an afterthought but part of the thought process itself. I will not go into full detail on the benefits of this approach, so if interested please review the following resources:

- [Benefits of Test-Driven Development](https://www.google.com)
- [What is Test-Driven Development?](https://www.google.com)
- [Improving Code Confidently with Test-Driven Development](https://www.google.com)

Why use TDD here? Mainly to encourage good code quality. I personally really like the fact that the tests can be used as documentation and describe the behavior of the application in great detail.

### Approaching the Problem

An HTTP load balancer is an HTTP proxy server that handles HTTP requests on behalf of other servers. This is what we'll be developing throughout this course. To assist in that effort, we'll be using the following tools:

- Flask: A popular Python web framework.
- pytest: A testing framework.
- venv: Used for creating isolated Python environments.
- Make: An automation tool that generates executable and non-source files.
- Docker: A containerization tool designed to simplify the development and deployment of applications.

### Setting up the Environment

First, we need to install our dependencies:

- python3 and pip (or pip3)
- venv
- pytest
- Flask

For dependency management, we'll be using `venv` along with `pip`. Feel free to swap them out for Poetry or Pipenv. For more, review [Modern Python Environments](https://www.google.com).

Open up a bash shell and enter the following commands:

```bash
$ mkdir http-load-balancer
$ cd http-load-balancer
$ python3.9 -m venv env
$ source env/bin/activate

(env)$ python -V
Python 3.9.0

(env)$ pip install flask pytest
```

With the dependencies installed, let's create our first test.

Create a file called `test_loadbalancer.py`:

```python
import pytest

from loadbalancer import loadbalancer

@pytest.fixture
def client():
    with loadbalancer.test_client() as client:
        yield client

def test_hello(client):
    result = client.get('/')
    assert b'hello' in result.data
```

In the above code, we imported the `pytest` module along with the `loadbalancer` application from the `loadbalancer` module (neither exist yet). The fixture function defines a test client for us to use. This allows us to set up a test client once and can be used in future test functions. In the `pytest` framework, every function (and module/file) that starts with `test_` is identified as a test. We wrote a `test_hello` function that hits the root of our web application and expects the text 'hello' to be returned.

Run:

```bash
(env)$ python -m pytest
```

```
==================================================== test session starts =====================================================
platform darwin -- Python 3.9.0, pytest-6.1.2, py-1.9.0, pluggy-0.13.1
rootdir: /Users/michael/repos/testdriven/http-load-balancer
collected 0 items / 1 error

=========================================================== ERRORS ===========================================================
___________________________________________ ERROR collecting test_loadbalancer.py ____________________________________________
ImportError while importing test module '/Users/michael/repos/testdriven/http-load-balancer/test_loadbalancer.py'.
Hint: make sure your test modules/packages have valid Python names.
Traceback:
../../../.pyenv/versions/3.9.0/lib/python3.9/importlib/__init__.py:127: in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
test_loadbalancer.py:3: in <module>
    from loadbalancer import loadbalancer
E   ModuleNotFoundError: No module named 'loadbalancer'
================================================== short test summary info ===================================================
ERROR test_loadbalancer.py
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!! Interrupted: 1 error during collection !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
====================================================== 1 error in 0.11s ======================================================
```

As you can see, there's a big fat error! This is the spirit of TDD. Now, let's fix the error. It's complaining about the `loadbalancer` module that doesn't exist. We're going to create one as follows:

Create a file called `loadbalancer.py` in the `http-load-balancer` directory:

```python
from flask import Flask

loadbalancer = Flask(__name__)
```

Run the tests again:

```bash
(env)$ python -m pytest
```

```
==================================================== test session starts =====================================================
platform darwin -- Python 3.9.0, pytest-6.1.2, py-1.9.0, pluggy-0.13.1
rootdir: /Users/michael/repos/testdriven/http-load-balancer
collected 1 item

test_loadbalancer.py F                                                                                                 [100%]

========================================================== FAILURES ==========================================================
_________________________________________________________ test_hello _________________________________________________________

client = <FlaskClient <Flask 'loadbalancer'>>

    def test_hello(client):
        result = client.get('/')
>       assert b'hello' in result.data
E       assert b'hello' in b'<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">\n<title>404 Not Found</title>\n<h1>Not Found</h1>\n<p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>\n'
E        +  where b'<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">\n<title>404 Not Found</title>\n<h1>Not Found</h1>\n<p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>\n' = <Response 232 bytes [404 NOT FOUND]>.data

test_loadbalancer.py:14: AssertionError
================================================== short test summary info ===================================================
FAILED test_loadbalancer.py::test_hello - assert b'hello' in b'<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">\n<ti...
===================================================== 1 failed in 0.12s ======================================================
```

Now we get a 404 Not Found, which essentially means we do not have a `/` URL defined for our app.

Let's add a view function:

```python
from flask import Flask

loadbalancer = Flask(__name__)

@loadbalancer.route('/')
def router():
    return 'hello'
```

Here, we have a simple view function, which is a Python decorator with the URL as a parameter. When the client hits the URL, the `router` function will be executed.

Run the tests:

```bash
(env)$ python -m pytest
```

```
==================================================== test session starts =====================================================
platform darwin -- Python 3.9.0, pytest-6.1.2, py-1.9.0, pluggy-0.13.1
rootdir: /Users/michael/repos/testdriven/http-load-balancer
collected 1 item

test_loadbalancer.py .                                                                                                 [100%]

===================================================== 1 passed in 0.10s ======================================================
```

Hooray! We've made our first test pass!

That's it for the first chapter. We've gone through the theoretical concepts of why we need a load balancer and why we're using TDD to write one. We also created the