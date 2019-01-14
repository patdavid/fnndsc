# fnndsc

Notes and snippets for FNNDSC image presentation system.

The goal of the exercise is to find a way to present the image dataset provided in a web page<sup>1</sup>.


<sup>1</sup> By "web page" I am assuming any sort of web-delivery technology utilizing a browser as the client-side renderer.
(Not that a properly architected solution like what's already been setup couldn't be leveraged in a native app as well.)


## Dataset

**Note:** Currently, a fresh clone of the fnndsc data repo (on Ubuntu 18.04) and following the instructions trips an error during processing JPG files using `pfdicom_rev`.  Details of the error [are documented on a separate page](pfdicom_rev-error.md).

From the provided dataset the data is presented in a structured format, sorted by age in a `00-yr/00-mo/subj-N-NNNN-ex` structure.
Pre-processing of the dataset also includes JSON data nested into the data structure to provide metadata.
In the sample data for instance, the `map.json` file provides a mapping of all sub-directory data (http://fnndsc.childrens.harvard.edu/rev/viewer/library-anon/map.json)

Sample JSON data:

```
{
    "data": [
        "./00-yr/00-mo/01-ex",
        "./00-yr/00-mo/02-ex",
        "./00-yr/00-mo/03-ex",
        ...
    ]
}
```

The JSON data can be modified on the server/generation side to extend the data being delivered.
The benefit of serving JSON data.

The sample dataset from the github repo also provides more JSON files alongside existing image data to provide further information.


## Process Architecture

This is a classical decoupling of model data where the domain looks like:

1. Model data (images/metadata) in a structured directory format, with JSON metadata available.
    This is already handled by the data structure.
2. Javascript in the page code for control.  At the moment purely loading resources and displaying them.
    This layer handles communication with the structured data source and responding to view events as needed (loading new images, changing views, etc).
3. View through the page.
    This can be any fancy view system you want (React, Vue.js, vanilla/bespoke).  Needs to couple with the JS controller layers as appropriate.



One approach to the problem becomes readily apparent when considering the domain:

1. Assets for the system are accessible through REST API calls against the webserver.
    That is, all of the created assets will be accessible through network calls against the webserver.

    Even if there isn't a formal API set up to handle more advanced requests the base capability of querying and parsing JSON files from the filesystem might be enough for this example.  That is, if we wanted to access a specific JSON resource, we can do a simple XMLHTTPRequest for it.

    TODO: Demonstrate a simple XMLHTTPRequest against http://fnndsc.childrens.harvard.edu/rev/viewer/library-anon/00-yr/00-mo/ex.json
    [example xmlhttprequest](example-xmlhttprequest/).


## Design
