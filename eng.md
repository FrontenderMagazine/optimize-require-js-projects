This article will demonstrate you how to concatenate and minify projects that
are based on RequireJS. In this article I’ll use several tools that require Node.
js. So, if you don’t have Node.js yet, install it[here][1].

## Motivation

A lot has been written already about RequireJS. This tool allows you to easily
separate your JavaScript code into several modules and by this keep your code 
modular and easy to maintain. Then, you get many JavaScript files that have 
dependency relation. By adding one script reference to RequireJS in your html 
file, you can load all the required scripts for your page.  
Still, in production, this is a bad practice to leave all JavaScript files
separated. Making many requests, no matter how small the requested files are, 
take time. This time can be saved by concatenating scripts that reduce the 
number of requests and save the loading time.  
Another technique to save loading time is to reduce the size of the requested
files, a small file can be delivered faster. This process is called
[minification][2] and it is done by carefully changing the script’s code
without changing its behavior and functionality. Such changes can be: removing 
unnecessary characters like spaces, mangling variables and methods names and so 
on. This process of concatenation and minification is called optimization. In 
addition to JavaScript files optimization, the same methods are used to optimize
CSS files.  
RequireJS has two main methods: define() and require(). These methods basically
have similar declaration and they both know to load dependencies and then 
execute a callback function. Unlike require(), define() is used to store code as
a named module. Therefore the define()’s callback function should return a value
to define the module. Such modules are called[AMD][3] (Asynchronous Module
Definition
).

If you are not familiar with RequireJS or didn’t fully understand what I
wrote - don’t worry. An example is about to come.

## JavaScript Application Optimization

In this section I will demonstrate the optimization of Addy Osmani’s 
[TodoMVC Backbone.js + RequireJS project][4]. Since the TodoMVC project
contains many implementations of TodoMVC in different frameworks, I downloaded 
version 1.1.0 and draw out the Backbone.js + RequireJS application. Download the
application from[here][5] and extract the zip file. The extracted todo-mvc
directory will be our example root path and from now on I’ll refer to this 
directory as <root
>.   
If you’ll look on <root>/index.html file, you will see it contains only one
script tag (and another one if you use Internet Explorer
):<figure class="code"><figcaption>

index.html scripts refrences</figcaption>

| 1
    2
    3
    4 | <span class="line"><span class="nt"><script </span>&lt
;span class="na">data-main=</span><span class="s">"js/main"</span> <
span class="na">src=</span><span class="s">"js/lib/require/require.js"<
/span><span class="nt">></script></span
>
    </span><span class="line"><span class="c"><!--[if IE]>&
lt;/span
>
    </span><span class="line"><span class="c">    <script src
="js/lib/ie.js"></script></span
>
    </span><span class="line"><span class="c"><![endif]-->&
lt;/span
>
    </span> |
||</figure>

In fact, the only tag required for loading the whole project’s scripts is the
require.js script tag. If you’ll launch[the project][6] in your browser and
look under the network tab of your favorite inspection tool, you will notice 
that your browser has also loaded other JavaScript files:  
![Loaded JavaScript Files List][7]  
All the scripts marked inside the red square were loaded by RequireJS.

To optimize the project we will use [RequireJS Optimizer][8]. Follow the 
[download instructions][9], get r.js and copy it to the <root> directory.
jrburke’s[r.js][10] is a command line tool that can run AMD based projects, but
what is more important, it includes the RequireJS Optimizer which allows us to 
concatenate and minify scripts.  
RequireJS Optimizer has many usages. It can optimize single JavaScript or
single CSS file, it can optimize a whole project or only part of it as well as 
multi-page application. It can also use different minification engines or no 
minification at all, and so on. This article has no intention to cover all the 
possibilities of RequireJS Optimizer, but to demonstrate a usage.

As I mentioned earlier, we will use Node.js in order to run the optimizer. The
following command runs it:<figure class="code"><figcaption>

Run RequireJS Optimizer</figcaption>

| 1 | <span class="line"><span class="nv">$ </span>node r.js -o <
arguments>
    </span> |
||</figure>

There are two ways to supply arguments to the optimizer. One way is to specify
arguments on the command line:<figure class="code"><figcaption>

Arguments on the command line</figcaption>

| 1 | <span class="line"><span class="nv">$ </span>node r.js -o <
span class="nv">baseUrl</span><span class="o">=</span>. <span class
="nv">name</span><span class="o">=</span>main <span class="nv">out&
lt;/span><span class="o">=</span>main-built.js
    </span> |
||</figure>

Other way is to specify a build profile file (relative to the execution folder
) that contains the arguments:<figure class="code"><figcaption>

Arguments on build profile file</figcaption>

| 1 | <span class="line"><span class="nv">$ </span>node r.js -o build.
js
    </span> |
||</figure>

And build.js content:<figure class="code"><figcaption>

Arguments on build profile file</figcaption>

