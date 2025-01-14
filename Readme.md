One line has been changed from the original.

```js
// next = path.join(opts.includePath || path.dirname(p), fileLocation);
next = path.join(level === 1 ? opts.includePath : path.dirname(p), fileLocation);
```

# grunt-includes [![Build Status](https://travis-ci.org/vanetix/grunt-includes.png?branch=master)](https://travis-ci.org/vanetix/grunt-includes)

**_Requires grunt >= 1.0.0_**

A grunt task for including a file within another file (think php includes). _Circular_ imports will break the recursive strategy. _All includes retain parent and child indentation_

## Getting Started

Install this grunt plugin with: `npm install grunt-includes-relative-path --save-dev`

Then add this line to your project's `Gruntfile.js`:

```javascript
grunt.loadNpmTasks('grunt-includes-relative-path');
```

An example site can be found at [grunt-includes-example](https://github.com/vanetix/grunt-includes-example).

## Options

#### flatten

Type: `String`
Default: `false`

Flatten all file sources to the destination directory. For example `source/file/path.html` will be shortened to `path.html`.

#### banner

Type: `String`
Default: `''`

String processed by `grunt.template.process`, and prepended to every compiled file.

#### duplicates

Type: `Boolean`
Default: `true`

Permits the same file to be included twice.

#### debug

Type: `Boolean`
Default: `false`

Enables debug mode compilation.

#### silent

Type: `Boolean`
Default: `false`

Silences file save success messages.

#### includePath

Type: `String` | `Array`
Default: ``

Indicates the path(s) to use when looking for included files. Instead of using relative path in the include statements, use includePath as base directory. When using an array for includePath, the first path for a file that is valid will be used.

#### filenamePrefix

Type: `String`
Default: ``

Prepend the defined string to each included filename before reading them. Thus making possible to include "include_file.ext" by using directly "file.ext".

#### filenameSuffix

Type: `String`
Default: ``

Append the defined string to each included filename before reading them. Thus making possible to include "file.ext" by using directly "file".

#### templateFileRegexp

Type: `RegExp`
Default: `/\{\{\s?file\s?\}\}/`

Regular expression for matching and replacing template text. Example: `Start of template | {{file}} | End of template`.

#### template

Type: `String`

Template to be rendered into, `{{fileName}}` and `{{file}}` will be replaced with the filename and file contents respectively.

#### wrapperFileRegexp

Type: `RegExp`
Default: `/\{\{\s?file\s?\}\}/`

Regular expression for matching and replacing file contents within a wrapper. Example: `Start of wrapper | {{file}} | End of wrapper`.

#### wrapper

Type: `String` | `Path`

Wrapper to be rendered into, `{{fileName}}` and `{{file}}` will be replaced with the filename and file contents respectively.

#### includeRegexp

Type: `RegExp`
Default: `/^(\s*)include\s+"(\S+)"\s*$/`
Matches: `include "some/file.html"`

Sets the regular expression used to find _include_ statements.

A regex group is used to identify the important parts of the include statement. When constructing your own regex, it can contain up to two groups (denoted by parentheses `()` in the regular expression):

1.  The indentation whitespace to be appended to the front of the included file's contents
2.  The file location

**All regular expressions used must contain at least one group.** If only one group is used, it will be assumed to contain the file path.

## Usage

You can use this plugin to build html templates.

```javascript
includes: {
  files: {
    src: ['path/to/foo.html', 'path/to/bar.html'], // Source files
    dest: 'tmp', // Destination directory
    flatten: true,
    cwd: '.',
    options: {
      silent: true,
      banner: '<!-- I am a banner <% includes.files.dest %> -->'
    }
  }
}
```

Or even organize your static files.

```javascript
includes: {
  js: {
    options: {
      includeRegexp: /^\/\/\s*import\s+['"]?([^'"]+)['"]?\s*$/,
      duplicates: false,
      debug: true
    },
    files: [{
      cwd: 'assets/js/',
      src: '**/*.js',
      dest: 'assets/dist/js/',
    }],
  },
},
watch: {
  js: {
    files: ['assets/js/**/*.js'],
    tasks: ['includes:js', 'jshint']
  },
},
```

Or build files with a wrapper, where `{{file}}` gets replaced with the contents of the file being parsed.

```javascript
includes: {
  target: {
    options: {
      wrapper: 'src/wrapper.html'
    },
    files: [{
      cwd: 'src/',
      src: '*.html',
      dest: 'dist/',
    }],
  },
},
```

## Troubleshooting

- **Grunt is copying the wrong files to the wrong locations.** If you're using `includes` as a [multitask](http://gruntjs.com/creating-tasks#multi-tasks) and are specifying a `files` object with `src`, `dest`, and `cwd`, you must supply it in an array using the [files array format](http://gruntjs.com/configuring-tasks#files-array-format). If you shorthand it by setting `files: {src: ... }`, Grunt will misinterpret it into the [files object format](http://gruntjs.com/configuring-tasks#files-object-format) and mysteriously copy artifacts into (potentially new) `src`, `dest`, and `cwd` directories. This appears to affect only multitask usage.

## Example

_index.html_

```html
<html>
  <head>
    <title>Show me</title>
  </head>
  <body>
    include "content.html"
  </body>
</html>
```

_content.html_

```html
<div class="content">
  <h1>Content</h1>
  <p>More content</p>
</div>
```

**Produces**

```html
<html>
  <head>
    <title>Show me</title>
  </head>
  <body>
    <div class="content">
      <h1>Content</h1>
      <p>More content</p>
    </div>
  </body>
</html>
```

## Release History

- 1.1.0 - Release wrapper ability by [laurenhamel](https://github.com/laurenhamel)
- 0.5.0 - Given a full include path, filenamePrefix and filenameSuffix will now be ignored.
- 0.4.0 - Release templating ability. Thanks [nathankot](https://github.com/nathankot)!
- 0.3.7 - Various bug fixes and updates.
- 0.3.6 - Update replacement so only the include statement is replace. Thanks [SAPikachu](https://github.com/SAPikachu)!
- 0.3.5 - Add options for filename prefix and suffix.
- 0.3.4 - Add explicit include path option.
- 0.3.2 - Add silent flag to silence save messages.
- 0.3.0 - Add indention preservation and banner support
- 0.2.3 - Fix bug when building source files from a different platform. Thanks [wGEric](https://github.com/wGEric)!
- 0.2.0 - Support for expandable paths and debugging. Thanks [@ktmud](https://github.com/ktmud)!
- 0.1.0 - Updated for grunt 0.4
- 0.0.1 - Initial release

## License (MIT)

Copyright (c) 2012-2015 Matt McFarland and Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions: The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
