[![version](https://img.shields.io/npm/v/gulp-rev-collector-concat.svg "version")](https://www.npmjs.com/package/gulp-rev-collector-concat)&nbsp;
[![Build Status](https://img.shields.io/travis/Froguard/gulp-rev-collector-concat.svg)](https://travis-ci.org/Froguard/gulp-rev-collector-concat)&nbsp;
[![GitHub issues](https://img.shields.io/github/issues/Froguard/gulp-rev-collector-concat.svg)](https://github.com/Froguard/gulp-rev-collector-concat/issues?q=is%3Aopen+is%3Aissue)&nbsp;
[![download](https://img.shields.io/npm/dt/gulp-rev-collector-concat.svg "download")](https://www.npmjs.com/package/gulp-rev-collector-concat)&nbsp;
[![license](https://img.shields.io/github/license/froguard/gulp-rev-collector-concat.svg)](https://github.com/froguard/gulp-rev-collector-concat/blob/master/LICENSE)

# [gulp-rev-collector](https://www.npmjs.com/package/gulp-rev-collector)-concat

- Base on [gulp-rev-collector](https://github.com/shonny-ua/gulp-rev-collector), And has the same function as [gulp-rev-collector](https://www.npmjs.com/package/gulp-rev-collector).
- More than [gulp-rev-collector](https://www.npmjs.com/package/gulp-rev-collector) of an new feature [concat-asserts-feature](https://github.com/shonny-ua/gulp-rev-collector/issues/44) .
- If the [PR](https://github.com/shonny-ua/gulp-rev-collector/pull/43) is merged in the future, The maintenance of this project will be abandoned!

[![NPM](https://nodei.co/npm/gulp-rev-collector-concat.png?downloads=true&stars=true)](https://nodei.co/npm/gulp-rev-collector-concat/)

> Static asset revision data collector from manifests, generated from different streams, and replace their links in html template.

## Install

```sh
$ npm install --save gulp-rev-collector-concat
```

## Usage

We can use [gulp-rev](https://github.com/sindresorhus/gulp-rev) to cache-bust several assets and generate manifest files for them. Then using gulp-rev-collector-concat we can collect data from several manifest files and replace links to assets in html templates.

```js
var gulp = require('gulp');
var rev = require('gulp-rev');

gulp.task('css', function () {
    return gulp.src('src/css/*.css')
        .pipe(rev())
        .pipe(gulp.dest('dist/css'))
        .pipe( rev.manifest() )
        .pipe( gulp.dest( 'rev/css' ) );
});

gulp.task('scripts', function () {
    return gulp.src('src/js/*.js')
        .pipe(rev())
        .pipe(gulp.dest('dist/js'))
        .pipe( rev.manifest() )
        .pipe( gulp.dest( 'rev/js' ) );
});

...

var revCollector = require('gulp-rev-collector-concat');
var minifyHTML   = require('gulp-minify-html');

gulp.task('rev', function () {
    return gulp.src(['rev/**/*.json', 'templates/**/*.html'])
        .pipe( revCollector({
            replaceReved: true,
            dirReplacements: {
                'css': '/dist/css',
                '/js/': '/dist/js/',
                'cdn/': function(manifest_value) {
                    return '//cdn' + (Math.floor(Math.random() * 9) + 1) + '.' + 'exsample.dot' + '/img/' + manifest_value;
                }
            }
        }) )
        .pipe( minifyHTML({
                empty:true,
                spare:true
            }) )
        .pipe( gulp.dest('dist') );
});
```

### Options

#### concatPrefixes

Type : `Object`

If you use some concat static path(for optimizing resource request reduction).

You may need convert ```/js/??/com/a.js,/com/b.js,/com/c.js``` to ```/js/??/com/a-[revcode].js,/com/b-[revcode].js,/com/c-[revcode].js```

Such as:
```
{
    "/js/??": "/js",
    "/css/??": "/css"
}
```

see concat-static-res-request information: [http-nginx-concat](https://github.com/alibaba/nginx-http-concat)

> The concat static asserts should be wrapped in quotes ('|").  "/js/??/com/a.js,/com/b.js,/com/c.js"

#### ignoreKeys

Type: `Array`

Set ignoreKeys to skip some special keys in mainfest during doing rev.

Sucn as: 
```
[
	'some_path_u_dont_want_rev.js',
	'some_path_u_dont_want_rev.css'
]
```

#### collectedManifest

Type : `String`

It is a filename for collecded and merged manifest data in `json` format.

#### replaceReved

Type : `Boolean`

You set a flag, replaceReved, which will replace alredy replaced links in template's files. Default value is `false`.

#### dirReplacements

Type : `Object`

Specifies a directories replacement set. [gulp-rev](https://github.com/sindresorhus/gulp-rev) creates manifest files without any info about directories. E.c. if you use dirReplacements param from [Usage](#usage) example, you get next replacement:

```
"/css/style.css" => "/dist/css/style-1d87bebe.css"
"/js/script1.js" => "/dist/script1-61e0be79.js"
"cdn/image.gif"  => "//cdn8.example.dot/img/image-35c3af8134.gif"
```

#### revSuffix

Type : `String`

It is pattern for define reved files suffixes. Default value is '-[0-9a-f]{8,10}-?'. This is necessary in case of e.c. [gulp-rename](https://github.com/hparra/gulp-rename) usage. If reved filenames had different from default mask.

#### extMap

Type : `Object`

If You use some methods to modify the rev_manifest.json after use [gulp-rev](https://github.com/sindresorhus/gulp-rev) and get a result like this:
```
{
    "assets/less/common.less": "assets/css/common-2c0d21e40c.css"
}
```
It means that keys and values in the rev_manifest.json have different ext : less & css.

You can define extentions maping rules for correct manifest file processing.

Default value is:
```
{
    '.scss': '.css',
    '.less': '.css',
    '.jsx': '.js'
}
```


### Works with gulp-rev-collector-concat

- [gulp-rev](https://github.com/sindresorhus/gulp-rev)

## License

[MIT](http://opensource.org/licenses/MIT)
