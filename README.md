# gulp-from-config-rg

**`gulp-from-config-rg`** provides ability to run gulp tasks from configs.

> Version for rg stack, fork from [https://github.com/nanomen/gulp-from-config](https://github.com/nanomen/gulp-from-config)

## Why

- Team work on the same project without risk of breaking other tasks
- Store multiple typical tasks as configs
- Write clear and specific to application gulpfile and keep routine tasks in configs

### Caveats

- tests and linter coverage needed

## Install

```bash
# Don't forget to install gulp globaly
# $ sudo npm install -g gulp
$ npm install gulp gulp-from-config-rg
```

## As simple as

Install plugins that you need:

```bash
# This command will install sass compiler for gulp
$ npm install gulp-sass --save-dev
```

Write tasks in JSON configs and place them in ./configs or any other folder

```javascript

// Load gulp and gulp-from-config-rg
var gulp = require('gulp'),
    gulpFromConfig = require('gulp-from-config-rg');

    // Set config files path
    gulpFromConfig.setConfigsPath('configs');

    // Create tasks
    gulpFromConfig.createTasks(gulp);
```

Run them as any other gulp tasks from console (by task name):

```bash
# This command will search for build task and run it
$ gulp build
```

## Usage

```javascript
'use strict';

/**
 *  At the beginning load:
 *  - gulp
 *  - gulp-from-config-rg
 */
var gulp = require('gulp'),
    gulpFromConfig = require('gulp-from-config-rg')
    tasks = []; // declare tasks list array

    /**
     *  First option is to get tasks from configs
     *  and set path to files.
     *
     *  For example to ./configs directory
     */
    gulpFromConfig.setConfigsPath('configs');

    /**
     *  Or define config
     */
    var task = {
        expired: "07-22-2018" // task expired date
        name: "styles", // module task name
            subTasks: [
                {
                    name: "sass", // technical task name
                    dest: "/dest/css", // path to build
                    sourcemaps: true, // enable sourcemaps
                    src: {
                        include: [
                            "/src/sass/*.sass" // files to proceed
                        ],
                        exclude: [
                            "/src/sass/_*.sass" // files to ignore
                        ]
                    },
                    plugins: [
                        {
                            "name": "gulp-sass", // gulp-sass plugin
                            "options": {
                                "outputStyle": "compressed" // will be passed to plugin parameter
                            }
                        }
                    ]
                }
            ]
    };

    /**
     *  And pass it as Array to setConfigs function
     */
    gulpFromConfig.setConfigs([task]);

    /**
     *  Callback function can be triggered on completion of subtasks
     *  Sub task config is passed as parameter
     */
    var callback = function(config) {
        console.log('Sub task config:', config);
    }
    gulpFromConfig.setCallback(callback);

    /**
     *  Define tasks based on configs
     *  Run like normal gulp task 'gulp styles'
     */
    tasks = gulpFromConfig.createTasks(gulp);

    /**
     *  Or if you need to run all of them
     *  pass tasks array to default task
     *  and run 'gulp'
     */
    gulp.task('default', tasks, function() {
        console.log('All tasks are done!');
    });
```
> Example gulpfile.jsmake sure installing them

## parameters

If you want run task without expire date, add --no_expire parameter or alias --ne. For example, sudo -u admin gulp tmpl --expire
> --no_expire(--ne) parameter

If you want run task without gulp-imagemin plugin, add --imgmin=no parameter. For example, sudo -u admin gulp tmpl --imgmin=no
> --imgmin=no parameter


## config

```javascript
{
    "name": "production", // task name which can be called by 'gulp production'
    "subTasks": [
        {
            "name": "script", // sub task name
            "dest": "/dest/js", // for gulp.dest('/dest/css')
            "sourcemaps": false, // if sourcemaps are required
            "browserify": {
                "transform": ["ractivate"] // Set extra browserify transforms (make sure that transform installed!)
                "file": "prod.js" // You can specify file name. Will be task name by default ('production')
            },
            "watch": {
                // watch if environment: dev
                dev: [ // if array is empty will watch src files
                    "/src/js/*.js", // watch changes on source files
                    "/src/js/_*.js"
                ],
                // unwatch if environment: prod
                prod: false
            },
            "src": {
                "include": [
                    "/src/js/*.js" // will be processed
                ],
                "exclude": [
                    "/src/js/_*.js" // will be ignored
                ]
            },
            "plugins": [
                {
                    "name": "gulp-uglify", // gulp-uglify plugin (make sure that plugin installed!)
                    "options": {
                        "mangle": false // will be passed into gulp.pipe(uglify(options))
                    }
                }
            ]
        },
        {
            "similarSubTask",
            "dest" "~",
            "src": "~", // use ~ to get any props from previous subtasks
            "plugins": [
                "~gulp-uglify" // use ~PLUGIN_NAME if you wish to copy this plugin config from previous subtask (or use plugins: "~" to repeat all pugins)
            ]
        }
    ]
};
```
> Example production.json

## watch option examples

```javascript
{
    ...
    "subTasks": [
        "watch": true
    ]
    ...
}
```
> Example default watch

```javascript
{
    ...
    "subTasks": [
        "watch": {
            dev: true,
            prod: false
        }
    ]
    ...
}
```
> Example watch in dev environment

```javascript
{
    ...
    "subTasks": [
        "watch": {
            dev: false, // unwatch in development mode
            prod: [ // if array is empty will watch src files
                "/src/js/*.js", // watch changes on source files
                "/src/js/_*.js"
            ]
        }
    ]
    ...
}
```
> Example watch in prod environment

```javascript
{
    ...
    "subTasks": [
        "watch":  [ // if array is empty will watch src files
            "/src/js/*.js", // watch changes on source files
            "/src/js/_*.js"
        ]
    ]
    ...
}
```
> Example watch must be array

## browserify option example
```javascript
{
    ...
    "subTasks": [
        "watch": {
            dev: false, // unwatch in development mode
            prod: [ // if array is empty will watch src files
                "/src/js/*.js", // watch changes on source files
                "/src/js/_*.js"
            ]
        }
    ],
    "browserify": {
        "transforms": [
          {
            "name": "eslintify"
          },
          {
            "name": "ractivate",
            "options": {
              "extensions": [
                ".ihtml"
              ]
            }
          }
        ],
        "watchify": {
          "dev": true,
          "prod": false
        }
      },
    ...
}
```
> Example browserify watch
