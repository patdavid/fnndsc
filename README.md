# fnndsc

Notes and snippets for FNNDSC image presentation system.
The Github repo for these pages: https://github.com/patdavid/fnndsc.

The goal of the exercise is to find a way to present the image dataset in a web page<sup>1</sup>.

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
    This is already handled by the existing data structure.
2. JavaScript in the page code for control.  At the moment purely loading resources and displaying them.  
    This layer handles communication with the structured data source and responding to view events as needed (loading new images, changing views, etc).
3. View through the page.  
    This can be any fancy view system you want (React, Vue.js, vanilla/bespoke).  Needs to couple with the JS controller layers as appropriate.

This is really just a version of [Model-View-ViewModel](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel).



### JavaScript Control

Looking at the data, it would seem to make sense that the fundamental object under consideration is a **scan event**.
My limited understanding would seem to indicate that a scan event takes place and produces multiple sets of images for the event (in the case of sample MRI data it appears to be _Axial_, _Coronal_, and _Sagittal_).

If the scan event is the defining parameter for associated data, it would make sense to build the JavaScript objects around it.
The classes can be instantiated on a per-scan basis and contain the metadata for the scan event along with links to all of the image assets (or better, the images themselves).

Firing up a viewer page:

1. Load base list of all available assets (`map.json`?)
2. For each line of mapping, query the study ID/name.  
    This yr/mo/ex/name will define an asset path for an instance.
3. Create object for list + representative image of each scan event.
    This can be a an object that displays a list of yr/mo/ex/name + a thumbnail to the end user to pick on.  
    Note - it might make sense to create another spritesheet of representative images to keep network traffic lower and speed up rendering.
4. Listen for load requests from UI for specific studies.  
    When triggered, fire off a network request for further JSON data to load all images and metadata into study object.  
    Load states can be passed off to UI/UX components for updates and visualization (mutating Vue.js objects for instance).
    Basically, pass the images to the view (UI) layer.
5. (future thoughts) - possibly store loaded object data into IndexDB to offload large data and hopefully keep the app responsive, while minimizing latency and network I/O to retrieve data objects that were already loaded.  Cache API might be a good option as well (allows offline caching of big data objects).


### Examples

#### XMLHttpRequest

We can request JSON data from the data structures fairly easily using classical XMLHttpRequest objects in JavaScript.  
Here is a quick [**example xmlhttprequest**](example-xmlhttprequest/) with the data.


#### Vue.js Components

Thought I'd see what's going on in Vue.js land.  Quickly setup an example page with a couple of components tied to a
data store.  I load a list of images on page load and use an async factory to load all the images into the page.

Then use native Vue.js components and data binding to mutate the data when you scroll your mouse over the example image.
This is _not_ fancy in any way, just a quick proof of concept that it works as expected (I've never used Vue.js before, so it's possibly a little ugly).

This is extensible to just about any framework, Reach, Angular, Vue, etc...  The idea being to bind the model data to a view component in the page and manage events for interacting with those views (load new ones, scroll through image sets, or even push information out to an REST endpoint/api to trigger further processing on larger clusters and then retrieve the results locally).

Here is the [**example Vue.js**](example-vuejs/) page to play with.


#### Scan/Study Class Example

Fire up an example of a study class with some methods to demonstrate the base object to present to the rest of an app.


### Notes on existing data

The data model can be reached through mapping a pair of json files.
The first provides a data mapping for all of the YEAR/MONTH/EX breakdown, and is located in the root of the data directory as `map.json`.

```
00-yr/00-mo/0{1..4}-ex
```

This coincides with another JSON file, `ex.json`, located in the root of the month directories (ie: `00-yr/00-mo/ex.json`).
This provides data to map to name and `imageLocation`:

```
{
    "01-ex": {
        "name": "01-ex",
        "imageLocation": [
            "/var/www/html/rev/viewer/library-anon/./00-yr/00-mo/01-ex/AX_FSE_T2-AX_FSE_T2-231017/dcm2jpgRaw/middle-AX_FSE_T2-AX_FSE_T2-231017.jpg",
            "/var/www/html/rev/viewer/library-anon/./00-yr/00-mo/01-ex/CORONAL_REFORMAT-WIPmocoMEMPRAGE_FOV_160-229991/dcm2jpgRaw/middle-CORONAL_REFORMAT-WIPmocoMEMPRAGE_FOV_160-229991.jpg",
            "/var/www/html/rev/viewer/library-anon/./00-yr/00-mo/01-ex/AXIAL_REFORMAT-WIPmocoMEMPRAGE_FOV_160-229132/dcm2jpgRaw/middle-AXIAL_REFORMAT-WIPmocoMEMPRAGE_FOV_160-229132.jpg",
            ...
        ]
    }
}
```

From this JSON we can find each set of images by working on the URI path.  Stripping the leading webserver paths to coincide with our area of interest we can find something like:

```
/00-yr/00-mo/01-ex/AX_FSE_T2-AX_FSE_T2-231017
/00-yr/00-mo/01-ex/CORONAL_REFORMAT-WIPmocoMEMPRAGE_FOV_160-229991
/00-yr/00-mo/01-ex/AXIAL_REFORMAT-WIPmocoMEMPRAGE_FOV_160-229132
```

each of those locations should contain the sub-directories of images, but also the metadata information to coincide with them in the form of tag files:

```
tag-col.txt
tag-csv.txt
tag-dict.txt
tag-raw.txt
```


#### An Interesting Addendum on Image Information

I had done some work years ago on implementing something known as "Pseudogrey" by it's creator, Rich Franzen.

The idea is that you can mimic higher value tones in an image, like a 10-bit medical image, into the 8-bit image space (PNG, JPG) by "bit-stealing" and introducing _slight_ color into the images.  The idea was to do luminosity mapping of subtle ranges in order to expand the possibly values that can be displayed beyond 8-bit grayscale images.
This is something that could be done in the imagemagick commands fairly easily I think, and may be worth looking into?


## Design

UI/UX interaction (should) can be separated from the underlying data management and manipulation.
Interaction views should be components that can be mutated as needed with relevant data as requested.  This allows those components to also be re-used as required.


### Use

The problem requires some time to investigate and understand better a typical users patterns of interacting with the data, what their requirements are, and how they might expect things to work.
A thorough understanding of their requirements and habits will yield a far better guide for implementing the UI and directing the UX for maximum efficiency.








