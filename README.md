# Web Project Workflows
- [COURSE LINK](https://www.linkedin.com/learning/gulp-js-git-and-browserify-web-project-workflows/next-steps)

---
## 1. Get Started
### Prequisite
- Web project, HTML structure
- JavaScript
- JASON
- CSS, text editor

### Why use workflow tools?
- speed up development
- Automate useful tasks
- Handle processed languages
- Speed up your site

### Types of tools
- Editor tools
- GUI / Codekit/livereload/prepros
  - not good for team
- open source tools
  - flexible and free
  - high requirements
  - package.jason (included dependencies)
- Git/Github

### Create a package.jason for project

jason file store basic information about the application we're working on
- name
- description
- version number

open bash on current folder, generate jason file automately by commmand:
```cmd
npm init
```
- name: (workflows)
- version- semantic versioning
- description
- entry point index.js
- git repostory: ?.git

JavaScript and jason

### directory

- builds
  - development //assent need for project
  - production //need to upload to the server

### including all .js files
JavaScript and jason course
### sass and compass for CSS
Syntactically Awesome Style Sheets
- allow you create variable in CSS
- nesting: keep a better organized
- partials
- mixins: create functions
- compass: extension for css

### tracking project with git and Github
course git essential training
- git ignore file
  .gitignore
  - node_modules
  - sass-cache
  - builds/**/images/*

  ```cmd
  git init
  ```
- add all our file from project to Git so it starts tracking them
- two folders:
  - staging: temporary place to move files while working on them
  - add all files to git
  ```cmd
  git add .
  git rm file
  git commit -m "commit"
  git status
  git log
  git checkout hashnum
  git branch
  git checkout master
  git push -u origin master

  ```
  create a new repository in github
  push local repository to github

## 2. Setting up a development workflow

### Gulp.js
- Gulp uses the concept of pipes, take one flexible process it through a plug-in and then send the output of that file to another plug-in.
- Broccoli uses trees which are directories an sub-directories of the files.
- https://www.gulpjs.com.cn/docs/getting-started/
```cmd
npm install --save-dev gulp //save devDependencies
npm install --save //save dependencies
```

- create a gulpfile.js
  - tell gulp what to do
- using pipe to process coffeescript files

### Combining js
```js
var jsSources = [
  'components/scripts/pixgrid.js',
  'components/scripts/rclick.js',
  'components/scripts/template.js',
  'components/scripts/tagline.js',
]

gulp.task('js', function(){
  gulp.src(jsSources)
    .pipe(concat('script.js'))
    .pipe(gulp.dest('builds/development/js'))
});
```

```cmd
gulp js
```

### Importing Libraries with Browserify
- let you require modules in the browser by bundling up all of your dependencies
```js
gulp.task('js', function(){
  gulp.src(jsSources)
    .pipe(concat('script.js'))
    .pipe(browserify())
    .pipe(gulp.dest('builds/development/js'))
});
```

### Process Sass and compass
- SASS 是css的预处理器
- http://www.ruanyifeng.com/blog/2012/06/sass.html

```js
var sassSources = ['components/sass/style.scss'];

gulp.task('compass', function() {
  gulp.src(sassSources)
    .pipe(compass({
      sass: 'components/sass',
      image: 'builds/development/images',
      style: 'expanded'
    })
    .on('error', gutil.log))
    .pipe(gulp.dest('builds/development/css'))
});

```
## 3. Setting Up Builds and Automation
### Issuing tasks in sequence and by default
- if we want coffee script task to be executed before the JavaScript task ran, add dependencies of runing JavaScript task
```js
gulp.task('js', ['coffee'], function(){
  gulp.src(jsSources)
    .pipe(concat('script.js'))
    .pipe(browserify())
    .pipe(gulp.dest('builds/development/js'))
});
```
- create a task not have a call back
```js
gulp.task('all', ['coffee', 'js', 'compass']);
gulp.task('default', ['coffee', 'js', 'compass']);

```

### gulp.watch
- every time change the content of source file, the gulp function will run automatically to update the content in JavaScript.
```js
gulp.task('watch', function(){
  gulp.watch(coffeeSources, ['coffee']);
  gulp.watch(jsSources, ['js']);
  gulp.watch('components/sass/*.scss', ['compass']);
})
```

### gulp.connect
- run a webserver with livereload
```js
gulp.task('connect', function(){
  connect.server({
    root: 'builds/development/',
    livereload: 'true'
  });
});
```

### static reload(for html&&json file)
```js
gulp.task('watch', function() {
  gulp.watch(coffeeSources, ['coffee']);
  gulp.watch(jsSources, ['js']);
  gulp.watch('components/sass/*.scss', ['compass']);
  gulp.watch(htmlSources, ['html']);
  gulp.watch(jsonSources, ['json']);
});

gulp.task('html', function() {
  gulp.src(htmlSources)
    .pipe(connect.reload())
});

gulp.task('json', function() {
  gulp.src(jsonSources)
    .pipe(connect.reload())
});

```

## 4. Creating production Builds
### Setting up environmnet variables
```js
env = process.env.NODE_ENV || 'development';

if(env === 'development'){
  outputDir = 'builds/development/';
}else {
  outputDir = 'builds/production/';
}
```
```
$ NODE_ENV=production gulp
```
compass doesn't work?

### Uglifying conditionally with gulpif
gulp-if
gulp-uglify
```
events.js:183
      throw er; // Unhandled 'error' event
      ^
GulpUglifyError: unable to minify JavaScript
Caused by: SyntaxError: Unexpected character '`'
File: C:\Users\sdall\Desktop\Full-Stack\workflow\components\scripts\script.js
Line: 81
Col: 30
```

### mimifying html
gulp-minify-html
```js
gulp.task('html', function() {
  gulp.src('builds/development/*.html')
    .pipe(gulpif(env === 'production', minifyHTML()))
    .pipe(gulpif(env === 'production', gulp.dest(outputDir)))
    .pipe(connect.reload())
});
```

### minifying json file
gulp-minifyjson
```js
gulp.task('json', function() {
  gulp.src('builds/development/js/*.json')
  .pipe(gulpif(env === 'production', jsonminify()))
  .pipe(gulpif(env === 'production', gulp.dest('builds/production/js')))
    .pipe(connect.reload())
});
```
### compressing images
gulp-imagemin
imagemin-pngcrush

```js
gulp.task('images', function(){
  gulp.src('builds/development/images/**/*.*')
    .pipe(gulpif(env === 'production', imagemin({
      progressive: true,
      svgoPlugins: [{ removeViewBox: false }],
      use: [pngcrush()]
    })))
    .pipe(gulpif(env === 'production', gulp.dest(outputDir+'images')))
    .pipe(connect.reload())

})
```
