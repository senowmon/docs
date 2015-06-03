# Login

## Retrieve API Key

> integrationKey and password values have been redacted. In an actual request, substitute your own key and the user's password

```shell
curl -X POST -d "integrationKey=XXX" -d "email=fishbowl@wufoo.com" -d "password=XXX" -d "subdomain=fishbowl" -u "AOI6-LFKL-VM1Q-IEX9":"footastic" "https://wufoo.com/api/v3/login.json"
```

```python
import urllib
import urllib2
import json

base_url = 'https://fishbowl.wufoo.com/api/v3/'
username = 'AOI6-LFKL-VM1Q-IEX9'
password = 'footastic'

password_manager = urllib2.HTTPPasswordMgrWithDefaultRealm()
password_manager.add_password(None, base_url, username, password)
handler = urllib2.HTTPBasicAuthHandler(password_manager)
opener = urllib2.build_opener(handler)

urllib2.install_opener(opener)

values = {
        'integrationKey' : 'XXX', 
        'email' : 'fishbowl@wufoo.com',
        'password' : 'XXX',
        'subdomain': 'fishbowl'
}

data = urllib.urlencode(values)

response = urllib2.urlopen(base_url+'login.json', data)
data = json.load(response)
print json.dumps(data, indent=4, sort_keys=True)
```

```ruby
require "net/http"
require "uri"
require "json"

base_url = 'https://fishbowl.wufoo.com/api/v3/'
username = 'AOI6-LFKL-VM1Q-IEX9'
password = 'footastic'

uri = URI.parse(base_url+"forms/s1afea8b1vk0jf7/entries.json")

request = Net::HTTP::Post.new(uri.request_uri)
request.basic_auth(username, password)

request.set_form_data('integrationKey' => 'XXX',
                      'email' => 'fishbowl@wufoo.com',
                      'password' => 'XXX',
                      'subdomain' => 'fishbowl'
                      )

response = Net::HTTP.start(uri.hostname, uri.port, :use_ssl => uri.scheme == 'https') {|http|
  http.request(request)
}

puts JSON.pretty_generate(JSON[response.body])
```

> The above request will recieve a response in this format:

```json
{
  "ApiKey":"AOI6-LFKL-VM1Q-IEX9",
  "UserLink":"https://fishbowl.wufoo.com/api/v3/users/b1fe5l920lqsh58.json",
  "Subdomain":"fishbowl"
}
```

### HTTP Request

`POST http://wufoo.com/api/v3/login.{format}`

### URL Parameters

Parameter | Description
--------- | -----------
format    | Either 'json' or 'xml' is required. This will determine response format

### POST Parameters

Parameter      | Description
-------------- | -----------
integrationKey | Required. This is your Login integration key. You can apply for one [here](https://master.wufoo.com/forms/integration-key-application/)
email          | Required. The user’s email, which acts as the identifier for their account.
password       | Required. The user's password
subdomain      | Optional. The user's subdomain. Is required if the email belongs to a sub-user or the email address is used on multiple accounts.
<aside class="warning">If you submit a request without the subdomain, but there's a conflict, the request will return a 409 error</aside>