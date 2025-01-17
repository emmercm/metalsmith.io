---
title: "Metalsmith"
description: "An extremely simple, pluggable static site generator."
slug: "home"
layout: "home.njk"
config:
  anchors: true
  
highlights:
  - trait: Convenient
    icon: pointright
    description: 'Metalsmith works with all the tools and data formats you already know and use: NodeJS, npm, markdown, json, yaml and the templating language of your choice.'

  - trait: Simple
    icon: lightbulb
    description: Metalsmith translates a directory tree to plain Javascript objects that you can manipulate effortlessly with your selection of plugins.

  - trait: Pluggable
    icon: gridlines
    description: You shouldn't have to bend your project needs to a specific framework or tool. Metalsmith gives you full control of how you want to conceptualize, structure and build your project.

  - trait: Versatile
    icon: expand
    description: 'Use Metalsmith to generate anything from a static site, to a scaffolder, backup, command-line, or deploy tool. Configuration over code or code over configuration: Metalsmith supports both.'
---
{% include "./lib/views/partials/doc-mdlinks.njk" %}

<section class="Highlight-wrapper">
{% for item in highlights %}
  <div class="Highlight-item Highlight">
    <div class="Highlight-content">
      <i class="Highlight-icon ss-{{item.icon}}"></i>
      <h2 class="Highlight-title">{{ item.trait }}</h2>
      <p class="Highlight-desc">{{ item.description }}</p>
    </div>
  </div>
{% endfor %}
</section>

## Welcome

You want to build a website or blog with a static site generator. Well, here is our elevator pitch. It's as easy as that:

`metalsmith.js`
```javascript
const Metalsmith  = require('metalsmith');
const collections = require('@metalsmith/collections');
const layouts     = require('@metalsmith/layouts');
const markdown    = require('@metalsmith/markdown');
const permalinks  = require('@metalsmith/permalinks');

Metalsmith(__dirname)         // __dirname defined by node.js:
                              // name of the directory of this file
  .metadata({                 // add any variable you want
                              // use them in layout-files
    sitename: "My Static Site & Blog",
    siteurl: "http://example.com/",
    description: "It's about saying »Hello« to the world.",
    generatorname: "Metalsmith",
    generatorurl: "http://metalsmith.io/"
  })
  .source('./src')            // source directory
  .destination('./build')     // destination directory
  .clean(true)                // clean destination before
  .use(collections({          // group all blog posts by internally
    posts: 'posts/*.md'       // adding key 'collections':'posts'
  }))                         // use `collections.posts` in layouts
  .use(markdown())            // transpile all md into html
  .use(permalinks({           // change URLs to permalink URLs
    relative: false           // put css only in /css
  }))
  .use(layouts())             // wrap layouts around html
  .build(function(err) {      // build process
    if (err) throw err;       // error handling is required
  });
```

[You can follow along with a detailed walkthrough](./step-by-step) or have a go with an example:

```bash
git clone https://github.com/metalsmith/metalsmith.git
cd metalsmith/examples/static-site
npm install
npm start
```

---

## Install it

Metalsmith and its plugins can be installed with npm or Yarn:

NPM:
```bash
npm install metalsmith
```

Yarn:
```bash
yarn add metalsmith
```

