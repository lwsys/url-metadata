# url-metadata

Request an http(s) url and scrape its metadata. Many of the metadata fields returned are [Open Graph Protocol (og:)](http://ogp.me/) so far.

Support also added for [JSON-LD](https://moz.com/blog/json-ld-for-beginners).

Under the hood, this package does some post-request processing on top of the [request](https://www.npmjs.com/package/request) module.

If you want a new feature, please open an issue or pull request in [GitHub](https://github.com/laurengarcia/url-metadata).


## Usage

To use in an npm/ Node.js project, install from your CLI:
```
$ npm install url-metadata --save`
```

Then in your project file:
```javascript
const urlMetadata = require('url-metadata')
try {
  const metadata = await urlMetadata(url)
  // do stuff with metadata
} catch(err) {
  console.log(err)
}
```

To override the default options (see below), pass in a second argument:
```javascript
const urlMetadata = require('urlMetadata')
const metadata = urlMetadata('http://bit.ly/2ePIrDy', { fromEmail: 'me@myexample.com' })
```

### Options & Defaults
This module's default options are the values below that you can override:
```javascript
{
  // custom name for the user agent and email that will make url request:
  userAgent: 'url-metadata/3.0 (npm module)',
  fromEmail: 'example@example.com',

  // module will follow a maximum of 10 redirects
  maxRedirects: 10,

  // timeout in milliseconds, default below is 10 seconds:
  timeout: 10000,

  // number of characters to truncate description to:
  descriptionLength: 750,

  // force image urls in selected tags to use https,
  // valid for 'image', 'og:image' and 'og:image:secure_url' tags:
  ensureSecureImageRequest: true,

  // custom function to decode special-case encodings;
  // defaults to undefined:
  decode: undefined,

  // custom function to encode the metadata fields before they are returned;
  // defaults to undefined:
  encode: undefined
}
```

#### Option: Decode
Supply a custom function to decode the metadata scraped from the url. Example decoding of EUC-JP (Japanese) can be found in `test/decode-EUC-JP.test.js`.

This module is not opinionated about what you do in the decode() function. It simply receives a buffer as argument and must return a string.

#### Option: Encode
Supply a custom function to encode the metadata fields before they are returned from this module, see `test/encode.test.js`:
```javascript
const options = {
  encode: function (value) {
    return encodeURIComponent(value).replace(/['*]/g, escape)
  }
}
```

### Returns
Returns a promise that gets resolved with the following metadata if the request response returns successfully. Note that the `url` field returned below will be the last hop in the request chain. So if you passed in a url that was generated by a link shortener, for example, you'll get back the final destination of the link as the `url`.
```javascript
{
    'url': '',
    'canonical': '',
    'title': '',
    'image': '',
    'author': '',
    'description': '',
    'keywords': '',
    'price': '',
    'priceCurrency': '',
    'availability': '',
    'robots': '',
    'og:url': '',
    'og:locale': '',
    'og:locale:alternate': '',
    'og:title': '',
    'og:type': '',
    'og:description': '',
    'og:determiner': '',
    'og:site_name': '',
    'og:image': '',
    'og:image:secure_url': '',
    'og:image:type': '',
    'og:image:width': '',
    'og:image:height': '',
    'twitter:title': '',
    'twitter:image': '',
    'twitter:image:alt': '',
    'twitter:card': '',
    'twitter:site': '',
    'twitter:site:id': '',
    'twitter:account_id': '',
    'twitter:creator': '',
    'twitter:creator:id': '',
    'twitter:player': '',
    'twitter:player:width': '',
    'twitter:player:height': '',
    'twitter:player:stream': '',
    'jsonld': {}
}
```

Additional fields are also returned if the url has an `og:type` set to `article`. These fields are:
```javascript
{
  'article:published_time'     : '',
  'article:modified_time'      : '',
  'article:expiration_time'    : '',
  'article:author'             : '',
  'article:section'            : '',
  'article:tag'                : '',
  'og:article:published_time'  : '',
  'og:article:modified_time'   : '',
  'og:article:expiration_time' : '',
  'og:article:author'          : '',
  'og:article:section'         : '',
  'og:article:tag'             : ''
}
```
