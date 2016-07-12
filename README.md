# Grunt Crash Course

> Node direct NodeJS experience is needed. Grunt modules are a NodeJS or collection of NodeJS modules abstracted to be utilized via JSON formatted config properties in the `Gruntfile.js` config file.

Grunt's config object is passed to the `.initConfig` method of the `grunt` NodeJS module. This is required to be wrapped in NodeJS' `module.exports` method. After this, almost everything is done with Grunt methods.

Use this template to when creating a new `Gruntfile.js` file (The `.loadNpmTasks()` and `.registerTask()` grunt methods will be explaind later):
```javascript
'use strict';

module.exports = function(grunt) {

  grunt.loadNpmTasks('grunt-task-to-load');
  
  grunt.registerTask('alias', ['task1', 'task2', 'task3:target']

  grunt.initConfig({
    ...Grunt tasks here...
  });
}  
```

## Tasks and Targets

> After telling Grunt to load a `task` using `grunt.loadNpmTasks()` add an object, who's property name is that of the task to be configured, to the Grunt config object. To run multiple tasks with one command, register a new task that calls multiple tasks using `grunt.registerTask()` method.

### Loading Tasks

Use `grunt.loadNpmTasks('grunt-task');` to load tasks into the grunt file. Since these grunt modules are `npm` packages, the name of the package is the name used to load it.

For instance, is you install

```shell
npm install grunt-contrib-copy
```

Then you load the grunt task with

```javascript
grunt.loadNpmTasks('grunt-contrib-copy');
```

### Configuring Tasks

> The name of the grunt module install via `npm` and the name used to configure a tasks are often different. Usually the Github page of the grunt module will give examples of how to load and configure the tasks.

The Grunt config is a JSON object. Each property in the config object is a `task` - identified by the property name. This property is also the argument give to the `grunt` command.

Given this config for the `grunt-contrib-copy` Grunt module

```javascript
grunt.loadNpmTasks('grunt-contrib-copy');

grunt.initConfig({
  copy: {
    ...Task Config Here...
  }
});
```

You would run the task with the following command:
```shell
grunt copy
```

### Task Targets

Each `task` config object is split up into one or more `targets`. These `targets` can be used to perform different operations for different files, using the same Grunt `tasks`. You can run all `targets` at once, or individual `targets` separately.

Given the following targets for `grunt-contrib-copy`:

```javascript
grunt.loadNpmTasks('grunt-contrib-copy');

grunt.initConfig({
  copy: {
    target1: {
      ...Target1 Config...
    }
    target2: {
      ...Target2 Config...
    }
  }
});
```

You can run both task targets or run them individually:
```shell
grunt copy
grunt copy:target1
grunt copy:target2
```

### Common Task Config Properties

> Most Grunt modules use common Grunt properties like `files` and `options`.

#### Files

Since Grunt mainly performance file operations, almost every module will have use the `files` object to sepcify which files to perform the task on.

Give this file structure:
```
- fromHere
--- here.txt
- fromThere
--- there.txt
- toHere
- toThere
```

With this `Gruntfile.js`:

```javascript
grunt.loadNpmTasks('grunt-contrib-copy');

grunt.initConfig({
  copy: {
    toHere: {
      files:[{
        src: ['fromHere/*.*'],
        dest: ['toHere/']
      }]
    }
    toThere: {
      files[{
        src: ['fromThere/*.*'],
        dest: ['toThere/']
      }]
    }
  }
});
```
Running
```shell
grunt copy
```
Results in:
```
- fromHere
--- here.txt
- fromThere
--- there.txt
- toHere
--- here.txt
- toThere
--- there.txt
```

#### File Source and Destination

A few patterns to specify file source and destination are common. Not all patterns are supported by all modules, so it is best to follow the pattern given in the module's examples, when in doubt.

[Compact format](http://gruntjs.com/configuring-tasks#compact-format)
```javascript
foo: {
      src: ['src/aa.js', 'src/aaa.js']
    }
```
[Files object format](http://gruntjs.com/configuring-tasks#files-object-format)
```javascript
foo: {
      files: {
        'dest/a.js': ['src/aa.js', 'src/aaa.js'],
        'dest/a1.js': ['src/aa1.js', 'src/aaa1.js'],
      },
    }
```
[Files array format](http://gruntjs.com/configuring-tasks#files-array-format)
```javascript
foo: {
      files: [
        {src: ['src/aa.js', 'src/aaa.js'], dest: 'dest/a.js'},
        {src: ['src/aa1.js', 'src/aaa1.js'], dest: 'dest/a1.js'},
      ],
    }
```
[dest-as-target format](http://gruntjs.com/configuring-tasks#older-formats) (This is an "older" format, but still commonly used)
```javascript
  concat: {
    'dest/a.js': ['src/aa.js', 'src/aaa.js'],
    'dest/b.js': ['src/bb.js', 'src/bbb.js'],
  }
```

#### File Globbing

To select or exclude file selection, multiple "globbing" and "exclusion" syntaxes are used with Grunt's file abstraction.

```javascript
  copy: {
    all: {
      files:[{
        src: ['**/*'], // Select all files recursively
        dest: ['toHere/']
      }]
    },
    html: {
      files[{
        src: ['dirName/**/*.html'], // Select html files in dirName recursively
        dest: ['toThere/']
      }]
    },
    htmlExclude: {
      files[{
        src: ['dirName/**/*.html', '!dirName/**/exclude.html'], // Select html files in dirName recursively, except any named exclude.html
        dest: ['toThere/']
      }]
    },
    allNoMinified: {
      files[{
        src: ['dirName/**/*.*', '!dirName/**/*.min.*'], // Select all files in dirName recursively, except any with .min.* extension
        dest: ['toThere/']
      }]
    }
  }
```