The package exposes both a [JavaScript API](/api), and [CLI](https://github.com/metalsmith/metalsmith#cli) in case you're used to that type of workflow from other static site generators. To see how they're used check out the [examples](https://github.com/metalsmith/metalsmith/tree/master/examples) or [the walkthrough](./step-by-step).

---

## Introduction

Metalsmith is an extremely simple, pluggable static site generator. So let us explain why:

### Why is Metalsmith a pluggable static site generator?

The task of a static site generator is to produce static build files that can be deployed to a web server. These files are built from source files. Basically for a static site generator this means:

1. from a source directory read the source files and extract their information
2. manipulate the information
3. write the manipulated information to files into a destination directory

Metalsmith is built on this reasoning. It takes the information from the source files from a source directory and it writes the manipulated information to files into a destination directory. All manipulations, however, it exclusively leaves to plugins.

Manipulations can be anything: translating templates, transpiling code, replacing variables, wrapping layouts around content, grouping files, moving files and so on. This is why we say *»Everything is a Plugin«*. And of course, several manipulations can be applied one after another. Obviously, in this case the sequence matters.

### Why is Metalsmith extremely simple?

1. When all manipulations are performed by plugins, the only thing Metalsmith has to do in its core is to provide for an underlying logic of actually how manipulations are dealt with and for a defined interface for the plugins. To achieve this, we only needed around 400 lines of code --- have a [look at the source yourself](https://github.com/metalsmith/metalsmith/blob/master/lib/index.js). We believe this is rather simple.

2. For manipulations Metalsmith uses a very clever, but extremely simple idea. All source files are initially converted into JavaScript objects with the usual `{property: property value}` pairs. These `{property: property value}` pairs contain information on the original file itself (such as its `birthtime` or `path`) and on its `content`. The JavaScript object for each file is then supplemented with all variables either specified in the front-matter of the file or elsewhere. The manipulations performed by the plugins are now nothing else then modifications applied to the JavaScript objects either by changing the properties or the property values.

3. Breaking down Metalsmith into a core and many plugins has several advantages. It reduces complexity. It gives the user the freedom to use exactly only those plugins he or she needs. Furthermore, it distributes the honor and the burden of maintaining the Metalsmith core and its plugins onto the Metalsmith community. With this approach we hope to keep the Metalsmith environment pretty up-to-date.

4. Writing plugins itself is also rather simple. The plugin-interface is easy to understand and most plugins are also rather short.

5. Every site needs JavaScript anyway. Just like the popular task runners [gulp](http://gulpjs.com/) or [grunt](http://gruntjs.com/) Metalsmith is programmed in JavaScript. So, you do not have to rely on a further language such as Ruby, Python or Go. This also helps to keep your workflow simple.

---

## Everything is a Plugin --- A first example

All of the logic in Metalsmith is handled by plugins. You simply chain them together. Here's what the simplest blog looks like. It uses only two plugins, `markdown()` and `layouts()`...

```javascript
Metalsmith(__dirname)          // instantiate Metalsmith in the cwd
  .source('sourcepath')        // specify source directory
  .destination('destpath')     // specify destination directory
  .use(markdown())             // transpile markdown into html
  .use(layouts())              // wrap a nunjucks layout around the html
  .build(function(err) {       // this is the actual build process
    if (err) throw err;    // throwing errors is required
  });
```

... and by the way, if you do not want your destination directory to be cleaned before a new build, just add `.clean(false)`. But what if you want to get fancier by hiding your unfinished drafts and using permalinks? Just add plugins...

```javascript
Metalsmith(__dirname)
  .source('sourcepath')      
  .destination('destpath')
  .clean(false)                  // do not clean destination
                                 // directory before new build   
  .use(drafts())                 // only files that are NOT drafts
  .use(markdown())
  .use(permalinks())             // make a permalink output path
  .use(layouts())
  .build(function(err) {    
    if (err) throw err;
  });
```

...it's as easy as that!

A small comment. The `layouts()` plugin needs the `jstransformer-nunjucks` package to render layouts. Make sure to install it with `npm install jstransformer-nunjucks`. Other templating languages can be used as well (see the [@metalsmith/layouts readme](https://github.com/metalsmith/layouts) for more information).

---

## How does it work in more detail?

Metalsmith works in three simple steps:

  1. Read all the files in a source directory and transform them into a JavaScript object of JavaScript objects.
  2. Invoke a series of plugins that manipulate these objects.
  3. According to the information contained in the resulting objects write them as files into a destination directory

Every file in the source directory is transformed into a JavaScript Object. For instance,

`my-file.md:`

```markdown
---
title: A Catchy Title
draft: false
---

An unfinished article...
```

becomes

```javascript
{
  'relative_to_sourcepath/my-file.md': {
    title: 'A Catchy Title',
    draft: false,
    contents: 'An unfinished article...',
    mode: '0664',
    stats: {
      /* keys with information on the file */
    }    
  }
}
```

where the content of the file is always put into the property value of `contents`. For illustration purposes only we display the value of `contents` as a string. Technically, however, the property value of `contents` is realised as a `Buffer.from('...')` object, in order to also handle straight binary data well. `mode` contains the permission the file has and `stats` has more technical information on the file such as `size` or `birthtime`. Furthermore, the file is also parsed for YAML-front-matter information, which will then also be put into the JS Object. Thus, we finally have an JavaScript object of JavaScript objects. This encompassing JavaScript object is usally called `files` since it contains all the JavaScript objects that represent the files.

```javascript
{
  "relative_to_sourcepath/file1.md": {
    title: 'A Catchy Title',
    draft: false,
    contents: 'An unfinished article...',
    mode: '0664',
    stats: {
      /* keys with information on the file */
    }    
  },
  "relative_to_sourcepath/file2.md": {
    title: 'An Even Better Title',
    draft: false,
    contents: 'One more unfinished article...',
    mode: '0664',
    stats: {
      /* keys with information on the file */
    }    
  }
}
```

The plugins can manipulate the JavaScript objects representing the original files however they want, and writing one is super simple. Here's the code for the `drafts()` plugin from above. You can also find the code in the [github repository for `@metalsmith/drafts`](https://github.com/metalsmith/drafts). The code just runs through the JS object `files` and deletes all contained JavaScript objects that have a property value of `true` for the property `draft`:

```javascript
/**
 * Expose `plugin`.
 */
module.exports = plugin;

/**
 * Metalsmith plugin to hide drafts from the output.
 *
 * @return {Function}
 */

function plugin() {
  return function(files, metalsmith, done){
    setImmediate(done);
    Object.keys(files).forEach(function(file){
      const data = files[file];
      if (data.draft) delete files[file];
    });
  };
}
```

Of course plugins can get a lot more complicated too. That's what makes Metalsmith powerful; the plugins can do anything you want and the community has written a large amount of plugins already.

Note: The order the plugins are invoked is the order they are in the build script or the metalsmith.json file for cli implementations.  This is important for using a plugin that requires a plugins output to work.

If you are still struggling with the concept we like to recommend you the [`writemetadata()`](https://github.com/Waxolunist/metalsmith-writemetadata) plugin. It is a metalsmith plugin that writes the `{property: property value}` pairs excerpted from the JavaScript objects representing the files to the filesystem as .json files. You can then view the .json files to find out how files are represented internally in Metalsmith.

```javascript
Metalsmith(__dirname)            
  .source('sourcepath')      
  .destination('destpath')   
  .use(markdown())          
  .use(layouts())
  .use(writemetadata({            // write the JS object
    pattern: ['**/*'],            // for each file into .json
    ignorekeys: ['next', 'previous'],
    bufferencoding: 'utf8'        // also put 'content' into .json
  }))
  .build(function(err) {         
    if (err) throw err;          
  });
```

We believe, that understanding the internal representation of files as JavaScript objects is really key to fully grasp the concept of Metalsmith. To see this, we look at what happens in the second example chain above:

So, within the Markdown chain above after applying `.use(markdown())` the initial representation of the `my-file.md` becomes `my-file.html`...

```javascript
{
  'relative_to_sourcepath/my-file.html': {
    title: 'A Catchy Title',
    draft: false,
    contents: '<p>An unfinished article...</p>',
    ...
  }
}
```

and after applying `.use(permalinks())` it becomes:

```javascript
{
  'relative_to_sourcepath/my-file/index.html': {
    title: 'A Catchy Title',
    draft: false,
    contents: '<p>An unfinished article...</p>',
    path: 'myfile',
    ...
  }
}
```

Note, that `permalinks()` is also adding a `path`--property by default.

Assuming we have defined a very simple nunjucks layout file in a separate layouts folder...

`./layouts/layout.njk`

{% raw %}
```html
<!doctype html>
<html>
<head>
  <title>{{ title }}</title>
</head>
<body>
  {{ contents | safe }}
</body>
</html>
```
{% endraw %}

... after applying `.use(layouts())` in our Metalsmith chain our JavaScript object becomes:

```javascript
{
  'relative_to_sourcepath/my-file/index.html': {
    title: 'A Catchy Title',
    draft: false,
    contents: '<!doctype html><html><head>
               <title>A Catchy Title</title></head><body>
               <p>An unfinished article...</p>
               </body></html>',
    path: 'myfile',
    ...      
  }
}
```

Finally when the `.build(function(err))` is performed our JavaScript object is written to `relative_to_destpath/myfile/index.html`. So you see, how the chain works. It's rather straight forward, isn't it?

---

## Metadata & debugging

For Metalsmith we have stated that everything is a plugin. That is true, but in addition the Metalsmith core also provides for a `metadata()` function. You can specify arbitrary `{property: property value}` pairs and these information will be globally accessible from each plugin.

```javascript
const debug = require('metalsmith-debug');

...

Metalsmith(__dirname)            
  .source('sourcepath')      
  .destination('destpath')   
  .clean(false)
  .metadata({
      author: 'John Doe',
      site: 'http://example.com'
  })
  .use(markdown())
  .use(layouts())
  .use(debug())             // displays debug info on the console
  .build(function(err) {         
    if (err) throw err;
  });
```

As you have seen in the code above, we have also introduced a plugin named [`metalsmith-debug`](https://github.com/mahnunchik/metalsmith-debug). For this plugin to actually show debug information you need to define an environment variable `DEBUG` and set it to `metalsmith:*`

On Linux/Mac:
```bash
export DEBUG=metalsmith:*
```

On Windows:
```bat
set "DEBUG=metalsmith:*"
```

The source and destination path, the metadata and all files are then logged to the console.

---

## Further information

Yes, we know. The documentation can be improved. If you want to help, give us a shout. But in the meantime have a look at the [Awesome Metalsmith list](https://github.com/metalsmith/awesome-metalsmith). There you will find references to a number of excellent tutorials, examples and use cases.

---

## A Little Secret

We keep referring to Metalsmith as a "static site generator", but it's a lot more than that. Since everything is a plugin, the core library is actually just an abstraction for manipulating a directory of files.

Which means you could just as easily use it to make...

<ul class="ExampleList">
{% for example in examples %}
<li class="Example">
  <h1 class="Example-title">{{ example.name }}</h1>
  <ol class="Example-steps">
  {% for step in example.steps %}
  <li class="Example-step ss-{{ step.icon }}">{{ step.text }}</li>
  {% endfor %}
  </ol>
</li>
{% endfor %}
</ul>

The plugins are all reusable. That PDF generator plugin for eBooks? Use it to generate PDFs for each of your blog posts too!

Check out [the code examples](https://github.com/metalsmith/metalsmith/tree/master/examples) to get an idea for what's possible.

---

## Writing A Plugin

Writing a plugin is not difficult as you have seen above with the `metalsmith-drafts` plugin. In order to achieve more complicated tasks you will most likely find and can use `npm`-packages. Look at how others have done it. Here is an example using `debug` (which we appreciate if you use it) and `multimatch`:

`metalsmith-myplugin`:

```javascript
// we would like you to use debug
const debug = require('debug')('metalsmith-myplugin');
const multimatch = require('multimatch');

// Expose `plugin`.
module.exports = plugin;

function plugin(opts){
  opts.pattern = opts.pattern || [];

  return function (files, metalsmith, done){

    setImmediate(done);
    Object.keys(files).forEach(function(file){
      if(multimatch(file, opts.pattern).length) {
        debug('myplugin working on: %s', file);

        //
        // here would be your code
        //

      }
    });
  };
}
```

<p class="Note Note--tip">
Heads up! New docs for writing plugins are being written. It's still a work in progress, but I feel they already provide more value so feel free to <a href="/docs/writing-plugins">have a look</a> and provide feedback <a href="https://github.com/metalsmith/metalsmith.io/issues">on Github</a>
</p>

---

## Matching

Even though we touched on the topic already, we did not tackle it explicitly. We mentioned that plugins usually run through all files presented to `metalsmith`. This happens in a loop like this:

```javascript
Object.keys(files).forEach(function(file){
  //
  // here would be your code
  //
});
```

The question now is, how does for instance a markdown-engine know, which files to transpile? Per default, `@metalsmith/markdown` is checking if `file` has a `.md` or `.markdown` extension. Remember, `file` is a JavaScript object that has its full filename (including its path) as a value.
If the check is not true it jumps over it, otherwise it is passing the file to the engine. After processing it, `@metalsmith/markdown` replaces the `.md` extension with `.html` and the next plugin can now check against the new filename and so on.

A process such as this is called check for pattern matching. Many `metalsmith`-plugins employ such matching. Either they check against internally set requirements or patterns or they offer an explicit option to check against user defined matches, like we have already seen in the `writemetadata`-plugin:

```javascript
.use(writemetadata({            // write the JS object
  pattern: ['**/*'],            // for each file into .json
  ignorekeys: ['next', 'previous'],
  bufferencoding: 'utf8'        // also put 'content' into .json
}))
```

Pattern matching is normally based on [glob][glob_pattern] pattern. Many plugins employ either own functions or rely on [`micromatch`](https://www.npmjs.com/package/micromatch), [`minimatch`](https://www.npmjs.com/package/minimatch) or [`multimatch`](https://www.npmjs.com/package/multimatch).

```javascript
const micromatch = require('micromatch');
const { normalize } = require('path');

[...]

  // Checks whether files should be processed
  // length is zero if no matching pattern was found
  if (micromatch(file, opts.pattern, { dot: true, format: normalize }).length) {

    // process file

  }
```

---

## License

The MIT License (MIT)

Copyright &copy; 2021, Webketje &lt;metalsmith.org@gmail.com&gt;   
Copyright &copy; 2014-2021, Segment.io &lt;friends@segment.io&gt;

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

---
