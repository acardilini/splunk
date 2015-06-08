# My Splunk Cheat Sheet
This document will contain information about the use of Splunk.

## Resources
- Exploring Splunk: search processing language (SPL) primer and cookbook, found at 'https://www.splunk.com/web_assets/v5/book/Exploring_Splunk.pdf'

## General information
- Splunk uses perl syntax so there are plenty of '|'.
- This `sourcetype=syslog ERROR | top user | fields - percent` is an example of a search in Splunk, where the pipes separate the commands that make up the search. The first keyword after each pipe is that name of the search command, with an initial 'search' command always implied. So the command above says `search | top | fields`.


## Tips and tricks
- Explore what is happening by searching `cs_username=card`, my username, and exploring a course site. Record what logs are created when I do something.
- Clicking on the '>' in the 'i' column of a search result provides a detailed look at each of the fields returned by the log, it also provides the field name. This will be helpful when trying to determine which fields are of interest.
- Approximately every two minutes of a user sitting idle on d2l a log is created. I think this is an automatic refresh log, which could be helpful for identifying idle use (thought idle vs reading will be hard to determine).
> /d2l/activityFeed/checkForNewAlerts isXhr=true&requestId=3&X-D2L-Session=no-keep-alive

## Field description
