# octocat.js

[![npm version](https://badge.fury.io/js/octocat.svg)](http://badge.fury.io/js/octocat)
[![Build Status](https://travis-ci.org/SamyPesse/octocat.js.png?branch=master)](https://travis-ci.org/SamyPesse/octocat.js)

Javascript library to access the GitHub API.

- :sparkles: Promise based
- :sparkles: Support GitHub Enterprise
- :sparkles: Easy handling of paginated results

### Installation

```
$ npm install octocat
```

### Usage

#### Create an API client

```js
var GitHub = require('octocat');

// Using an access token
var client = new GitHub({
    token: "my-access-token"
});

// Using an username/password
var client = new GitHub({
    username: "SamyPesse",
    password: "my-password"
});

// Connecting to an enterprise version
var client = new GitHub({
    endpoint: "https:///github.mycompany.com"
});
```

#### Direct API access

All of these methods return promises.

```js
client.request('GET', '/repos/SamyPesse/octocat.js', { /* query/body parameters */})

client.get('/repos/SamyPesse/octocat.js', { /* query parameters */})
client.post('/repos/SamyPesse/octocat.js', { /* body parameters */})
client.patch('/repos/SamyPesse/octocat.js', { /* body parameters */})
client.put('/repos/SamyPesse/octocat.js', { /* body parameters */})
client.del('/repos/SamyPesse/octocat.js', { /* body parameters */})
```

These methods return a `response` object that looks like:

```js
{
    // HTTP Status code
    statusCode: 200,

    // Type of status: 2XX, 3XX, 4xx
    statusType: '2XX',

    // Headers returned by the server
    headers: { ... },

    // Body of the request
    body: { ... }
}
```

In case of error, the response can also be accessible using as a `response` property.

#### Pagination

Some methods of Octonode return paginated results. Check on GitHub which API methods are paginated.

These methods return a `Page` object:

- `page.list` contains the list of results in the current page
- `page.next()` update with the results of the next page (Promised)
- `page.prev()` update with the results of the previous page (Promised)
- `page.hasNext()` and `page.hasPrev()`

#### Users

```js
// Get a single user
var user = client.user('SamyPesse');
user.info().then(function(infos) { ... });

// Get the authenticated user
var user = client.me();

// Edit the authenticated user
user.edit({
    name: "New Name"
})
```

#### Repositories

```js
// Get public repositories (paginated)
client.repos().then(function(page) { ... });

// List user repositories
user.repos().then(function(page) { ... });

// Get a single repository
var repo = client.repo('SamyPesse/octocat.js');
repo.info().then(function(infos) { ... });

// Delete the repository
repo.destroy()
```

#### Issues

```js
// List issues for a repository
repo.issues().then(function(page) { ... });

// Create an issue in a repository
repo.createIssue({
    title: "An awesome issue"
});

// Get a single issue in a repository
var issue = repo.issue(200);
issue.info().then(function(infos) { ... });

// Edit an issue
issue.edit({
    title: "New Title"
})
```

#### Organization

```js
// List organizations of an user
user.orgs().then(function(orgs) { ... });

// Get a single organization
var org = client.org('GitbookIO');
org.info().then(function(infos) { ... });

// Edit the organization
org.edit({
    name: "The new name"
})
```

#### Commits

```js
// List commits for a repository
repo.commits().then(function(commits) { ... });

// Get a single commit
var commit = repo.commit('6dcb09b5b57875f334f61aebed695e2e4193db5e');
commit.info().then(function(infos) { ... });
```

#### Statuses

```js
// Create a Status
repo.createStatus('sha', { ... }).then(function() { ... });

// List Statuses for a specific Ref
commit.statuses().then(function(statuses) { ... });
```

#### Webhooks

```js
// List hooks for a repository
repo.hooks().then(function(hooks) { ... });

// Get a single hook
var hook = repo.hook('1');
hook.info().then(function(infos) { ... });

// Edit a hook
hook.edit({
    name: "Test"
})

// Delete the hook
hook.destroy()
```

#### Releases

```js
// List releases of a repository
repo.releases().then(function(assets) { ... });

// Get details about the release
var release = repo.release('1');
release.info().then(function(infos) { ... });

// Edit a release
release.edit({
    name: "Test"
})

// Delete the release
release.destroy()
```

#### Uploading assets

```js
// Upload a new asset as file/stream
release.upload('./myfile.zip').then(function() { ... });
release.upload(stream, { name: "myfile.zip" }).then(function() { ... });
```

`release.upload` will also notify with progress:

```js
release.upload('./myfile.zip')
.progress(function(p) {
    /*
    { percentage: 96.61881065572815,
      transferred: 45088768,
      length: 46666656,
      remaining: 1577888,
      eta: 0,
      runtime: 11,
      delta: 196608,
      speed: 3920762.434782609 }
    */
})
.then(function() {

});

```

#### Releases Assets

```js
// List assets of a release
release.assets().then(function(assets) { ... });

// Get details about the release
var asset = release.asset('1');
asset.info().then(function(infos) { ... });

// Download the asset to a file
asset.download('./myfile.zip').then(function() { ... });

// Download the asset to a stream
asset.download(stream).then(function() { ... });

// Delete the asset
asset.destroy()
```

#### Emails

```js
// List email addresses
client.userEmails().then(function(emails) { ... });

// Add email address(es)
client.addUserEmails([ 'octocat@github.com' ]).then(function() { ... });

// Delete email address(es)
client.deleteUserEmails([ 'octocat@github.com' ]).then(function() { ... });
```

#### Authorization for a specific app

```js
var app = client.application('clientId');
var token = app.token('access_token');

// Check an authorization
token.info().then(function(infos) { ... });

// Reset an authorization
token.reset().then(function() { ... });

// Delete an authorization
token.destroy().then(function() { ... });
```

#### Rate Limiting

You can also check your rate limit status by calling the following.

```js
client.limit().then(function(rate) { ... })
```

