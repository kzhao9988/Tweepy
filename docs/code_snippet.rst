.. _code_snippet:


*************
Code Snippets
*************

Introduction
============

Here are some code snippets to help you out with using Tweepy. Feel
free to contribute your own snippets or improve the ones here!

OAuth
=====

.. code-block :: python

   auth = tweepy.OAuthHandler("consumer_key", "consumer_secret")
   
   # Redirect user to Twitter to authorize
   redirect_user(auth.get_authorization_url())
   
   # Get access token
   auth.get_access_token("verifier_value")
   
   # Construct the API instance
   api = tweepy.API(auth)

FollowAll
=========

This snippet will follow every follower of the authenticated user.

.. code-block :: python

   for follower in tweepy.Cursor(api.followers).items():
       follower.follow()

Handling the rate limit using cursors
=====================================
   
Since cursors raise ``RateLimitError``\ s while iterating,
handling them can be done by wrapping the cursor in an iterator.
   
Running this snippet will print all users you follow that themselves follow
less than 300 people total - to exclude obvious spambots, for example - and
will wait for 15 minutes each time it hits the rate limit.
   
.. code-block :: python
   
   # In this example, the handler is time.sleep(15 * 60),
   # but you can of course handle it in any way you want.
   
   def limit_handled(cursor):
       while True:
           try:
               yield next(cursor)
           except tweepy.RateLimitError:
               time.sleep(15 * 60)
   
   for follower in limit_handled(tweepy.Cursor(api.followers).items()):
       if follower.friends_count < 300:
           print(follower.screen_name)
