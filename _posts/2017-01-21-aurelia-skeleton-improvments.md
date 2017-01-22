---
layout: post
title: "Aurelia Typescript Skeleton improvements"
categories: [Aurelia, Skeleton, Typescript, Productivity, Tools]
disqus: true
---

![Skeleton app](/images/2017-01-21/simple-app.png)

Aurelia provides a few different [skeletons](https://github.com/aurelia/skeleton-navigation) to start building your application as fast and efficient as possible.
I want to share with you a couple of productivity changes I made in skeleton to improve day-to-day work.

<!--more-->

### What is the story behind this?

Recently, I have created simple project using Aurelia to show how to build currency converter using features provided by the framework itself and modern js. This project is hosted on Github and can be found [here](https://github.com/matik12/simple-aurelia-app). As my first step to implement functionality, I have developed my own application skeleton, which includes a few changes. To download this skeleton, clone Github repo checking out the following [commit](https://github.com/matik12/simple-aurelia-app/commit/e90db931b33a8b46a34c9016a85c804619d01116) to get start up project clean copy or go to [this](https://github.com/matik12/simple-aurelia-app/tree/e90db931b33a8b46a34c9016a85c804619d01116) link and click on download button to get a zip file. 

### A list of configuration changes

1. Gulp task changes:
	- added support for sass lint & sass transpilation  
	- added support for copy assets: images, fonts
	- added support for **website config file** - not bundled json file (/src/config/app.config.json) and merging config tokens (local, deploy env.) for production & CI 
	- updated tslint task to work properly and tslint.json file with default rules (small additional changes)
	- updated bundles configuration and export
	- updated export task to unbundle config.js file after export - prevent saving bundle changes in config.js
2. Dependencies:
	- added bootstrap-sass
	- plugins for jspm module loading: text, json
3. Unit test configuration:
	- added lint and transpilation of code files
	- updated karma config to use transpiled files and **calculate karma code coverage on Typescript source files**
	- added generation of files import to calculate code coverage for all project source files - all-modules.spec.ts
4. Package.json changes:
	- added post installation scripts for installing all necessary dependencies i.e. jspm packages, typings packages and transpile scss
5. Project changes:
	- removed all e2e related files - e2e tests are in the other small project to easily build and deploy them without building the whole actual application
	- removed files not need in project setup structure
	- updated .gitignore to exclude coverage reports and traspiled unit tests
6. Configured default build task (watch) for Visual Studio Code and debugging js code in Visual Studio Code via Chrome browser. 

**Note**: Bootstrap & jquery bundle/minification issue is resolved in the next commit shown in simple-aurelia-app repo. The clean setup does not include jquery dependency, only bootstrap sass (js is not imported in the code). To include jquery and boostrap.js follow these steps:

1. Install jquery (check package.json file after installation and remove '^' from npm:jquery@^2.2.4 if this will be added by jspm)
{% highlight shell %}
jspm install npm:jquery@2.2.4
{% endhighlight %}
2. Add script tag in the index.html after meta tag in head
{% highlight html %}
<script src="jspm_packages/npm/jquery@2.2.4/dist/jquery.min.js"></script>
{% endhighlight %}
3. Add jquery path to exported files list in export.js
{% highlight javascript %}
'jspm_packages/npm/jquery@2.2.4/dist/jquery.min.js'
{% endhighlight %}
4. Install jquery typescript definitions if it's needed (add line to typings.json and run typings install)
{% highlight javascript %}
"globalDependencies": {
  "jquery": "registry:dt/jquery#1.10.0+20160929162922"
}
{% endhighlight %}
5. Add **import 'jquery';** in test/unit/setup.ts
5. Add **import 'bootstrap-sass';** in main.ts file before Aurelia import
6. Add **"bootstrap-sass"** to bundles.js files - include it in bundle with libraries

### Summary

That's all I have for now. If you have any interesting, smart or useful skeleton improvements that work for you in every day job, please let me know in the comments below.