| 1
    2
    3
    4
    5 | <span class="line"><span class="p">({</span>
    </span><
span class="line">    <span class="nx">baseUrl</span><span class="o">:&
lt;/span> <span class="s2">"."</span><span class="p">,</span
>
    </span><
span class="line">    <span class="nx">name</span><span class="o">:<
/span> <span class="s2">"main"</span><span class="p">,</span
>
    </span><
span class="line">    <span class="nx">out</span><span class="o">:</
span> <span class="s2">"main-built.js"</span
>
    </span><
span class="line"><span class="p">})</span
>
    </span> |
||</figure>

I think a build profile file is more readable than command line arguments so I
’ll use this method. Let’s create our <root>/build.js file and see which 
arguments it contains:<figure class="code"><figcaption>

<root>/build.js</figcaption>

| 1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22
    23
    24
    25
    26
    27
    28
    29
    30
    31
    32
    33
    34
    35
    36 | <span class="line"><span class="p">({</span>
    </span>&lt
;span class="line">    <span class="nx">appDir</span><span class="o">:&
lt;/span> <span class="s1">'./'</span><span class="p">,</span
>
    </span>&lt
;span class="line">    <span class="nx">baseUrl</span><span class="o
">:</span> <span class="s1">'./js'</span><span class="p">,</span
>
    </span>&lt
;span class="line">    <span class="nx">dir</span><span class="o">:<
/span> <span class="s1">'./dist'</span><span class="p">,</span
>
    </span>&lt
;span class="line">    <span class="nx">modules</span><span class="o
">:</span> <span class="p">[</span
>
    </span>&lt
;span class="line">        <span class="p">{</span
>
    </span>&lt
;span class="line">            <span class="nx">name</span><span class
="o">:</span> <span class="s1">'main'</span
>
    </span>&lt
;span class="line">        <span class="p">}</span
>
    </span>&lt
;span class="line">    <span class="p">],</span
>
    </span>&lt
;span class="line">    <span class="nx">fileExclusionRegExp</span><span
class="o">:</span> <span class="sr">/^(r|build)\.js$/</span><span 
class="p">,</span
>
    </span>&lt
;span class="line">    <span class="nx">optimizeCss</span><span class="
o">:</span> <span class="s1">'standard'</span><span class="p">,</
span
>
    </span>&lt
;span class="line">    <span class="nx">removeCombined</span><span 
class="o">:</span> <span class="kc">true</span><span class="p">,<
/span
>
    </span>&lt
;span class="line">    <span class="nx">paths</span><span class="o">:&
lt;/span> <span class="p">{</span
>
    </span>&lt
;span class="line">        <span class="nx">jquery</span><span class="o
">:</span> <span class="s1">'lib/jquery'</span><span class="p">,<
/span
>
    </span>&lt
;span class="line">        <span class="nx">underscore</span><span 
class="o">:</span> <span class="s1">'lib/underscore'</span><span 
class="p">,</span
>
    </span>&lt
;span class="line">        <span class="nx">backbone</span><span class
="o">:</span> <span class="s1">'lib/backbone/backbone'</span><span 
class="p">,</span
>
    </span>&lt
;span class="line">        <span class="nx">backboneLocalstorage</span><
span class="o">:</span> <span class="s1">'lib/backbone/backbone.
localStorage'</span><span class="p">,</span
>
    </span>&lt
;span class="line">        <span class="nx">text</span><span class="o
">:</span> <span class="s1">'lib/require/text'</span
>
    </span>&lt
;span class="line">    <span class="p">},</span
>
    </span>&lt
;span class="line">    <span class="nx">shim</span><span class="o">:<
/span> <span class="p">{</span
>
    </span>&lt
;span class="line">        <span class="nx">underscore</span><span 
class="o">:</span> <span class="p">{</span
>
    </span>&lt
;span class="line">            <span class="nx">exports</span><span 
class="o">:</span> <span class="s1">'_'</span
>
    </span>&lt
;span class="line">        <span class="p">},</span
>
    </span>&lt
;span class="line">        <span class="nx">backbone</span><span class
="o">:</span> <span class="p">{</span
>
    </span>&lt
;span class="line">            <span class="nx">deps</span><span class
="o">:</span> <span class="p">[</span
>
    </span>&lt
;span class="line">                <span class="s1">'underscore'</span><
span class="p">,</span
>
    </span>&lt
;span class="line">                <span class="s1">'jquery'</span
>
    </span>&lt
;span class="line">            <span class="p">],</span
>
    </span>&lt
;span class="line">            <span class="nx">exports</span><span 
class="o">:</span> <span class="s1">'Backbone'</span
>
    </span>&lt
;span class="line">        <span class="p">},</span
>
    </span>&lt
;span class="line">        <span class="nx">backboneLocalstorage</span><
span class="o">:</span> <span class="p">{</span
>
    </span>&lt
;span class="line">            <span class="nx">deps</span><span class
="o">:</span> <span class="p">[</span><span class="s1">'backbone'<
/span><span class="p">],</span
>
    </span>&lt
;span class="line">            <span class="nx">exports</span><span 
class="o">:</span> <span class="s1">'Store'</span
>
    </span>&lt
;span class="line">        <span class="p">}</span
>
    </span>&lt
;span class="line">    <span class="p">}</span
>
    </span>&lt
;span class="line"><span class="p">})</span
>
    </span
