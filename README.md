# Hello NW.js

Quick n dirty base for creating a desktop app based on [NWJS](http://nwjs.io/).

## Requirements

Installed by the install script in the Development section below.

* nwjs -- builds against v0.12.3


## Development

To initial setup (install dev dependencies)

> npm install

To run the app in development mode

> npm start

To package for Windows 64

> npm run package:win

Runs:

> node_modules\.bin\nwbuild.cmd -p win64 -v 0.12.3 -o ./dist "./**/**,!./node_modules/**/**,!./dist/**"

To package for OSX 64

> npm run package:mac

To see all available build commands

> npm run



## Hacks *#$!

nwbuild cli does not currently support glob patterns for `files` param. I need in order to have a simple project dir but ignore certain dirs on build.

Until I commit this upstream...

Edit
\node_modules\nw-builder\bin\nwbuild Ln:56

replace

```
// Error if there are no files
var files = argv._[0];
if(!files) {
    optimist.showHelp();
    process.exit(0);
}

var options = {
    files: path.resolve(process.cwd(), files) + '/**/*',
    platforms: argv.platforms.split(','),
    version: argv.version,
    macIcns: argv.macIcns || false,
    winIco: argv.winIco || false,
    cacheDir: path.resolve(__dirname, '..', 'cache'),
    buildDir: path.resolve(process.cwd(), argv.buildDir),
    forceDownload: argv.forceDownload
};
```

with

```
// Error if there are no files
var files = argv._[0];
if(!files) {
    optimist.showHelp();
    process.exit(0);
}

if(files.indexOf(',') > -1) {
    files = files.split(',');
}

var options = {
    // files: path.resolve(process.cwd(), files) + '/**/*',
    files: files,
    platforms: argv.platforms.split(','),
    version: argv.version,
    macIcns: argv.macIcns || false,
    winIco: argv.winIco || false,
    cacheDir: path.resolve(__dirname, '..', 'cache'),
    buildDir: path.resolve(process.cwd(), argv.buildDir),
    forceDownload: argv.forceDownload
};
```

it essentially enables user to pass in comma separated list of paths

```
if(files.indexOf(',') > -1) {
    files = files.split(',');
}
```

then passes the file paths in to be globbed later -- no attempts to resolve them

```
  files: files,
```