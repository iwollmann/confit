# confit

Simple, environment-based configuration. `confit` loads a default JSON
configuration file, additionally loading environment-specific files, if applicable.
It will also process the loaded files using any configured
[shortstop](https://github.com/paypal/shortstop) protocol handlers.
(See **Options** below.)

[![Build Status](https://travis-ci.org/totherik/confit.png)](https://travis-ci.org/totherik/confit)

## Usage
```javascript
var confit = require('confit');
```

### confit(options, callback);
* `options` (*String* | *Object*) - the base directory in which config files live
or a configuration object.
* `callback` (*Function*) - the callback to be called with the error or config object.
Signature `function (err, config) { /* ... */}`

```javascript
'use strict';

var path = require('path');
var confit = require('confit');

var basedir = path.join(__dirname, 'config');
confit(basedir, function (err, config) {
    config.get; // Function
    config.set; // Function
    config.use; // Function

    config.get('env:env'); // 'development'
});
```

## Options
* `basedir` (*String*) - the base directory in which config files can be found.
* `protocols` (*Object*) - An object containing a mapping of
[shortstop](https://github.com/paypal/shortstop) protocols to handler implementations.
This protocols will be used to process the config data prior to registration.
* `defaults` (*String*) - the name of the file containing all default values.
Defaults to `config.json`.

```javascript
'use strict';

var path = require('path');
var confit = require('confit');
var handlers = require('shortstop-handlers');


var options = {
    basedir: path.join(__dirname, 'config');
    handlers: {
        file: handlers.file,
        glob: handlers.glob
    }
};

confit(options, function (err, config) {
    config.get; // Function
    config.set; // Function
    config.use; // Function

    config.get('env:env'); // 'development'
});
```


## API
* `get(key)` - Retrieve the value for a given key.
* `set(key, value)` - Set a value for the given key.
* `use(obj, callback)`- Processed object with shortstop and loads into config.

```javascript
config.set('foo', 'bar');
config.get('foo'); // 'bar'

config.use({ foo: 'baz' }, function (err) {
    config.get('foo'); // 'baz'
});

```

## Default Behavior
By default, `confit` loads `process.env` and `argv` values upon initialization. Additionally,
it creates convenience environment properties prefixed with `env:` based on the
current `NODE_ENV` setting, defaulting to `development`. It also normalizes
`NODE_ENV` settings to the long form, so `dev` becomes `development`, `prod`
becomes `production`, etc.
```javascript
// NODE_ENV='dev'
config.get('NODE_ENV');        // 'dev'
config.get('env:env');         // 'development'
config.get('env:development'); // true
config.get('env:test');        // false
config.get('env:staging');     // false
config.get('env:production');  // false
```

```javascript
// NODE_ENV='custom'
config.get('NODE_ENV');        // 'custom'
config.get('env:env');         // 'custom'
config.get('env:development'); // false
config.get('env:test');        // false
config.get('env:staging');     // false
config.get('env:production');  // false
config.get('env:custom');      // true
```