> |
||</figure>

Understanding all the configurations of RequireJS Optimizer is not the aim of
this article, but I want do describe the arguments I used:

| Argument            | Description
|

| appDir              | The directory that contains the application (the <root
> directory). All the files sitting under this directory will be copied from 
here to the dir argument.
|
| baseUrl             | A path, relative to appDir, that represents the anchor
path for finding files.
|
| dir                 | This is the output directory which all the application
files will be copied to.
|
| modules             | Array that contains objects. Each object represents a
module that should be optimize.
|
| fileExclusionRegExp | Each file or directory that will be match to this
regular expression will not be copied to our output directory. Since we located 
r.js and build.js under the application directory, we want the optimizer to 
exclude them. Therefore we set this argument to /^(r|build)\.js
$/. |
| optimizeCss         | RequireJS Optimizer automatically optimizes our
application’s CSS files. This argument controls the CSS optimization settings. 
Allowed values: “none”, “standard”, “standard.keepLines”, “standard.keepComments
”, “standard.keepComments.keepLines
”.                             |
| removeCombined      | If true, optimizer will remove concatenated files from
the output directory.
|
| paths               | Relative paths of modules
.                                                                                                                                                                                                                                                       |
| shim                | Configure dependencies and exports for “browser
globals” scripts, that do not use define() to declare the dependencies and set a
module value.
|

For more information and for advanced usage of the RequireJS Optimizer, in
addition to it’s web page provided earlier, you can read the details of all the 
allowed optimizer configuration options[here][11].

Now that we have the build file, lets run the optimizer. Go to the <root>
directory and execute the command:<figure class="code"><figcaption>

Run optimizer</figcaption>

| 1 | <span class="line"><span class="nv">$ </span>node r.js -o build.
js
    </span> |
||</figure>

A new folder has created: <root>/dist. It is important to notice that the
script <root>/dist/js/main.js now contains all it’s combined and minified 
dependencies. Moreover, <root>/dist/css/base.css is also optimized.  
Running [the optimized project][12] launches the application which looks
exactly like the non-optimized version. Inspecting the page network traffic will
show that only two JavaScript files were loaded:
![Loaded Optimized JavaScript Files List][13]  
RequireJs Optimizer reduced the amount of server scripts requests from 13 to 2
and reduced the total scripts size from 164KB to 58.6KB (both require.js and 
main.js
).

## Overhead

Apparently, after the optimization is over, we don’t need a reference to
require.js because the scripts are no longer separated and all the dependencies 
were loaded.  
Still, the optimization process concatenated all our scripts and produced one
optimized script file which contains many calls to define() and require(). 
Therefore, to allow the application work properly, define() and require() must 
be specified and implemented somewhere in our application.  
This issue causes a well known overhead: we always have to have any code that
implement define() and require
(). **This code is not part of our application and it exists only due to our
infrastructure considerations.** This problem becomes even bigger when we want
to develop a JavaScript library. Such libraries usually have small size 
comparing to RequireJS itself, and therefore including it in the library will 
cause a huge overhead.

At the time of writing this article, there isn’t any full solution for this
overhead, but we can ease it using[almond][14]. Almond is a minimalistic AMD
loader which implements the RequireJS API, and so, instead of including the 
RequireJS implementation in our optimized code, we can include almond.  
Nowadays, I am working on an optimizer that will be able to optimize RequireJS
applications without overhead, but this is still a new project so there is 
nothing to show yet.

## Download & Conslusion

*   [Download][5] **unoptimized** TodoMVC Backbone.js + RequireJS project or 
    [See][6] it in action.
*   [Download][15] **optimized** TodoMVC Backbone.js + RequireJS project (
    located under dist folder) or
   [See][12] it in action.

After reading this article, I believe you got a solid idea how to optimize your
RequireJS application. I’ll be glad to answer any question you have.

Good Luck!   
NaorYe

 [1]: http://nodejs.org/
 [2]: http://en.wikipedia.org/wiki/Minification_%28programming%29
 [3]: http://requirejs.org/docs/whyamd.html
 [4]: http://todomvc.com/dependency-examples/backbone_require/
 [5]: http://www.webdeveasy.com/code/optimize-requirejs-projects/todo-mvc.zip
 [6]: http://www.webdeveasy.com/code/optimize-requirejs-projects/todo-mvc/
 [7]: img/loaded-js-files-list.png "Loaded JavaScript Files List"
 [8]: http://requirejs.org/docs/optimization.html
 [9]: http://requirejs.org/docs/optimization.html#download
 [10]: https://github.com/jrburke/r.js
 [11]: https://github.com/jrburke/r.js/blob/master/build/example.build.js

 [12]: http://www.webdeveasy.com/code/optimize-requirejs-projects/todo-mvc/dist/

 [13]: img/loaded-optimized-js-files-list.png "Loaded Optimized JavaScript Files List"
 [14]: https://github.com/jrburke/almond

 [15]: http://www.webdeveasy.com/code/optimize-requirejs-projects/todo-mvc-optimized.zip