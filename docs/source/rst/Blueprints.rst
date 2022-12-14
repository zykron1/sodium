Blueprints
==========
Imagine you have a route, lets say /signup. That route requires 3 things username, email, and password.
Now we will asume you can save the user to the database by running the create_user(username, email, password).
This may seem simple, but this is where complicatons arise. The problem is that you need to get them user input.
Now without any special tools, you would have to check if the user provided a username and a email and a password.
It's common knolage among developers to follow the Don't Repeat Yourself(DRY) design pateren and all of these checks
violate these rules.

What The Hell Is A Blueprint?
=============================
A blueprint will alow yourself to always get the request in the format **you** define, and handles all the checks for
you.

How to create Blueprints
========================
Ok, enough of the lame design paterens and theory, lets actualy use it.

For **Linux or Mac OS** users:

.. code-block:: sh

   python3 -m libsodium create blueprint signup 

For **Windows** users:

.. code-block:: sh

   python -m libsodium create blueprint signup

.. NOTE::
   The name of the blueprint dosn't have to be the name of the route, though it's **best practice**.


Now that we have created our blueprint, lets take a peek inside:

**/src/blueprints/signupBlueprint.py:**

.. code-block:: python

   from libsodium import Blueprint

   signupBlueprint = Blueprint([
   ('example',str)
   ])

Basicly the tuple is called a rule, and the list is the list of rules.
For our usecase it should look something like this:

.. code-block:: python

   from libsodium import Blueprint

   signupBlueprint = Blueprint([
   ('username', str)
   ('email', str, r"(?:[a-z0-9!#$%&'*+/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+/=?^_`{|}~-]+)*|"+'"'+"(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21\x23-\x5b\x5d-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])*"+'"'+")@(?:(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?|\[(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?|[a-z0-9-]*[a-z0-9]:(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21-\x5a\x53-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])+)\])")
   ('password', str)  
   ])

.. NOTE::
   The regex used for the email can be found `here <https://www.emailregex.com/>`_

Using The Blueprint
===================
Now lets add the blueprint to our route. We can do this by using the useBlueprint decorator.

.. NOTE::
   **libsodium.classes.useBlueprint(Blueprint blueprint, List[str,..] mimetypes)**

Code:

.. code-block:: python

   from libsodium import Route, Response, useBlueprint
   from src.blueprints.postBlueprint import postBlueprint

   def route():
       @useBlueprint(postBlueprint, ["applicaiton/json"])
       class signup:
           def onRequest(self, request):
               create_user(request.json["username"], request.json["email"], request.json["password"])
               rsp = Response(f'{"code":"success"}')
               rsp.headers['Content-Type'] = 'applicaiton/json'
               return rsp
       return Route('POST', '/signup', signup)

And just like that, we have blueprints installed.
