# fnndsc
Notes and snippets for FNNDSC image presentation system.

The goal of the exercise is to find a way to present the image dataset provided in a web page<sup>*</sup>.


<sup>*</sup> By "web page" I am assuming any sort of web-delivery technology utilizing a browser as the client-side renderer.
(Not that a properly architected solution like what's already been setup couldn't be leveraged in a native app as well.)


## Dataset

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
