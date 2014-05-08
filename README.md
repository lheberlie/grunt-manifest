# grunt-manifest 
Generate HTML5 Cache Manifest files. 

## Getting Started
This plugin requires Grunt `~0.4.0`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install git://github.com/lheberlie/grunt-manifest.git --save-dev
```

* FYI: depending on your network settings, you may need to force Git to clone from **https://** instead of **git://**
	* ```git config --global url.https://.insteadOf git://```


Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-manifest');
```

### HTML5 Cache Manifest task

_Run this task with the `grunt manifest` command._

Visit [A Beginner's Guide to Using the Application Cache](http://www.html5rocks.com/en/tutorials/appcache/beginner/) for more information on Cache Manifest files.

Task targets, files and options may be specified according to the grunt [Configuring tasks](http://gruntjs.com/configuring-tasks) guide.

### Parameters

#### options 
Type: `Object`  
Default: `{}`


This controls how this task (and its helpers) operate and should contain key:value pairs, see options below.

#### src
Type: `String` `Array`  
Default: `undefined`   

Sets the input files.

#### dest
Type: `String`	
Default `manifest.appcache`

Sets the name of the Cache Manifest file.	
Remember that `.appcache` is now the W3C recommended file extension. 

### Options

#### banner
Type: `String`	
Default: `undefined`

#### basePath
Type: `String`	
Default: `undefined`	

Sets the base path for **input files**. **_It's recommended to set this_**.

#### cache
Type: `String`	
Default: `undefined`	

Adds manually a string to the **CACHE** section. Needed when you have cache buster for example.

#### exclude
Type: `String` `Array`	
Default: `undefined`	

Exclude specific files from the Cache Manifest file.

#### network
Type: `String` `Array`	
Default: `"*"` (By default, an online whitelist wildcard flag is added)		

Adds a string to the **NETWORK** section.

See [here](http://diveintohtml5.info/offline.html#network) for more information.

#### fallback
Type: `String` `Array`	
Default: `undefined`	

Adds a string to the **FALLBACK** section.

See [here](http://diveintohtml5.info/offline.html#fallback) for more information.

#### preferOnline
Type: `Boolean`		
Default: `undefined`

Adds a string to the **SETTINGS** section, specifically the cache mode flag of the ```prefer-online``` state.

See [here](http://www.whatwg.org/specs/web-apps/current-work/multipage/offline.html#concept-appcache-mode-prefer-online) for more information.

#### verbose
Type: `Boolean`		
Default: `true`	

Adds a meta "copyright" comment.

#### networkHTTPArchive
Type: `String`		
Default: `undefined`

This tells the manifest task where to find the HTTP Archive file under your current directory. e.g., (**network_archives/build.har**).  Using Google Chrome Developer Tools you can capture the network waterfall and save that information to an archive file.  This will take the browser's web traffic and write it to your manifest file.  The file name is not important, it would be whatever you saved the file as.

#### timestamp
Type: `Boolean`		
Default: `true`	

Adds a timestamp as a comment for easy versioning.

Note: timestamp will invalidate application cache whenever cache manifest is rebuilt, even if contents of files in `src` have not changed.

#### hash
Type: `Boolean`
Default: `false`

Adds a sha256 hash of all `src` files (actual contents) as a comment.

This will ensure that application cache invalidates whenever actual file contents change (it's recommented to set `timestamp` to `false` when `hash` is used).

#### master
Type: `String` `Array`
Default: `undefined`

Hashes master html files (used with `hash`). Paths must be relative to the 'basePath'. This is useful when there are multiple html pages using one cache manifest and you don't want to explicitly include those pages in the manifest.

### Config Example

```js
// Project configuration
grunt.initConfig({
  manifest: {
    generate: {
      options: {
        banner: "# <%= pkg.name %> - v<%= pkg.version %> - " +
                        "<%= grunt.template.today(\"yyyy-mm-dd\") %>\n" +
                        "# Copyright (c) <%= grunt.template.today(\"yyyy\") %> <%= pkg.author.name %>" +
                        " Licensed <%= _.pluck(pkg.licenses, \"type\").join(\", \") %> \n",
        basePath: '../',
        cache: ['js/app.js', 'css/style.css'],
        network: ['http://*', 'https://*'],
        fallback: ['/ /offline.html'],
        exclude: ['js/jquery.min.js'],
        preferOnline: true,
        verbose: true,
        timestamp: true,
        hash: true,
        master: ['index.html']
      },
      src: [
      	'some_files/*.html',
    	  'js/*.min.js',
    	  'css/*.css'
      ],
      dest: 'manifest.appcache'
    }
  }
});
```

### Config Example using ```networkHTTPArchive```

```js
// Project configuration
grunt.initConfig({
  // grunt-manifest
        manifest: {
            build: {
                options: {
                    banner: "# <%= pkg.name %> - v<%= pkg.version %> - " +
                        "<%= grunt.template.today(\"yyyy-mm-dd\") %>\n" +
                        "# Copyright (c) <%= grunt.template.today(\"yyyy\") %> <%= pkg.author.name %>" +
                        " Licensed <%= _.pluck(pkg.licenses, \"type\").join(\", \") %> \n",
                    basePath: "./build",
                    cache: ["# Explicit files to include.\n"],
                    network: ["*"],
                    fallback: ["/ offline.html"],
                    verbose: true,
                    networkHTTPArchive: "<%= pkg.networkArchiveDirectory %>/build.har",
                    timestamp: true
                },
                src: ["*.html"],
                dest: "build/manifest.appcache"
            }
       }
});
```


### Output example

```
CACHE MANIFEST
# This manifest was generated by grunt-manifest HTML5 Cache Manifest Generator
# Time: Thu May 01 2014 15:52:31 GMT-0500 (CDT)
# grunt-test - v0.0.1 - 2014-05-01
# Copyright (c) 2014 Lloyd Heberlie Licensed Apache-2.0 

