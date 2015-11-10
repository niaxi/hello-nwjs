# hello-nwjs

Checking out [NWJS](http://nwjs.io/)

## Requirements

install nwjs and make sure the `nw` executable is available on your PATH


## Development

Initial setup (install dev dependencies)

> npm install

Run the app

> npm start

Package for Windows 64

> npm run package:win

Runs:

> node_modules\.bin\nwbuild.cmd -p win64 -v 0.12.3 -o ./dist "./**/**,!./node_modules/**/**,!./dist/**"



## Hacks

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