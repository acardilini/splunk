# My Splunk Cheat Sheet
This document will contain information about the use of Splunk.

## Resources
- Exploring Splunk: search processing language (SPL) primer and cookbook, found at 'https://www.splunk.com/web_assets/v5/book/Exploring_Splunk.pdf'

## General information
- Splunk uses perl syntax so there are plenty of '|'.
- This `sourcetype=syslog ERROR | top user | fields - percent` is an example of a search in Splunk, where the pipes separate the commands that make up the search. The first keyword after each pipe is that name of the search command, with an initial 'search' command always implied. So the command above says `search | top | fields`.

- When one goes to a new site from the CloudDeakin home page it can cause the submission of multiple log files because the browser is loading the elements contained within the page. Each one of these actions, e.g. loading an image, will result in the submission of a log file


## Tips and tricks
- Explore what is happening by searching `cs_username=card`, my username, and exploring a course site. Record what logs are created when I do something.

- Clicking on the '>' in the 'i' column of a search result provides a detailed look at each of the fields returned by the log, it also provides the field name. This will be helpful when trying to determine which fields are of interest.

- Approximately every two minutes of a user sitting idle on d2l a log is created. I think this is an automatic refresh log, which could be helpful for identifying idle use (thought idle vs reading will be hard to determine). If the window that d2l is open in is closed a final log with information will be submitted, giving an indication of exit time. The fields are for `cs_uri_query` and `cs_uri_stem` respectively and will have the following values.
> /d2l/activityFeed/checkForNewAlerts isXhr=true&requestId=3&X-D2L-Session=no-keep-alive

- The field `cs_uri_stem=` defines the URL that the person is using, for instance if one simply searches for `https://d2l.deakin.edu.au/d2l/home` the log will contain `/d2l/home`. This changes depending on the way that individuals access the home URL, for instance if one uses the the back button to get to the CloudDeakin home page from within a course site this field will be different, and the `cs_uri_query=` field actually identifies where one has come from with the 'ou=XXXXX' information.
> /d2l/common/language/image.d2l ou=96427&isi=0&iv=10.3.0.791-92&collection=shared.main.

- When entering a site and opens multiple elements on the page, the `cs_uri_stem=` of these elements will begin with `/d2l/lp`, which I think means 'load page'.

- When entering a site, the `ou=` value will be contained within the `cs_uri_stem=` URL for navbars and pages, and these elements will NOT have a `cs_uri_query=` field. On the other hand widgets will have a `cs_uri_query=` and an `ou=` field which contain the sites `ou=` value.



## Field description
- `cs_uri_stem=` specifies the URL actually used by the client.

- `ou=` specifies a unique identityer for the different d2l sites.

## Important values

## Common Queries

>cs_username=card NOT (cs_uri_stem=/d2l/activityFeed/checkForNewAlerts OR cs_uri_stem=/d2l/lp* OR cs_uri_stem=/d2l/common* OR  cs_uri_stem=/Shibboleth* OR cs_uri_stem=/d2l/shibboleth*)
