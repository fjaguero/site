---
title: 'The magic of gulpfiles: Part II'
date: 2018-04-16 01:15:49 Z
tags:
- Gulp
- Javascript
- Tools
layout: post
---

![gulp II](http://cl.ly/image/3o1b0P0a2S0z/gulp2.png)

**This is a continuation of [Part I](http://thewebistheplatform.com/magic-gulpfiles-part-1/)**

In this second part I want to share one of the best gulpfiles out there, the [web-startet-kit](http://github.com/google/web-starter-kit) one by the Google team.

Note that this gulpfile uses the package [gulp-load-plugins](https://www.npmjs.org/package/gulp-load-plugins), which let us load any gulp plugins in from the package.json.

Let's discuss the code by tasks that haven't been covered in the first post:

#### 1. Image optimization
Thanks to image-min we can get a better performance by trimming some size.

```language-javascript
gulp.task('images', function() {
  return gulp.src('app/images/**/*')
    .pipe($.cache($.imagemin({
      progressive: true,
      interlaced: true
    })))
    .pipe(gulp.dest('dist/images'))
    .pipe($.size({
      title: 'images'
    }));
});
```

#### 2. Stylesheets compilation and prefixing

This is a typical SASS compiling taks. The interesting part here is the autoprefixing. Note that the task uses a browsers array to set the prefixing preferences.

The SASS compilation is done with rubySass but if you want to avoid the ruby dependency you can still use [gulp-sass](http://https://www.npmjs.org/package/gulp-sass).

```language-javascript
gulp.task('styles', function() {
  // For best performance, don't add Sass partials to `gulp.src`
  return gulp.src([
      'app/styles/*.scss',
      'app/styles/**/*.css',
      'app/styles/components/components.scss'
    ])
    .pipe($.changed('styles', {
      extension: '.scss'
    }))
    .pipe($.rubySass({
        style: 'expanded',
        precision: 10
      })
      .on('error', console.error.bind(console))
    )
    .pipe($.autoprefixer(AUTOPREFIXER_BROWSERS))
    .pipe(gulp.dest('.tmp/styles'))
    .pipe(gulp.dest('dist/styles'))
    .pipe($.size({
      title: 'styles'
    }));
});
```

#### 3. Assets optimization
Note how the ```$.if``` is used to detect the file type. The *remove unused CSS* is really useful to cut down boilerplate CSS lines.

The tasks here are:
 
 - Concatenate And Minify JavaScript
 - **Remove unused CSS**
 - Concatenate And Minify Styles
 - Minify HTML


```language-javascript
 gulp.task('html', function() {
  var assets = $.useref.assets({
    searchPath: '{.tmp,app}'
  });
  return gulp.src('app/**/*.html')
    .pipe(assets)
    // Concatenate And Minify JavaScript
    .pipe($.if('*.js', $.uglify({
      preserveComments: 'some'
    })))
    // Remove Any Unused CSS
    // Note: If not using the Style Guide, you can delete it from
    // the next line to only include styles your project uses.
    .pipe($.if('*.css', $.uncss({
      html: [
        'app/index.html',
        'app/styleguide.html'
      ],
      // CSS Selectors for UnCSS to ignore
      ignore: [
        /.navdrawer-container.open/,
        /.app-bar.open/
      ]
    })))
    // Concatenate And Minify Styles
    .pipe($.if('*.css', $.csso()))
    .pipe(assets.restore())
    .pipe($.useref())
    // Update Production Style Guide Paths
    .pipe($.replace('components/components.css', 'components/main.min.css'))
    // Minify Any HTML
    .pipe($.if('*.html', $.minifyHtml()))
    // Output Files
    .pipe(gulp.dest('dist'))
    .pipe($.size({
      title: 'html'
    }));
});
```

#### 4. Watch files for changes and reload
Here where the magic comes with BrowserSync.

**BrowserSync** will let us have a synchronised browser withouth using third party extensions. The good part is that it will be serving the dist files.

```language-javascript
gulp.task('serve', ['styles'], function() {
  browserSync({
    notify: false,
    // Run as an https by uncommenting 'https: true'
    // Note: this uses an unsigned certificate which on first access
    // will present a certificate warning in the browser.
    // https: true,
    server: {
      baseDir: ['.tmp', 'app']
    }
  });
  gulp.watch(['app/**/*.html'], reload);
  gulp.watch(['app/styles/**/*.{scss,css}'], ['styles', reload]);
  gulp.watch(['app/scripts/**/*.js'], ['jshint']);
  gulp.watch(['app/images/**/*'], reload);
});
// Build and serve the output from the dist build
gulp.task('serve:dist', ['default'], function() {
  browserSync({
    notify: false,
    // Run as an https by uncommenting 'https: true'
    // Note: this uses an unsigned certificate which on first access
    // will present a certificate warning in the browser.
    // https: true,
    server: {
      baseDir: 'dist'
    }
  });
});
```

#### Bonus-task: Google PageSpeed Insights
Here you can get some metrics about your web performance on mobile and desktop.

```language-javascript
// Update `url` below to the public URL for your site
gulp.task('pagespeed', pagespeed.bind(null, {
  // By default, we use the PageSpeed Insights
  // free (no API key) tier. You can use a Google
  // Developer API key if you have one. See
  // http://goo.gl/RkN0vE for info key: 'YOUR_API_KEY'
  url: 'https://example.com',
  strategy: 'mobile'
}));
```

### The rise of Gulp

In the last months I've seen more and more projects using gulp instead of Grunt. Google is one of the companies that is pushing forward this movement, specially [Addy Osmani](https://github.com/addyosmani) on his projects.

