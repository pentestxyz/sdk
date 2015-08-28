# td-js-sdk
[![Build Status](https://travis-ci.org/treasure-data/td-js-sdk.svg?branch=master)](https://travis-ci.org/treasure-data/td-js-sdk) 
[![Sauce Test Status](https://saucelabs.com/buildstatus/dev-treasuredata)](https://saucelabs.com/u/dev-treasuredata)

## Installing

Read the [Browser Support and Polyfills](#browser-support-and-polyfills) section for information on supporting older browsers.

### Script snippet

Install td-js-sdk on your page by copying the appropriate JavaScript snippet below and pasting it into your page's `<head>` tag:

**Legacy** 

Use this snippet if you must support older browsers and are not already including json3 and es5-shim.

```html
<script type="text/javascript">
!function(t,e){if(void 0===e[t]){e[t]=function(){e[t].clients.push(this),this._init=[Array.prototype.slice.call(arguments)]},e[t].clients=[];for(var r=function(t){return function(){return this["_"+t]=this["_"+t]||[],this["_"+t].push(Array.prototype.slice.call(arguments)),this}},s=["addRecord","set","trackEvent","trackPageview","ready"],n=0;n<s.length;n++){var i=s[n];e[t].prototype[i]=r(i)}var a=document.createElement("script");a.type="text/javascript",a.async=!0,a.src=("https:"===document.location.protocol?"https:":"http:")+"//s3.amazonaws.com/td-cdn/sdk/td-1.3.0.legacy.js";var c=document.getElementsByTagName("script")[0];c.parentNode.insertBefore(a,c)}}("Treasure",this);
</script>
```

**Modern**

Use this snippet if you only support modern browsers or if you already include es5-shim and json3.

```html
<script type="text/javascript">
!function(t,e){if(void 0===e[t]){e[t]=function(){e[t].clients.push(this),this._init=[Array.prototype.slice.call(arguments)]},e[t].clients=[];for(var r=function(t){return function(){return this["_"+t]=this["_"+t]||[],this["_"+t].push(Array.prototype.slice.call(arguments)),this}},s=["addRecord","set","trackEvent","trackPageview","ready"],n=0;n<s.length;n++){var i=s[n];e[t].prototype[i]=r(i)}var a=document.createElement("script");a.type="text/javascript",a.async=!0,a.src=("https:"===document.location.protocol?"https:":"http:")+"//s3.amazonaws.com/td-cdn/sdk/td-1.3.0.js";var c=document.getElementsByTagName("script")[0];c.parentNode.insertBefore(a,c)}}("Treasure",this);
</script>
```

### bower

```sh
bower install --save td-js-sdk
```

This will download our library and include the following:

* `td-js-sdk/dist/td.js` - modern unminified build
* `td-js-sdk/dist/td.min.js` - modern minified build
* `td-js-sdk/dist/td.legacy.js` - legacy unminified build
* `td-js-sdk/dist/td.legacy.min.js` - legacy minified build

All builds export the `Treasure` global variable.

Files with **legacy** in the name include es5-shim and json3. Use these if you must support older browsers and are not already including them.

Files with **min** in the name are minified version of the library. Use these in production builds, otherwise you will have to minify the library yourself.

### npm

Does not work with NodeJS. **Browser only**.

```sh
npm install --save td-js-sdk
```

Exports Treasure class using CommonJS. The entry point is `lib/treasure.js`. Usable with a build tool such as Browserify or Webpack.

```javascript
var Treasure = require('td-js-sdk');
```

The CommonJS build does not include es5-shim or json3. You must include those manually if you need older browser support. 


## Getting started

### Get your write-only API key

Log in to [Treasure Data](https://console.treasuredata.com/) and go to your [profile](https://console.treasuredata.com/users/current). The API key should show up right next to your full-access key.

### Initializing

Our library works by creating an instance per database, and sending data into tables.

First install the library using any of the ways provided above.

After installing, initializing it is as simple as:

```javascript
  var foo = new Treasure({
    database: 'foo',
    writeKey: 'your_write_only_key'
  });
```

If you're an administrator, databases will automatically be created for you. Otherwise you'll need to ask an administrator to create the database and grant you `import only` or `full access` on it, otherwise you will be unable to send events.

### Sending your first event

```javascript
// Configure an instance for your database
var company = new Treasure({...});

// Create a data object with the properties you want to send
var sale = {
  itemId: 101,
  saleId: 10,
  userId: 1
};

// Send it to the 'sales' table
company.addRecord('sales', sale);
```

Send as many events as you like. Each event will fire off asynchronously.


## Tracking

td-js-sdk provides a way to track page impressions and events, as well as client information.

### Client ID and Storage

Each client requires a uuid. It may be set explicitly by setting `clientId` on the configuration object. Otherwise we search the cookies for a previously set uuid. If unable to find one, a uuid will be generated.

A cookie is set in order to track the client across sessions.

### Page impressions

Tracking page impressions is as easy as:

```javascript
/* insert javascript snippet */
var td = new Treasure({...});
td.trackPageview('pageviews');
```

This will send all the tracked information to the pageviews table.

### Event tracking

In addition to tracking pageviews, you can track events. The syntax is similar to `addRecord`, with the difference being that `trackEvent` will include all the tracked information.

```javascript
var td = new Treasure({});

var buttonEvent1 = function () {
  td.trackEvent('button', {
    number: 1
  });

  // doButtonEvent(1);
};

var buttonEvent2 = function () {
  td.trackEvent('button', {
    number: 2
  });

  // doButtonEvent(2);
};
```

### Tracked information

Every time a track functions is called, the following information is sent:

* **td_version** - td-js-sdk's version
* **td_client_id** - client's uuid
* **td_charset** - character set
* **td_language** - browser language
* **td_color** - screen color depth
* **td_screen** - screen resolution
* **td_viewport** - viewport size
* **td_title** - document title
* **td_url** - document url
* **td_host** - document host
* **td_path** - document pathname
* **td_referrer** - document referrer
* **td_ip** - request IP (server)
* **td_browser** - client browser (server)
* **td_browser_version** - client browser version (server)
* **td_os** - client operating system (server)
* **td_os_version** - client operating system version (server)

Certain values cannot be obtained from the browser. For these values, we send matching keys and values, and the server replaces the values upon receipt. For examples: `{"td_ip": "td_ip"}` is sent by the browser, and the server will update it to something like `{"td_ip": "1.2.3.4"}`

All server values except `td_ip` are found by parsing the user-agent string. This is done server-side to ensure that it can be kept up to date.


## Default values

Set default values on a table by using `Treasure#set`. Set default values on *all* tables by passing `$global` as the table name. 

Using `Treasure#get` you can view all global properties by passing the table name `$global`.

When a record is sent, an empty record object is created and properties are applied to it in the following order:

1. `$global` properties are applied to `record` object
2. Table properties are applied to `record` object, overwriting `$global` properties
3. Record properties passed to `addRecord` function are applied to `record` object, overwriting table properties

## Browser Support and Polyfills

In order to support older browsers while still writing forward-thinking JS, we use polyfills. This means we can write modern code and expect it to "just work", as compared to having a bunch of hacks littered all around the codebase. 

td-js-sdk requires the following polyfills in order to work on older browsers:

* [es5-shim](https://github.com/es-shims/es5-shim)
* [json3](http://bestiejs.github.io/json3/)

To make our user's lives easier, we provide a **legacy** build of td-js-sdk that bundles the required polyfills. This means that as a user you don't have to take any additional actions, simply select the legacy build from your preferred installation method.


## API

### Treasure(config)

Creates a new Treasure logger instance.
If the database does not exist and you have permissions, it will be created for you.

**Parameters:**

* **config** : Object (required) - instance configuration

**Core parameters:**

* **config.database** : String (required) - database name, must be between 3 and 255 characters and must consist only of lower case letters, numbers, and _
* **config.writeKey** : String (required) - write-only key, get it from your [user profile](console.treasuredata.com/users/current)
* **config.protocol** : String (optional) - protocol to use for sending events. Default: result of `document.location.protocol`
* **config.pathname** : String (optional) - path to append after host. Default: `/js/v3/event/`
* **config.host** : String (optional) - host to which events get sent. Default: `in.treasuredata.com`
* **config.development** : Boolean (optional) - triggers development mode which causes requests to be logged and not get sent. Default: `false`
* **config.logging** : Boolean (optional) - enable or disable logging. Default: `true`

**Track/Storage parameters:**

* **config.clientId** : String (optional) - uuid for this client. When undefined it will attempt fetching the value from a cookie if storage is enabled, if none is found it will generate a v4 uuid
* **config.storage** : Object | String (optional) - storage configuration object. When `none` it will disable cookie storage
* **config.storage.name** : String (optional) - cookie name. Default: `_td`
* **config.storage.expires** : Number (optional) - cookie expiration in seconds. When 0 it will expire with the session. Default: `63072000` (2 years)
* **config.storage.domain** : String (optional) - cookie domain. Default: result of `document.location.hostname`

**Returns:**

* Treasure logger instance object

**Example:**

```javascript
var foo = new Treasure({
  database: 'foo',
  writeKey: 'your_write_only_key'
});
```

### Treasure#addRecord(table, record, success, error)

Sends an event to Treasure Data. If the table does not exist it will be created for you.

Records will have additional properties applied to them if `$global` or table-specific attributes are configured using `Treasure#set`.

**Parameters:**

* **table** : String (required) - table name, must be between 3 and 255 characters and must consist only of lower case letters, numbers, and _
* **record** : Object (required) - Object that will be serialized to JSON and sent to the server
* **success** : Function (optional) - Callback for when sending the event is successful
* **error** : Function (optional) - Callback for when sending the event is unsuccessful

**Example:**

```javascript
var company = new Treasure({...});

var sale = {
  itemId: 100,
  saleId: 10,
  userId: 1
};

var successCallback = function () {
  // celebrate();
};

var errorCallback = function () {
  // cry();
}

company.addRecord('sales', sale, successCallback, errorCallback);
```

### Treasure#trackPageview(table, success, failure)

Helper function that calls trackEvent with an empty record.

**Parameters:**

* **table** : String (required) - table name, must be between 3 and 255 characters and must consist only of lower case letters, numbers, and _
* **success** : Function (optional) - Callback for when sending the event is successful
* **error** : Function (optional) - Callback for when sending the event is unsuccessful

**Example:**

```javascript
var td = new Treasure({...});
td.trackPageview('pageviews');
```

### Treasure#trackEvent(table, record, success, failure)

Creates an empty object, applies all tracked information values, and applies record values. Then it calls `addRecord` with the newly created object.

**Parameters:**

* **table** : String (required) - table name, must be between 3 and 255 characters and must consist only of lower case letters, numbers, and _
* **record** : Object (optional) - Additional key-value pairs that get sent with the tracked values. These values overwrite default tracking values
* **success** : Function (optional) - Callback for when sending the event is successful
* **error** : Function (optional) - Callback for when sending the event is unsuccessful

**Example:**

```javascript
var td = new Treasure({...});

td.trackEvent('events');
/* Sends:
{
  "td_ip": "192.168.0.1",
  ...
}
*/

td.trackEvent('events', {td_ip: '0.0.0.0'});
/* Sends:
{
  "td_ip": "0.0.0.0",
  ...
}
*/
```

### Treasure#set()

Default value setter for tables. Set default values for all tables by using `$global` as the setter's table name.

#### Treasure#set(table, key, value)

Useful when you want to set a single value.

**Parameters:**

* **table** : String (required) - table name
* **key** : String (required) - property name
* **value** : String | Number | Object (required) - property value

**Example:**

```javascript
var td = new Treasure({...})
td.set('table', 'foo', 'bar');
td.addRecord('table', {baz: 'qux'});
/* Sends:
{
  "foo": "bar",
  "baz": "qux"
}
*/
```

#### Treasure#set(table, properties)

Useful when you want to set multiple values.

**Parameters:**

* **table** : String (required) - table name
* **properties** : Object (required) - Object with keys and values that you wish applies on the table each time a record is sent

**Example:**

```javascript
var td = new Treasure({...})
td.set('table', {foo: 'foo', bar: 'bar'});
td.addRecord('table', {baz: 'baz'});
/* Sends:
{
  "foo": "foo",
  "bar": "bar",
  "baz": "baz"
}
*/
```


### Treasure#get(table)

Takes a table name and returns an object with its default values.

**NOTE:** This is only available once the library has loaded. Wrap any getter with a `Treasure#ready` callback to ensure the library is loaded.

**Parameters:**

* **table** : String (required) - table name

**Example:**

```javascript```
var td = new Treasure({..});
td.set('table', 'foo', 'bar');
td.get('table');
// {foo: 'bar'}
```


### Treasure#ready(fn)

Takes a callback which gets called one the library and DOM have both finished loading.

**Parameters:**

* **fn** : Function (required) - callback function

```javascript
/* javascript snippet here */
var td = new Treasure({...})
td.set('table', 'foo', 'bar');

td.ready(function(){
  td.get('table');
  // {foo: 'bar'}
});
```

## Support

Need a hand with something? Shoot us an email at [support@treasuredata.com](mailto:support@treasuredata.com)


## FAQ

* How does the async script snippet work?

The async script snippet will create a fake Treasure object on the window and inject the async script tag with the td-js-sdk url. This fake Treasure object includes a fake of all the public methods exposed by the real version. As you call different methods, they will be buffered in memory until the real td-js-sdk has loaded. Upon td-js-sdk loading, it will look for existing clients and process their buffered actions.

The unminified script loader can be seen in [src/loader.js](src/loader.js). The code to load existing clients and their buffered actions once td-js-sdk has been loaded can be seen in [lib/loadClients.js](lib/loadClients.js).


* Can I make my own Treasure wrapper and vendor it?

We've designed td-js-sdk in such a way that you can make a lightweight wrapper. You can see an example of this in [examples/wrapper](examples/wrapper). The main file of interest being [examples/wrapper/lib/index.js](examples/wrapper/lib/index.js).


## Other

### Dependency version notes

* `domready` is kept at `0.3.0` for IE6 and above support

