# ```browserify``` + ```vinyl-transform``` to create individual bundles from multiple file sources with the same transformation

This recipe shows you how to use vanilla [```browserify```](https://github.com/substack/node-browserify) (with some help from [```vinyl-transform```](https://github.com/hughsk/vinyl-transform)) to create individual bundles from multiple file sources using glob patterns (for eg: ```./src/**/*.js```),
and move away from using [```gulp-browserify```](https://github.com/deepak1556/gulp-browserify).

This recipe would be more familiar to current ```gulp-browserify``` who are used to using the following recipe

```javascript
var gulp = require('gulp');
var browserify = require('gulp-browserify');
var uglify = require('gulp-uglify');

gulp.task('browserify', function () {

  return gulp.src(['./src/*.js'])
    .pipe(browserify())
    .pipe(uglify())
    .pipe(gulp.dest('./dist-g'));
});
```

And here's the recipe replacing ```gulp-browserify``` with ```browswerify``` + ```vinyl-transform```

```javascript
var gulp = require('gulp');
var browserify = require('browserify');
var transform = require('vinyl-transform');
var uglify = require('gulp-uglify');

gulp.task('browserify', function () {
  var browserified = transform(function(filename) {
    var b = browserify(filename);
    return b.bundle();
  });
  
  return gulp.src(['./src/*.js'])
    .pipe(browserified)
    .pipe(uglify())
    .pipe(gulp.dest('./dist'));
});
```
Easy-peezy, lemon-squeezy.

We simply use ```vinyl-transform``` to wrap around the regular [```ReadableStream```](http://nodejs.org/api/stream.html) 
returned by ```b.bundle();```, so that we can use it down the pipeline as a vinyl file object.
```vinyl-transform``` takes care of creating both streaming and buffered vinyl file objects.

## Running the example

1. Run ```gulp```
2. Open ```index.html``` and ```index-2.html``` in a browser (Tested on Safari / Chrome).
3. Profit!

## Example details

1. There are 3 scripts in ```src``` folder
    1. ```foo.js```: a simple module that returns an array of string
    2. ```h3.js```: a script that ```require('./foo');``` and append ```<h3>``` for each string from ```foo`` to the browser document
    3. ```p.js```: a script that ```require('./foo');``` and append ```<p>``` for each string from ```foo`` to the browser document
2. Running ```gulp``` will ```browserify``` files with ```./src/*.js``` glob pattern.
    * It will create a separate bundle for each file in the ```src``` folder and write it to the ```dist``` folder.
3. ```index.html``` loads the bundled ```p.js``` script
4. ```index-2.html``` loads the bundled ```h3.js``` script

## Notes


### 56 reasons not to use ```gulp-browserify``` (or most gulp-* wrappers) and counting

The main ```browserify``` library has enough [open issues](https://github.com/substack/node-browserify/issues) at the moment
that it makes hard to guarantee that ```gulp-browserify``` will always be up-to-date.

In fact, since 13 March 2014, the maintainer of ```gulp-browserify``` has [stopped updating the codebase](https://github.com/deepak1556/gulp-browserify/commits/master).
