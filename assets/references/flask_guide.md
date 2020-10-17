# Flask Guide:

## Opening venv
- cd venv
- source bin/activate

## If Address Used
- ps -fA | grep python
- kill -9 pid

## Requests With Curl
 - POST: curl -H "Content-Type: application/json" -d '{"name":"dog","role":"pet","password":"cool"}' http://localhost:5000/users
    - -d implies POST
- PUT: curl -H 'Content-Type: application/json' -X PUT -d '{"name":"cat"}' http://localhost:5000/users

## Setting Cookies
- curl -b 'password=secretpassword' http://localhost:5000/admin

## 405 Errors
- By not defining any other methods than the ones used, 405 is automatically given when a different method is used in CRUD

## Cookies Guide:
- request.cookies: give the cookies
- a = make_reponse('response'): give response on the site as text
- a.set_cookie('cookiename', 'cookievalue', max_age = seconds)

## Resources
https://blog.miguelgrinberg.com/post/designing-a-restful-api-with-python-and-flask
