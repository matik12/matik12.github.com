---
layout: post
title: "Karma Code Coverage for all typescript files"
categories: [Karma, Coverage, Gulp, Typescript, Aurelia]
disqus: true
---

![Karma Coverage](/images/2017-01-08/coverage-all-files.png)

Using Karma-Coverage plugin with JavaScript module imports in most cases can lead you to some odd overall coverage results. 
In this post I will provide you a solution, how to make sure that code coverage is calculated based on all source files.

<!--more-->

### Why coverage shows odd results?

Karma-Coverage can run code calculations on all source files that have been loaded in the browser. Thus, when you are using module imports in your typescript files, then the code coverage will be calculated based on all imported source files. This generally means, **your coverage will include only those source files, that were imported in the test specifications**. But, hey is it what we really wanted? I guess not, because when you add new source code file without test specification importing it, you will get coverage that not include this source file. What is more, later on you will not be sure that your coverage level is good enough to provide sustainable code, because you don't know how much of your code is not covered by tests at all.

### Proposed solution

After some research I have done, I couldn't find a solution out of the box, so I came up with my own implementation. Basically, we can **generate typescript test specification file, which contains imports for all source files**. Thanks to that, code coverage will include all files and if we don't write tests for our code, code coverage level will decrease. Below, actual implementation is based on Aurelia Skeleton project. 

#### Install dependencies

To generate new file, we will add new gulp task. It has two npm package dependencies -> **fs**, **fs-tools**, so we will install it first.

```js
npm install fs fs-tools --save-dev
```

#### Define path variables

Then define some path variables used in gulp task as below - (build/paths.js)

{% highlight javascript %}
testRoot: 'test/',

coverage: {
  instrumentationFilePath: testRoot + 'unit/all-modules.spec.ts',
  excludePaths: [
    '.d.ts',
    '\\some-path-to-exclude\\'
  ]
}
{% endhighlight %}

Sometimes, you want to exclude files from coverage, because this code is inherited and you will likely not change it. For this case, you can use **excludePaths** array. Make sure, that instrumentation file path is included in karma.conf.js files array to run this file i.e. 'test/unit/all-modules.spec.ts' and files: ['test/unit/\*\*/\*.ts'].

#### Create new gulp task

Now, we can add new gulp task, to generate imports (build/tasks/test.js).

{% highlight javascript %}
var fs = require('fs');
var fsTools = require('fs-tools');
...

gulp.task('coverage-instrumentation-file', () => {
    if (fs.existsSync(paths.coverage.instrumentationFilePath)){
      fs.unlinkSync(paths.coverage.instrumentationFilePath);
    }

    var file = fs.createWriteStream(paths.coverage.instrumentationFilePath, { 'flags': 'a' });
	
    // paths.root - this is the path for all your source code i.e. 'src/'
    fsTools.walkSync(paths.root, '.ts$', function(path){
      // exclude all file with machting paths
      var excludeFile = false;      
      paths.coverage.excludePaths.forEach(exclude => {
        if (path.indexOf(exclude) !== -1) {
          console.log('exclude ' + path);
          excludeFile = true;
          return;
        }
      });

      if (excludeFile) {
        return;
      }

      file.write('import \''+ path.split('\\').join('/').replace('.ts', '') + '\';\n');
    });
});
{% endhighlight %}

It is a really simple task. First it will clean and create this new specification file in proper location. Then it will pick up every ts file in paths.root location (source code) and perform two actions - check if this ts file path is exclude, then the file is omitted, otherwise will write import statement to the instrumentation file. Imported file path needs to use slash char, so we split all backslashes and join strings with proper slash char. Example output file looks like below one.

{% highlight typescript %}
import 'src/app';
import 'src/config/app.jsnlog';
import 'src/config/app.oauth';
import 'src/main';
...
{% endhighlight %}

#### Ignore generated file

We are almost done, one last thing is to add this generated file to git ignore and that's all i.e. '/test/unit/all-modules.spec.ts' 

### Summary

If you want to measure your js code coverage level, make sure you do it right and rely on correct results. Happy js testing!
