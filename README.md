# requirejs/example-multipage-shim

This project shows how to set up a multi-page requirejs-based project that has
the following goals:

* Each page uses a mix of common and page-specific modules.
* All pages share the same requirejs config.
* After an optimization build, the common items should be in a shared common
layer, and the page-specific modules should be in a page-specific layer.
* The HTML page should not have to be changed after doing the build.
* **[shim config](http://requirejs.org/docs/api.html#config-shim)** is used to
load Backbone and underscore.

This **project is different** from the standard
[requirejs/example-multipage](https://github.com/requirejs/example-multipage)
because [shim config](http://requirejs.org/docs/api.html#config-shim)
is used. Shimmed modules need their dependencies loaded before they are executed.
It is not as robust as normal modules. Additionally, the common.js file has
shim config in it. See the js/app/main1.js file for the Backbone and underscore
use.

Since the shim config requires dependencies to be in the page, instead of
using `data-main="js/page1"` for page1.html, this example inlines the require
calls in the HTML page. If data-main was used instead, then 'js/page1' could not
have any dependencies inlined, and instead still rely on the 'common' and
'app/main1' build layers to hold the modules, due to the restrictions shim
config places on the build.

## Getting this project template

If you are using [volo](https://github.com/volojs/volo):

    volo create projectname requirejs/example-multipage-shim

Otherwise,
[download latest zipball of master](https://github.com/requirejs/example-multipage-shim/zipball/master).

## Project layout

This project has the following layout:

* **tools**: The requirejs optimizer, **r.js**, and the optimizer config,
**build.js.**
* **www**: The code that runs in the browser while in development mode.
* **www-built**: Generated after an optimizer build. Contains the built code
that can be deployed to the live site.

This **www** has the following layout:

* **page1.html**: page 1 of the app.
* **page2.html**: page 2 of the app.
* **js**
    * **app**: the directory to store app-specific modules.
    * **lib**: the directory to hold third party modules, like jQuery.
    * **common.js**: contains the requirejs config, and it will be the build
    target for the set of common modules.

To optimize, run:

    node tools/r.js -o tools/build.js

That build command creates an optimized version of the project in a
**www-built** directory. The **js/common.js** file will contain all the common
modules. **js/app/main1.js** will contain the main1-specific modules,
**js/app/main2.js** will contain the main2-specific modules.

This means that for page 1, after an optimization, there will be two scripts
loaded:

* js/common.js
* js/app/main1.js

## Building up the common layer

As you do builds and see in the build output that each page is including the
same module, add it to common's "include" array in **tools/build.js**.

It is better to add these common modules to the **tools/build.js** config
instead of doing a require([]) call for them in **js/common.js**. Modules that
are not explicitly required at runtime are not executed when added to common.js
via the include build option. So by using **tools/build.js**, you can include
common modules that may be in 2-3 pages but not all pages. For pages that do
not need a particular common module, it will not be executed. If you put in a
require() call for it in **js/common.js**, then it will always be executed.

## More info

For more information on the optimizer:
http://requirejs.org/docs/optimization.html

For more information on using requirejs:
http://requirejs.org/docs/api.html
