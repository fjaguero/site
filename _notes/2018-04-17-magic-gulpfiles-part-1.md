---
title: 'The magic of gulpfiles: Part I'
date: 2018-04-16 01:15:49 Z
tags:
- Gulp
- Javascript
- Tools
layout: post
---

![Gulp](http://cl.ly/image/3629180a1X02/gulp.jpg)

I've been using Gulp for a while and **I'm really in love with it so far**. I prefer code (Gulp) over config (Grunt) since the node style looks more familiar and flexible.

In this first post I want to share the *gulpfile* I use to build an extension, both for the stage environment and distribution via the Chrome Store. I use another gulpfile for the webapp I'm working and I will probably share it the next week. I commentend almost every line of this one so it's as clear as possible.

Like many build files, it's a work in progress and I'm always up to improvements. The only thing that you won't see in this config file is something related with tests. We will implement the test tasks as soon as we choose a test suite for the extension.

## Packages used

``` language-javascript
// Where the magic starts
var gulp = require('gulp'),

// Minify css with [clean-css](https://github.com/GoalSmashers/clean-css), including optional caching
minifycss = require('gulp-minify-css'),

// [JSHint](http://www.jshint.com/) plugin for gulp
jshint = require('gulp-jshint'),

// Minify JavaScript with [UglifyJS2](https://github.com/mishoo/UglifyJS2)
uglify = require('gulp-uglify'),

// Provides simple file renaming methods
rename = require('gulp-rename'),

// A plugin for livereload best used with the livereload chrome/firefox extension
livereload = require('gulp-livereload'),

// Tiny LiveReload server, background-friendly
lr = require('tiny-lr'),

// Used to run the LiveReload server
server = lr(),

// Generates complexity analysis reports
plato = require('gulp-plato'),

// A string replace plugin
replace = require('gulp-replace'),

// Generates a TODO.md file from your javascript todos and fixmes
todo = require('gulp-todo'),

// A plugin for removing files and folders
clean = require('gulp-clean'),

// Strips console and debugger statements from JavaScript code
stripDebug = require('gulp-strip-debug'),

// Utility functions for gulp plugins
gutil = require('gulp-util'),

// Removes unneeded whitespaces, line-breaks, comments, etc from HTML
cleanhtml = require('gulp-cleanhtml'),

// ZIP compress files
zip = require('gulp-zip'),

// We will get the extension version from the manifest file
manifest = require('./manifest.json')

// Files that will be copied when building stage/dist
var filesToMove = [
    './_locales/**/*.*',
    './images/*.*/'
];

```


## Tasks used

### Cleaning directories

**clean-stage:** Removes everything from the /stage directory. This task is used just before the *stage* taks is run.

``` language-javascript
// Cleans the stage directory
gulp.task('clean-stage', function() {

	// Logs a colored message on the console
    gutil.log(gutil.colors.cyan('===== CLEANING STAGE DIRECTORY ======'));

    return gulp.src(['stage/*'], {
        read: false
    })
        .pipe(clean());
});
```


**clean-dist:** Removes everything from the /dist directory. This task is used just before the *dist* taks is run.

``` language-javascript
// Cleans the dist directory
gulp.task('clean-dist', function() {

	// Logs a colored message on the console
    gutil.log(gutil.colors.cyan('===== CLEANING DIST DIRECTORY ======'));

    return gulp.src(['dist/*'], {
        read: false
    })
        .pipe(clean());
});
```

### Javascript

**scripts:** Runs JSHint on the Javascript files

``` language-javascript
// Runs JSHint on scripts
gulp.task('scripts', function() {
    return gulp.src('javascripts/*.js')
        .pipe(jshint())
        .pipe(jshint.reporter('jshint-stylish'))
    // .pipe(jshint.reporter('fail'))
    .pipe(livereload(server));
});
```

**watch:** Watches the Javascript files and run JSHint in every change

```language-javascript
// Watch .js files on change
gulp.task('watch', function() {
	
    // Starts LiveReload server
    server.listen(35729, function(err) {
        if (err) {
            return console.log(err);
        }
        gulp.watch('javascripts/*.js', ['scripts']);
    });

});
```

### Building

**stage:** Builds the stage extension folder

``` language-javascript
// Stage extension packaging. clean-stage is run before starting.
gulp.task('stage', ['clean-stage'], function() {

	// Logs a colored message on the console
    gutil.log(gutil.colors.yellow('===== CREATING STAGE EXTENSION ======'));

    // Moves static files to the new stage folder
    gulp.src(filesToMove, {
        base: './'
    })
        .pipe(gulp.dest('stage'));

    // Replaces domain and Mixpanel references from production to stage
    gulp.src('javascripts/*.js')
        .pipe(replace('Extension Production', 'Extension Stage'))
        .pipe(replace('12345123451234', '543215432154321'))
        .pipe(gulp.dest('stage/javascripts'));

    // Minifies CSS
    gulp.src('stylesheets/*.css')
        .pipe(rename({
            suffix: '.min'
        }))
        .pipe(minifycss())
        .pipe(gulp.dest('stage/stylesheets'));

    // Replaces references to JS and CSS to the minified files
    gulp.src('./*.html')
        .pipe(replace('.css', '.min.css'))
        .pipe(gulp.dest('stage/'));

    // Replaces content-script css reference on the manifest.json
    gulp.src('./manifest.json')
        .pipe(replace('content-script.css', 'content-script.min.css'))
        .pipe(gulp.dest('stage/'));

});
```
**zip-stage:** Creates a zip file to distribute the stage extension

``` language-javascript
// Creates a zip file to distribute the stage extension
gulp.task('zip-stage', ['stage'], function() {

    // Gets the current version from the manifest file
    var version = manifest.version;

    // Logs a colored message on the console
    gutil.log(gutil.colors.yellow('===== PACKAGING STAGE v' + version + ' ZIP ======'));

    return gulp.src('./stage/**/*.*')
        .pipe(zip('stage-' + version + '-ext.zip'))
        .pipe(gulp.dest('./'));

});
```

**dist:** Builds the distribution extension folder

``` language-javascript

// Distribution extension packaging. clean-dist is run before starting.
gulp.task('dist', ['clean-dist'], function() {
	
    // Logs a colored message on the console
    gutil.log(gutil.colors.yellow('===== CREATING DIST EXTENSION ======'));

    // Moves static files to the new dist folder
    gulp.src(filesToMove, {
        base: './'
    })
        .pipe(gulp.dest('dist'));

    // Replaces domain and Mixpanel references from stage to production and change icon
    // Uglifies and removes debug statements from Javascript files
    gulp.src('javascripts/*.js')
        .pipe(replace('stage.domain.com', 'www.domain.com'))
        .pipe(replace('icon-logged-in-stage.png', 'icon-logged-in.png'))
        .pipe(replace('Extension Stage', 'Extension production'))
        .pipe(replace('543215432154321', '12345123451234'))
        .pipe(uglify())
        .pipe(stripDebug())
        .pipe(gulp.dest('dist/js'))
        .on('error', gutil.log);

    // Minifies CSS
    gulp.src('stylesheets/*.css')
        .pipe(rename({
            suffix: '.min'
        }))
        .pipe(minifycss())
        .pipe(gulp.dest('dist/css'));

    // Replaces references to JS and CSS to the minified files
    gulp.src('./*.html')
        .pipe(replace('src="javascripts/', 'src="js/'))
        .pipe(replace('stylesheets/', 'css/'))
        .pipe(replace('.css', '.min.css'))
        .pipe(cleanhtml())
        .pipe(gulp.dest('dist/'));

    // Replaces stage references to production and renames directories
    gulp.src('./manifest.json')
        .pipe(replace('icon-48-stage.png', 'icon-48.png'))
        .pipe(replace('icon-16-stage.png', 'icon-16.png'))
        .pipe(replace('Extension Stage', 'Extension Production'))
        .pipe(replace('stylesheets', 'css'))
        .pipe(replace('javascripts', 'js'))
        .pipe(replace('content-script.css', 'content-script.min.css'))
        .pipe(gulp.dest('dist'));
});

```
**zip-dist:** Creates a zip file to distribute the production extension

``` language-javascript
// Creates zip file to distribute the production extension
gulp.task('zip-dist', ['dist'], function() {

    // Gets the current version from the manifest file
    var version = manifest.version;

    // Logs a colored message on the console
    gutil.log(gutil.colors.yellow('===== PACKAGING v' + version + ' ZIP ======'));

    return gulp.src('./dist/**/*.*')
        .pipe(zip('production-' + version + '-ext.zip'))
        .pipe(gulp.dest('./'));

});
```

### Miscellaneous

**todo:** Builds a TODO.md file from the javascript todos and fixmes. This is useful when you know that a code can be improved but you don't know how to do it yet.

``` language-javascript
// Generate TODO.md file from the javascript todos and fixmes
gulp.task('todo', function() {
    gulp.src('javascripts/*')
        .pipe(todo())
        .pipe(gulp.dest('todo'));
});
```

**plato:** Generates the plato directory with a complexity analysis. An example from the [jQuery library](http://es-analysis.github.io/plato/examples/jquery/).

``` language-javascript
// Generates Plato complexity analysis
gulp.task('plato', function() {
    gulp.src('javascripts/*')
        .pipe(plato('report', {
            jshint: {
                options: {
                    strict: false
                }
            },
            complexity: {
                trycatch: true
            }
        }));
});
```
I hope you found something useful to use in your own gulpfile. They are hundres of packages to use and sometimes I'm not aware of the great things that can be done with Gulp.

Mind to share your gulpfile? 

Thanks!
