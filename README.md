# ChatApp  #

![](http://g.recordit.co/JYruQDLd0h.gif)

A small functional person-to-person message center application built using Django.
It has a REST API and uses WebSockets to notify clients of new messages and 
avoid polling.

## Architecture ##
 - When a user logs in, the frontend downloads the user list and opens a
   Websocket connection to the server (notifications channel).
 - When a user selects another user to chat, the frontend downloads the latest
   15 messages (see settings) they've exchanged.
 - When a user sends a message, the frontend sends a POST to the REST API, then
   Django saves the message and notifies the users involved using the Websocket
   connection (sends the new message ID).
 - When the frontend receives a new message notification (with the message ID),
   it performs a GET query to the API to download the received message.

## Scaling ##

### Requests ###
"Because Channels takes Django into a multi-process model, you no longer run 
everything in one process along with a WSGI server (of course, you’re still 
free to do that if you don’t want to use Channels). Instead, you run one or 
more interface servers, and one or more worker servers, connected by that 
channel layer you configured earlier."

In this case, I'm using the In-Memory channel system, but could be changed to
the Redis backend to improve performance and spawn multiple workers in a
distributed environment.

Please take a look at the link below for more information:
https://channels.readthedocs.io/en/latest/introduction.html




## Assumptions ##
Because of time constraints this project lacks of:

- User Sign-In / Forgot Password
- User Selector Pagination
- Good Test Coverage
- Better Comments / Documentation Strings
- Frontend Tests
- Modern Frontend Framework (like React)
- Frontend Package (automatic lintin, building and minification)
- Proper UX / UI design (looks plain bootstrap)

## Run ##

0. move to project root folder


1. Create and activate a virtualenv (Python 3)
```bash
python3 -m venv env
```
2. Install requirements
```bash
pip install -r requirements.txt
```

3. Start Redis Server
```bash
redis-server
```

4. Init database
```bash
./manage.py migrate
```
5. Run tests
```bash
./manage.py test
```

6. Create admin user
```bash
./manage.py createsuperuser
```

7. Run development server
```bash
./manage.py runserver
```

To override default settings, create a local_settings.py file in the chat folder.

Message prefetch config (load last n messages):
```python
MESSAGES_TO_LOAD = 15
```
