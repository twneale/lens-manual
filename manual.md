Disclaimer: This documentation is a work-in-progress. However, it reflects the latest state of Lens development and provides documentation for the new [0.2.x](https://github.com/elifesciences/lens/tree/0.2.x) series of Lens. You can contribute to this manual by making changes to the source [markdown file](https://github.com/elifesciences/lens-manual/blob/master/manual.md).

# Introduction

[eLife Lens](http://elifesciences.org/lens) provides a novel way of looking at content on the web. It is designed to make life easier for researchers, reviewers, authors and readers. For example, have you tried to look at a figure in an online article, while at the same time trying to see what the author says about the figure, jumping all around the article, losing track of what you were looking for in the first place? The reason for this is that most online research articles are published in a fixed digital version of the original paper. With eLife Lens, we take full advantage of the Internet’s flexibility.

## The Big Picture

Lens has a pretty simple architecture. It is a stand-alone web application that can easily be hosted on any webserver. Every Lens instance can be configured to view arbitrary content, simply by specifying a URL to a Lens Library, which has a bunch of documents, organized in collections. Lens Libraries as well as Lens Articles are stored as JSON. You'll learn more about these formats in later chapters. What's important to note is that Lens follows a distributed architecture for content, which means Lens content (libraries and articles) can be hosted anywhere. It doesn't require one big centralized repository that has all the content. Instead, anyone (even authors) can host their own documents and libraries.

![](http://f.cl.ly/items/2f3Q120T2g2f3k3f3V2V/lens-big-picture.png)

The figure above shows independent Lens instances from two OA publishers, eLife and Landes Bioscience, which have already adapted Lens for their content. There's another instance at `lens.substance.io`, which always has the latest Lens release and connects to its own library, which holds all Lens-related documentation like the one you are currently reading.

## The Lens Article Format

The Lens Article Format is an implementation of the [Substance Document Model](http://github.com/substance/document) dedicated to scientific content. It features basic content types such as paragraphs, headings, and various figure types such as images, tables and videos complete with captions and cross-references.

The document definitions are easily extended, so you can either create your own flavour or contribute to the Lens Article Format directly. We have auto-generated documentation for the latest [Lens Article spec](#lens/lens_article).

Do we really need another spec for scientific documents?  

We believe so for the following reasons:

- XML-based formats such as NLM are hard to consume by web clients.
- Strict separation of content and style is important. Existing formats target print, and thus contain style information which makes them hard to process by computer programs.
- The greatest advantage of Lens Articles is that any of them can be viewed in Lens, a modern web-based interface for consuming science content.


### Nodes

Lens articles are data-centric representations of digital content. Each content element lives as a node in a flat address space, identified by a unique id. Think of it as a database of independent content fragments.

The following graphic shows a sample document containing a heading (`h1`), paragraph (`p1`), and formula (`f1`). It also has an image (`i1`) and a table (`t1`) as well as two citations (`c1` and `c2`).

![](http://f.cl.ly/items/060x2w1f2r1A3y3D3z2w/lens-document-nodes.png)


### Views

Now these building blocks of a document are organized using views. The main body of the document is referenced in the `content` view. Figures (like images and tables) are kept in the `figures` view and citations live in `citations` respectively.

![](http://f.cl.ly/items/0J3m3D3Z2u3E292A1j3T/lens-document-views.png)


## The Lens Library

With Lens 0.2.x we're proposing a simple interface for specifying Lens Libraries that can be organized using collections. If you would like to make your own content available to Lens, all you have to do is follow the Lens Library specification. See chapter "Publish your own Lens Library" to learn the details.

![](http://f.cl.ly/items/1m0b3V2L1K2g3V282c2a/Screen%20Shot%202013-08-26%20at%205.27.08%20PM.png)


# Installation

It's fairly easy to install and run the latest Lens development version locally.

## Prerequisites

- Node.js >=0.8.x
- [Pandoc](http://johnmacfarlane.net/pandoc/installing.html) >= 1.11.x (for on-the-fly generation of the Lens manual from Markdown)

Node.js is just used as a development environment. You'll soon be able to create self-contained packages of individual modules or the main app itself.

## Fresh install

First install the Substance Screwdriver command line utility. It's just a little helper that helps you deal with our many modules.

    $ git clone https://github.com/substance/screwdriver.git
    $ cd screwdriver
    $ sudo python setup.py install


Clone the Lens Mothership

    $ git clone https://github.com/elifesciences/lens.git
  
Run the update command, which pulls in all the sub-modules and dependencies

    $ cd lens
    $ substance --update
  
Finally start the server

    $ substance

You can have a look at the example document, by pointing your browser to `http://localhost:4000/#lens/lorem_ipsum`, view the autogenerated Lens.Article documentation here `http://localhost:4000/#lens/lens_article` or the manual `http://localhost:4000/#lens/manual`.
   
## Keep your local version in sync

You may want to pull in updates every now and then, which is simple. In the Lens project root dir do:

    $ substance --update
   
And start the dev environment again.

    $ substance



# Contributing

I'm assuming here that you have push access to the repositories, because as a start I'd like to get the Lens core dev team up and running. I'll provide documentation on how to work with a forked version of a module and submit a pull request soon.


Say you've made changes to the Lens.Article module. In order to commit them you simply have to navigate to `node_modules/lens-article` and do:

    $ git add <YOUR STUFF>
    $ git commit -m "Fixed X"
    $ git push
   
Alternatively, if you are working on breaking changes you can switch to a different branch, and submit a pull request using the Github interface. Here's how:

    $ git checkout -b my_feature_branch
    $ git add <YOUR STUFF>
    $ git commit -m "Fixed X"
    $ git push

Then go to [Github](http://github.com) and submit a pull request.


Another hint: To pull in upstream changes from master for the entire project do this:

    $ substance --git -- pull origin master:<feature_branch_name>


## Publish your own Lens Library

Lens always reads a JSON file from a URL that describes a library. It's completely up to you how you want to host your Lens libraries. You can put them on Amazon S3 as static files or use some server infrastructure that serves the library on the fly. Please note that libraries are completely decoupled from documents. Libraries just have document records which reference a Lens document using a URL. Documents don't need to live on the same server whatsoever. With Lens we favor a decentralized strategy for document hosting. Publishers should be able to have complete control of their hosted content. You can put documents wherever you like, too.


Supposing you'd like to host your pets library here `http://myserver.com/pets.json`, the contents must look like so:

    {
      id: "pet_library",
      "nodes": {
        "library": {
          "name": "My Pets",
          "type": "library",
          "collections": ["cats", "dogs"]
        },
        "cats": {
          "id": "cats",
          "type": "collection",
          "records": ["oliver", "minkie"]
        },
        ...
        "oliver": {
          "id": "oliver",
          "title": "Oliver the tomcat",
          "type": "record",
          "url": "http://docserver.com/pets/oliver.json"
        },
        "minkie": {
          "id": "minkie",
          "title": "Minkie the cat",
          "type": "record",
          "url": "http://docserver.com/pets/minkie.json"
        }
      }
    }

If you'd like to make your corpus of science documents available to Lens, you might want to have a look at [Refract](http://github.com/elifesciences/refract), our reference implementation of a NLM to Lens converter. Refract can be seeded with a list of NLM XML files, which are converted one by one and cached in memory for fast access of the converted JSON. Once seeding has completed, your running Refract forms a valid Substance Library that you can view in Lens. All you have to do is change the library_url to `http://localhost:1441/documents` in your Lens `index.html`:

    var app = new Lens({
      env: 'development',
      library_url: 'http://localhost:1441/documents'
    });

## Adjusting styles

Most customization can be done using CSS without interfering with the Lens codebase. You only override styles on the application level.

Say you'd like to like to color citation cards red.

    #my_app .article .citations .resource-header {
      background: red;
    }

You've got the basic idea, now it's on you. It should be easy to pull in changes from the official Lens modules, without breaking your app.

## Implement a new node type

It doesn't take much to implement a new node type for Lens. However, consider carefully whether there isn't an existing type that will meet your need. We would prefer that you adjust existing types and contribute back. If we'd all figure out a way to find common ground in our scientific language that would be awesome. :)

Anyway, let's create a new node type now. For the purpose of demonstration only, let's create a `cat` node type that you can reference in the main body of the document. The first thing to do is create a new folder in the `lens-article` repository  `/nodes/cat`. The structure of that folder will be like this:

    index.js
    cat.js
    cat_view.js
    cat.css

Let's start by specifying the type definitions in `cat.js`. 

    var _ = require('underscore');
    var Node = require('../node');

    var Cat = function(node, doc) {
      Node.call(this, node, doc);
    };

    // Type definition
    // -----------------
    //

    Cat.type = {
      "id": "cat",
      "parent": "figure",
      "properties": {
        "name": "string",
        "speed": "string",
        "abilities": ["array", "string"],
      }
    };

    Cat.prototype = Node.prototype
    Cat.prototype.constructor = Cat;


    // Auto-generate property getters based on the type definition 
    // --------

    var getters = {};

    _.each(Cat.type.properties, function(prop, key) {
      getters[key] = {
        get: function() {
          return this.properties[key];
        }
      };
    });


    Object.defineProperties(Cat.prototype, _.extend(getters, {
      caption: {
        // Used for 
        heading: function() {
          return this.properties.
        }
      }
    }));

    module.exports = Cat;


That was easy. Now we need to implement a view for the `Cat` model we just defined. Here is `cat_view.js`.

    var NodeView = require("../node").View;

    var CatView = function(node) {
      NodeView.call(this, node);

      this.$el.attr({id: node.id});
      this.$el.addClass("content-node cat");
    };

    CatView.Prototype = function() {

      this.render = function() {
        NodeView.prototype.render.call(this);
        var node = this.node;

        var html = [
          '<div class="name">'+this.cat+'</div>'
          '<div class="speed">'+this.speed+'</div>'
          '<div class="abilities">'+this.abilities.join(', ')+'</div>'
        ].join('\n');

        this.content.innerHTML = html;

        return this;
      }
    };

    // CatView inherits from NodeView
    CatView.Prototype.prototype = NodeView.prototype;
    CatView.prototype = new CatView.Prototype();

    module.exports = CatView;

Add some styles in `cat.css`:

    .content-node.cat .name {
      font-size: 20px;
      color: green;
    }

By convention, there needs to be an `index.js` file in the repo.

    var Cat = require('./cat');
    Cat.View = require('./cat_view');
    module.exports = Cat;


Finally you have to register your new node type in `nodes/index.js`.

    module.exports = {
      ...
      "cat": require("./cat"),
      ...
    };


Now in your actual document, you can specify and reference `cat` resources. It looks like so:

    {
      id: "example_doc",
      "nodes": {
        ...
        "oliver": {
          "id": "oliver"
          "type": "cat",
          "name": "Oliver",
          "abilities": ["jump high", "eat much", "bite", "hunt mice"],
        },
        "paragraph_1": {
          "id": "paragraph_1",
          "type": "paragraph",
          "content": "Last sunday I had much fun with Oliver the cat."
        },
        "figure_reference_oliver": {
          "id":"figure_reference_oliver",
          "type":"figure_reference",
          "path": ["paragraph_3", "content"],
          "target": "oliver",
          "range":[31,37]
        },
        ...
      }
    }
