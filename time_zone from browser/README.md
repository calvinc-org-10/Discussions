Eventually, I want my code to honor the time zone of the browser where it is being run.  I've put in the first hooks in WICS (the system that pays my rent at the moment).

The stack is javascript and django

Basically, the way it works is this:
1. when a user logs in, capture the time zone from the browser
2. in the login view, set the django setting TIME_ZONE from the captured value

While I was modifying login.py and login.html, I strengthened the code to let me login in easily in development mode.  I won't explain that code in this post; the code is here, but the explanation's for another time.
