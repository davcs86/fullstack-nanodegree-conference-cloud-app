App Engine application for the Udacity training course (participant: davcs86)

## Products
- [App Engine][1]

## Language
- [Python][2]

## APIs
- [Google Cloud Endpoints][3]

## Setup Instructions
1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
1. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][4].
1. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
1. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
1. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting
   your local server's address (by default [localhost:8080][5].)
1. Generate your client library(ies) with [the endpoints tool][6].
1. Deploy your application.

## CHANGELOG

### Aplication on 

[https://moonlit-app-120817.appspot.com/](https://moonlit-app-120817.appspot.com/)
API explorer: [https://apis-explorer.appspot.com/apis-explorer/?base=https://moonlit-app-120817.appspot.com/_ah/api#](https://apis-explorer.appspot.com/apis-explorer/?base=https://moonlit-app-120817.appspot.com/_ah/api#)

### Task 1: Add Sessions to a Conference

API endpoints
**getConferenceSessions(websafeConferenceKey)** -- Given a conference, return all sessions

**getConferenceSessionsByType(websafeConferenceKey, typeOfSession)** Given a conference, return all sessions of a specified type (eg lecture, keynote, workshop)

**getSessionsBySpeaker(speaker)** -- Given a speaker, return all sessions given by this particular speaker, across all conferences

**createSession(SessionForm, websafeConferenceKey)** -- open only to the organizer of the conference

**Design considerations:**

1. Created an Entity for Session
   ```python
   class ConfSession(ndb.Model):
       """Session -- Session object"""
       name = ndb.StringProperty()
       highlights = ndb.StringProperty()
       speakerId = ndb.StringProperty()
       duration = ndb.TimeProperty()
       typeOfSession = ndb.StringProperty(default='NOT_SPECIFIED')
       date = ndb.DateProperty()
       start_time = ndb.TimeProperty()
   ```

1. Created an Entity for Speaker
   ```python
   class ConfSpeaker(ndb.Model):
       """Speaker -- speaker object"""
       displayName = ndb.StringProperty()
       confSessionKeysToAttend = ndb.StringProperty(repeated=True)
   ```

1. The sessions has as parent a conference (because the relationship between Conferences and sessions is 1:n in the model)
   ```python
   p_key = conf.key // where conf = ndb.Key(urlsafe=request.websafeConferenceKey).get()
   c_id = ConfSession.allocate_ids(size=1, parent=p_key)[0]
   c_key = ndb.Key(ConfSession, c_id, parent=p_key)
   ```
   this facilitates a common query __getConferenceSessions__

1. Sessions can have one speaker, but these speakers can have many sessions. Thus, the first relationship is stored in
   ```python
   class ConfSession(ndb.Model):
       # ...
       speakerId = ndb.StringProperty()
   ```
   and the second one is in a list of sessions keys in 
   ```python
   class ConfSpeaker(ndb.Model):
       # ...
       confSessionKeysToAttend = ndb.StringProperty(repeated=True)
   ```

[1]: https://developers.google.com/appengine
[2]: http://python.org
[3]: https://developers.google.com/appengine/docs/python/endpoints/
[4]: https://console.developers.google.com/
[5]: https://localhost:8080/
[6]: https://developers.google.com/appengine/docs/python/endpoints/endpoints_tool