CACHE:
js/app.js
css/style
css/style.css
js/zepto.min.js
js/script.js
some_files/index.html
some_files/about.html

NETWORK:
*

# hash: 76f0ef591f999871e1dbdf6d5064d1276d80846feeef6b556f74ad87b44ca16a
```

You do need to be fully aware of standard browser caching.
If the files in **CACHE** are in the network cache, they won't actually update,
since the network cache will spit back the same file to the application cache.
Therefore, it's recommended to add a hash to the filenames's, akin to rails or yeoman. See [here](http://www.stevesouders.com/blog/2008/08/23/revving-filenames-dont-use-querystring/) why query strings are not recommended.

### Output example using network HTTP Archive (HAR)  

```
CACHE MANIFEST
# This manifest was generated by grunt-manifest HTML5 Cache Manifest Generator
# Time: Tue May 06 2014 17:16:06 GMT-0500 (CDT)
# Copyright (c) 2014 Lloyd Heberlie Licensed Apache-2.0 

CACHE:
# Contents from the HTTP Archive file.
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/css/application-styles.min.css
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/js/application-library.min.js
http://js.arcgis.com/3.9compact/init.js
http://js.arcgis.com/3.9compact/js/esri/nls/jsapi_en-us.js
http://js.arcgis.com/3.9compact/js/dojo/dojox/gfx/svg.js
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/css/images/ajax-loader.gif
http://js.arcgis.com/3.9compact/js/esri/dijit/Geocoder.js
http://js.arcgis.com/3.9compact/js/esri/dijit/LocateButton.js
http://js.arcgis.com/3.9compact/js/dojo/dijit/_WidgetBase.js
http://js.arcgis.com/3.9compact/js/dojo/dijit/a11yclick.js
http://js.arcgis.com/3.9compact/js/dojo/dijit/_TemplatedMixin.js
http://js.arcgis.com/3.9compact/js/dojo/dijit/focus.js
http://js.arcgis.com/3.9compact/js/esri/dijit/_EventedWidget.js
http://js.arcgis.com/3.9compact/js/esri/tasks/locator.js
http://js.arcgis.com/3.9compact/js/dojo/dojo/touch.js
http://js.arcgis.com/3.9compact/js/dojo/dijit/Destroyable.js
http://js.arcgis.com/3.9compact/js/dojo/dojo/cache.js
http://js.arcgis.com/3.9compact/js/dojo/dijit/_AttachMixin.js
http://js.arcgis.com/3.9compact/js/esri/tasks/Task.js
http://js.arcgis.com/3.9compact/js/esri/tasks/AddressCandidate.js
http://js.arcgis.com/3.9compact/js/dojo/dijit/a11y.js
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/images/map/logo-med.png
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/css/images/locate-button-icon50x50.png
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/images/simpleGeocoder.png
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/images/ajax-loader.gif
http://heb.esri.com/js/playground-js/mobile/grunt-manifest-test/dist/images/popup-sprite.png
index.html
offline.html

NETWORK:
*

FALLBACK:
/%20offline.html
```


## Release History
View the [releases](../../releases)

## Issues
Find a bug or want to request a new feature?  Please let us know by [submitting an issue](../../issues).

## Contributing
In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using [Grunt](http://gruntjs.com/).

We welcome [contributions](CONTRIBUTING.md) from anyone and everyone.

## Licensing
Copyright (c) 2014 Lloyd Heberlie  
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

A copy of the license is available in the repository's [license.txt](license.txt) file.
