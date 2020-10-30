---
layout: post
title: [Holiday Workout]
subtitle: Hivestorm Practice
thumbnail-img: /assets/img/patrick_christmas.gif
tags: [blue]
---

# Holiday Workout Writeup

## Task:

Familiarization with flask and python webservers.

## Design Specifications:

```
The app must serve an initial page with your name on it at the home directory. It will then utilize RESTful API Design to act as a user database through serving the following pages.

Each user will have a name, role, and password

/:
READ - Return your name
CREATE - 405 Error - Method Not Allowed
UPDATE - 405 Error - Method Not Allowed
DELETE - 405 Error - Method Not Allowed

/users:
READ - Return a json list of users
CREATE - Creates a new user
UPDATE - Update the field of all users
DELETE - Delete all users
If there are no users, return an empty json

/users/NAME:
READ - Return json of a specific users information
CREATE - 405 Error - Method Not Allowed
UPDATE - Update a specific users information
DELETE - Delete a user
If a user does not exist, return a 404.

BONUS:
Create a page /admin with a fake flag which requires the password, secretpassword, to be stored in a cookie to access.
```

## Solve:

```python
from flask import Flask
from flask import abort
from flask import request
from flask import jsonify

app = Flask(__name__)

@app.route("/")
def index():
    if request.method == 'GET':
        return "Toadmo"
    else:
        abort(405)

users = [
    {
        "name": u"admin",
        "role": u"admin",
        "password" : u"admin",
    },
    {
        "name": u"edward",
        "role": u"cadet",
        "password": u"tang",
    }
]


@app.route("/users", methods = ['GET','POST','PUT','DELETE'])
def get_users_all():
    if request.method == 'GET':
        return jsonify({'users': users})
    elif request.method == 'POST':
        if not request.json or "name" not in request.json:
            abort(400)
        else:
            new_user = {
                'name': request.json['name'],
                'role': request.json['role'],
                'password': request.json['password'],
            }
            users.append(new_user)
        return jsonify({'user': new_user})
    elif request.method == 'PUT':
        for user in users:
            if request.json and 'name' in request.json:
                user['name'] = request.json.get('name', user['name'])
            elif request.json and 'role' in request.json:
                user['role'] = request.json.get('role', user['role'])
            elif request.json and 'password' in request.json:
                user['password'] = request.json.get('password', user['password'])
            else:
                abort(400)
        return jsonify(users)
    elif request.method == 'DELETE':
        users.clear()
        return jsonify({'users': users})
    else:
        abort(404)


@app.route("/users/<string:user_name>", methods = ['GET','PUT','DELETE'])
def get_user(user_name):
    if request.method == 'GET':
        user = [user for user in users if user["name"] == user_name]
        if len(user) == 0:
            abort(404)
        return jsonify({"name": user[0]})
    elif request.method == 'PUT':
        user = [user for user in users if user['name'] == user_name]
        if len(user) == 0:
            abort(404)
        if not request.json:
            abort(400)
        if 'name' in request.json and type(request.json['name']) != str:
            abort(400)
        if 'role' in request.json and type(request.json['role']) != str:
            abort(400)
        if 'password' in request.json and type(request.json['password']) != str:
            abort(400)
        user[0]['name'] = request.json.get('name', user[0]['name'])
        user[0]['role'] = request.json.get('role', user[0]['role'])
        user[0]['password'] = request.json.get('password', user[0]['password'])
        return jsonify({'user': user[0]})
    elif request.method == 'DELETE':
        user = [user for user in users if user['name'] == user_name]
        if len(user) == 0:
            abort(404)
        else:
            users.remove(user[0])
        return jsonify({'result':True})
    elif request.method == 'POST':
        abort(405)
    else:
        abort(404)

@app.route("/admin", methods = ['GET'])
def flag():
    if request.method == 'GET':
        password = request.cookies.get('password')
        if password == "secretpassword":
            return("dank")
        else:
            return("wrong password mang")
    elif request.method == 'PUT':
        abort(405)
    elif request.method == 'POST':
        abort(405)
    elif request.method == 'DELETE':
        abort(405)
    else: 
        abort(404)

app.run(debug=True)
```

## Extra Flask Notes:

Read More [Here](/assets/references/flask_guide.md)
