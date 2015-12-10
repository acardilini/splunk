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
- `cs` in any field stands for client-to-server action.

- `sc` in any field stands for server-to-client action.

- `time=` is self evident and should always be included in any query. This will help determine the sequence of events.

- `date=` is self evident and should always be included in any query. This will help determine the sequence of events.

- `c_ip=` specifies an ip address, client ip. This will be helpful when trying to identify the stream of a single individual but will need to changed to de-identify the data.

- `s_ip=` specifies an ip address, server ip.

- `sourcetype=` specifies the input file format of the log file, 'iis' the most common sourcetype in the d2l logs stands for Internet Information Systems.

- `cs_uri_stem=` specifies the URL actually used by the client.

- `ou=` specifies a unique identityer for the different d2l sites.

## Important values
- `cs_uri_stem=`
  - `/d2l/activityFeed/checkForNewAlerts`, is a log based on the idleness of a user. This is returned every two minutes and records an automatic page refresh submission.
  - `/d2l/lp/ouHome/home.d2l`, is a log that is submitted when one clicks on a course site link from the D2L homepage.
  - `/d2l/home/XXXXXX` specifies the homepage of a site, the x's is the ou value for a the site.
  - `/d2l/common/viewContentFile.d2lfile` specifies page content that is loaded upon an individual entering a site.
  Logs that contain this value will also have a `cs_uri_query=` field that identifies the object being loaded. For instance many of these are loaded in ITCH, one for each staff profile.
  - `/d2l/lp/navbars/*` specifies navigation bars that are loaded within a page upon loading the page.
  - `/d2l/le/content/XXXXXX/viewContent/YYYYYYY/View` specifies looking at content within site X and the content has a unique identifyer of Y. This is logged before a log that specifies the page title within the `cs_uri_query=`, but which has a generic `/d2l/common/viewContentFile.d2lfile`.
  - `/d2l/lms/news/main.d2l` indicates that someone has accessed the news section of the site.
  - `/d2l/lms/discussions/ajax/ajaxFunctions.d2lfile` indicates accessing a discussion board.


- `cs_uri_query=`
  - Within this string files that are considered resources will include a section in their directory called 'resource-working-files/', this will be followed by the names of the file, e.g. 'My+Portfolio.html'. These files will also begin with the string '/content'.
  `/content/enforced/267060-PS_128-0510-Ongoing/resource-working-files/Computer+and+Library+Resources.html`
  - `/content/enforced/267060-PS_128-0510-Ongoing/resource-working-files/Profiles.html`, the section at the end 'Profiles.html' seems to indicate if someone has clicked on the read more button for the news widget.

- `ou=267060&postId=2072899&topicId=293697` when an individual goes into a discussion topic it will provide the following log, which indicates the postId and topicId. These numbers describe a unique identifier for each post.



## Course site ou
- Information Technology Course Hub = 267060



## Common Queries
- To identify all indiviuals who have accessed the IT Course Hub we can use the following code which reads `search for ITCH site | group records by username | return username from each grouped record`.
> cs_uri_stem=/d2l/home/267060 | transaction cs_username | table cs_username

- Alternative, to find all logs within d2l that have been produced by users who have visited the IT course hub we do, `search for logs in d2l that include [all individuals who have accessed the IT Course Hub]`
> cs_uri_stem=/d2l* [search cs_uri_stem=/d2l/home/267060 | transaction cs_username | table cs_username]

- To get a count of the number of visits for each hour by each user. If you remove the 'date_hour' argument it will give a count of each users visits over the time specified:
>cs_uri_stem=/d2l/home/267060 | stats count by cs_username, date_hour

- This search returns all logs with username 'card' that don't include fields with the values expressed in the brackets.
>cs_username=card NOT (cs_uri_stem=/d2l/activityFeed/checkForNewAlerts OR cs_uri_stem=/d2l/lp* OR cs_uri_stem=/d2l/common* OR  cs_uri_stem=/Shibboleth* OR cs_uri_stem=/d2l/shibboleth*)

- To identify all users using a course site and then search all of their D2L use, see http://answers.splunk.com/answers/121489/extract-user-list-and-use-in-next-query.html

- Search for all users who accessed the homepage `cs_unsername="*" cs_uri_stem=/d2l/home/XXXXXX`
