---
title: Wufoo API v3

language_tabs:
  - shell: cURL
  - php: PHP
  - python: Python
  - ruby: Ruby
  - javascript: Node.js

toc_footers:
  - <a href='http://www.wufoo.com'>Wufoo</a>
  - <a href='http://github.com/tripit/slate'>Powered by Slate</a>

includes:
  - forms
  - entries
  - reports
  - users
  - webhooks
  - login
  - errors
  - tool

search: true
---

# Introduction

Welcome to the Wufoo API! You can use our API to access your [forms](http://help.wufoo.com/articles/en_US/kb/Form-Builder?bc=Creating_Forms), [entries](http://help.wufoo.com/articles/en_US/kb/Entry-Manager), [reports](http://help.wufoo.com/articles/en_US/kb/Report-Manager?bc=Creating_Reports), submit new entries, and add or remove Webhooks.

## Structure

>Our API can be used by making requests with any HTTP service/client/library. To help you get started with building your own integrations, we'll include basic code samples in this area. You can switch the language of the examples with the tabs at the top.

Each resource page will go into more detail, but here's a quick overview of how the Wufoo API is structured

When making a request, you'll use your specific account subdomain. For example: `https://fishbowl.wufoo.com`

All of our API resources then start with: `/api/v3`

To help keep things organized, we've broken up our API into logical sections based on the different aspects of Wufoo. These are:  

Name     | Endpoint    | HTTP Methods
-------- | ----------- | -----------
Forms    | `/forms`    | GET
Entries  | `/entries`  | GET or POST
Reports  | `/reports`  | GET
Users    | `/users`    | GET
Webhooks | `/webhooks` | PUT or DELETE
Login    | `/login`    | POST

Some requests require you to identify the specific resource you're trying to access. The most commonly used example would be a form identifier. To uniquely identify a specific form, you can use either
the "hashed" URL: `/forms/s1afea8b1vk0jf7/`
or the form title: `/forms/wufoo-api-example/`

Forms and Reports resources have a  "hashed" and a "title" identifier. Widgets, Users, and WebHooks resources only have hash identifiers.

Finally, each request will end with a format extension. Our API can return responses in either `.json` or `.xml` format.

A complete request will look like this: `https://fishbowl.wufoo.com/api/v3/forms/s1afea8b1vk0jf7.json`

Optional query parameters can also be added to the end of the request like so:
`https://fishbowl.wufoo.com/api/v3/forms/s1afea8b1vk0jf7.json?pretty=true&includeTodayCount=true`

## API Key

>All examples use the demo 'fishbowl' subdomain. When making your own requests, be sure to use your own account name/subdomain, and your own API Key.

To use any of the API functions, you'll need to use your Wufoo API Key. If you haven't already, you can follow these steps to locate your key:

 1. Log in to your Wufoo account
 1. From the Form Manager, select **API Information** from the **More** dropdown on any form. 
 1. On that page there is a 16 digit code, which is your unique API key. 

## Form Hashes and Field Ids

Many API calls also involve using a Form's hash or the API ID for a form's field. To locate this information:

 1. Log in to your Wufoo account
 1. From the Form Manager, select **API Information** from the **More** dropdown on the form you want to make calls against.
 1. In the table below your API Key you will find an API ID for each field and your form's Hash. 

<aside class="notice">
Each sub-user on your account has their own API Key. This allows you to still enforce <a href='http://help.wufoo.com/articles/en_US/SurveyMonkeyArticleType/User-Management'>User Permissions</a>, even with the API
</aside>

## Restrictions

We currently restrict API usage per key, per day. Your API usage is dependent on your plan. Check out how many requests you have per day on our <a href='https://www.wufoo.com/pricing/'>pricing page</a>.

For security, API request can only be made using HTTPS. Our servers will block request made using SSLv3 or lower, so be sure to force TLSv1 or higher if your default SSL/TLS version is different.

# Authentication

> Here is a sample request, showing the process for authenticating using your API Key:

```shell
# With cURL, you can just pass the "username" and "password" with each request
curl -u "AOI6-LFKL-VM1Q-IEX9":"footastic" "https://fishbowl.wufoo.com/api/v3/forms.json"
```

```php
<?php
$curl = curl_init('https://fishbowl.wufoo.com/api/v3/forms.json');
curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($curl, CURLOPT_USERPWD, 'AOI6-LFKL-VM1Q-IEX9:footastic');
curl_setopt($curl, CURLOPT_HTTPAUTH, CURLAUTH_ANY);
curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($curl, CURLOPT_FOLLOWLOCATION, true);
curl_setopt($curl, CURLOPT_USERAGENT, 'Wufoo Sample Code');

$response = curl_exec($curl);
$resultStatus = curl_getinfo($curl);

if($resultStatus['http_code'] == 200) {
    echo htmlentities($response);
} else {
    echo 'Call Failed '.print_r($resultStatus);
}
```

```python
# Using urllib2 we'll need an HTTPPasswordMgr and HTTPBasicAuthHandler
import urllib2

base_url = 'https://fishbowl.wufoo.com/api/v3/'
username = 'AOI6-LFKL-VM1Q-IEX9'
password = 'footastic'

# Create a password manager
password_manager = urllib2.HTTPPasswordMgrWithDefaultRealm()

# Add the username and password (API key and nonsense value)
password_manager.add_password(None, base_url, username, password)

# Create the AuthHandler
handler = urllib2.HTTPBasicAuthHandler(password_manager)

# Create and install an opener using the AuthHandler
opener = urllib2.build_opener(handler)
urllib2.install_opener(opener)

# Now each request we make will be authenticated
response = urllib2.urlopen(base_url+'forms.json')
```

```ruby
require "net/http"
require "uri"
require "json"

base_url = 'https://fishbowl.wufoo.com/api/v3/'
username = 'AOI6-LFKL-VM1Q-IEX9'
password = 'footastic'

uri = URI.parse(base_url+"forms.json")

# Set up our request using the desired endpoint, and configure the basic auth
request = Net::HTTP::Get.new(uri.request_uri)
request.basic_auth(username, password)

# Make our request using https
response = Net::HTTP.start(uri.hostname, uri.port, :use_ssl => uri.scheme == 'https') {|http|
  http.request(request)
}

# Print the output in a "pretty" json format
puts JSON.pretty_generate(JSON[response.body])
```

```javascript
//With Node, we'll use the `request` library. If you don't have it already, you can get it with `npm install request`
var request = require("request");

request({
  uri: "https://fishbowl.wufoo.com/api/v3/forms.json",
  method: "GET",
  auth: {
    'username': 'AOI6-LFKL-VM1Q-IEX9',
    'password': 'footastic',
    'sendImmediately': false
  }
}, function(error, response, body) {
  console.log(body);
});
```

> Make sure to replace `AOI6-LFKL-VM1Q-IEX9` with your API key and `fishbowl` with your own subdomain. The second value `footastic` can be set to anything (we don't check the content).

Wufoo uses [Basic Auth](http://www.ietf.org/rfc/rfc2617.txt) with API Keys to allow access to the API.

Wufoo expects the API key to be included as the 'username' and any value as the 'password' portion of Basic Auth. If the service you're using doesn't have a built in way to authenticate using Basic Auth, you can set the Authorization header to a base64 encoded string:

`base64('username:password')`

After encoding, you can set the header like this:

`Authorization: Basic QU9JNi1MRktMLVZNMVEtSUVYOTpmb290YXN0aWM=`

<aside class="notice">
You'll need to replace 'username' with your API key, and password with any value (our examples use 'footastic')
</aside>
