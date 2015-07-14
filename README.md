node-soundcloud [![npm version](https://badge.fury.io/js/node-soundcloud.svg)](http://badge.fury.io/js/node-soundcloud)
=======================================================================================================================

node.js wrapper for the Soundcloud SDK. Inspired by the libraries [soundcloud-node](https://github.com/maruf89/soundcloud-node) and [soundclouder.js](https://github.com/khilnani/soundclouder.js).

---

### Getting started

1. `npm install --save node-soundcloud`
2. `var SC = require('node-soundcloud')`

---

### Getting an OAuth Token

1. Require `node-soundcloud` and initialize with your parameters
2. Redirect user to the necessary SoundCloud Connect URL
3. User will then be redirected to your `redirect_uri`, which can be handled by an Express endpoint (discussed in the next section).

```javascript
var SC = require('node-soundcloud');

// Initialize client
SC.init({
  id: 'your SoundCloud client ID',
  secret: 'your SoundCloud client secret',
  uri: 'your SoundCloud redirect URI'
});

// Connect user to authorize application
var initOAuth = function(req, res) {
  var url = SC.getConnectUrl();

  res.writeHead(301, Location: url);
  res.end();
};

// Get OAuth token (example endpoint discussed in the next section)
```

---

### Example Redirect URI Endpoint

After your user is redirected to the Connect URL, SoundCloud will then redirect them back to the URL you have specified as your `redirect_uri`. There will also be the parameter `code` in the query string, and this is used to retrieve your OAuth token. An endpoint to handle this could work as follows:

```javascript
var redirectHandler = function(req, res) {
  var code = req.query.code;

  SC.authorize(code, function(err, accessToken) {
    if ( err ) {
      throw err;
    } else {
      // Client is now authorized and able to make API calls
      console.log('access token:', accessToken);
    }
  });
};
```

---

### Initializing with an OAuth Token

If you have already acquired an OAuth access token, you can initialize the client without going through the authorization process by passing it as an extra option:

```javascript
var SC = require('node-soundcloud');

// Initialize client with additional accessToken field
SC.init({
  id: 'your SoundCloud client ID',
  secret: 'your SoundCloud client secret',
  uri: 'your SoundCloud redirect URI',
  accessToken: 'your existing access token'
});
```

---

### Making calls to the SoundCloud API

Once authorized (or if you're accessing unprotected endpoints), you may now make calls to the SoundCloud API [documented here](https://developers.soundcloud.com/docs/api/reference). An example could be as follows:

```javascript
SC.get('/tracks/164497989', function(err, track) {
  if ( err ) {
    throw err;
  } else {
    console.log('track retrieved:', track);
  }
});
```
