
[travis-url]: https://travis-ci.org/larsvoigt/epub-full-text-search
[travis-image]: https://travis-ci.org/larsvoigt/epub-full-text-search.svg?branch=master
[npm-url]: https://npmjs.org/package/epub-full-text-search
[npm-version-image]: http://img.shields.io/npm/v/epub-full-text-search.svg?style=flat

[![Build Status][travis-image]][travis-url] [![NPM version][npm-version-image]][npm-url] 

# EPUB-Search
## Search engine for EPUB3 documents 

EPUB-Search can be used to search in EPUB3-Documents.
 
You can make your whole cloud based epub3-book-collection content based searchable.

EPUB-Search uses [search-index](https://github.com/fergiemcdowall/search-index) 
to indexing the base content.

### Online Demo
[Demo](http://protected-dusk-3051.herokuapp.com/)

### Features included:

* Full text search (get all query matches for one epub-document or for a whole epub collection)  
* Instant search (provide suggestions)
* Full javascript
* Hits including [cfi](http://www.idpf.org/epub/linking/cfi/epub-cfi.html) references
* Returning result in JSON format

## Installation

For CLI use

```
[sudo] npm install epub-full-text-search -g
```

For library use

```
$ npm install epub-full-text-search --save
```

## Running as a Service 

**CLI**

```
$ epub-search 

Welcome to Epub search service

Usage: epub-search [action] [options]

Actions:
        start           Start the service
        stop            Stop the service
        logs            Show logs
        writeToIndex    Epub-book(s) which should be written to index.(Hint: the epub content have to be unzipped)

Options:
        -p      Path to epub folder which contains epub-book(s).

```

### Start Service

```
$ [sudo] epub-search start
```

### Indexing
Let´s start to index some epub-documents: 

```
$ epub-search writeToIndex -p  <path>
```

### Search 

Search for term:

```
http://localhost:8085/search?q=...
```
### Suggestions

Offering search suggestions 

```
$  http://localhost:8085/matcher?beginsWith=...
```

#### Expample:

At first, please install epub-search globally: 

```
[sudo] npm install epub-full-text-search -g
```

Start service: 

```
$ [sudo] epub-search start
```

Add sample epubs to index:

```
epub-search writeToIndex -p {prefix}/node_modules/epub-full-text-search/node_modules/epub3-samples
```

Now we can get some hits for the term ``epub``:

<sub>For requests you can use *$ curl -XGET "http://localhost:8085/search?q=math"* or the *browser*...</sub>

* Search in the whole indexed ebook-collection:

```
http://localhost:8085/search?q=math
```

* Set query filter book title ```t="..."``` to search only within a specific ebook:

```
http://localhost:8085/search?q=epub&t=Accessible+EPUB+3
```

* Or we can get some suggestions for instant searching:

```
http://localhost:8085/matcher?beginsWith=epu
```

## For library use

```javascript
import epubSearch from 'epub-full-text-search';
const options = {'indexPath': 'path_to_index-DB'}; // an own path can be set optional
epubSearch(options || {})
    .then((searchInstance) => {
        
        // INDEXING (write your **unzipped** EPUB3-Document to index)
        searchInstance.indexing('your_epub(s)_directory')
            .then((info) => {
               console.log(info);
            })
            .fail(function(err) {
                console.error(err);
            });
       
        // SEARCHING
        // search(query, epubTitle)
        searchInstance.search('epub', "Accessible EPUB 3")
            .then((results) => {
                console.log(results);
            })
            .fail(function(err) {
                console.error(err);
            });
            
        // COMPLEX SEARCHING
        // query(query, epubTitle)
        const search = 'epub';
        searchInstance.query({
            query: [
                {
                    AND: [
                        {'*': [search]},
                        {filename: ['accessible_epub_3']}
                    ]
                }
            ]
        }, search)
            .then((results) => {
                console.log(results);
            })
            .fail(function(err) {
                console.error(err);
            });
     
        // SEARCH SUGGESTIONS
        // match(beginsWith, epubTitle)
        searchInstance.match('matrix', 'A First Course in Linear Algebra')
            .then((results) => {
                console.log(results);
            })
            .fail(function(err) {
                console.error(err);
            });
    })
    .fail(function(err) {
        console.error(err);
    });

``` 
   

## Example


### Local testing 

Install all dependent modules: ``` npm install ```.

Start up the example ```npm run express-service```. It should run an express server on your local machine.

When navigating to [http://localhost:8080/](http://localhost:8080/) then you can see a test page where you can enter a search query.

Note: The indexing process starts automatically and it takes a few seconds until the search service is really available.    

