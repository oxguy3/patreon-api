# Patreon API
Patreon uses an API of sorts for a lot of actions on their website; it's just not documented. I'm doing some digging and am going to try my best to provide some documentation for it. I am not at all affiliated with Patreon and provide no warranty that this information will work in the future or even now. I'm just publishing it in the hope that it will be useful and will help people extend the Patreon platform in awesome ways.

All requests are based on the domain https://api.patreon.com. Requests can be GET or POST (usually POST). POST requests generally take JSON data and require the header "Content-type: application/json" to avoid a 500 error.

## Methods

### /login (POST)

Method for iniating a user session. Also retrieves a lot of information about the logged in user. In addition to returning a JSON object with information about the user, the server also responds with a Set-Cookie header that sets a cookie named "session_id" to a unique session ID on the domain "patreon.com". This cookie appears to expire a month later by default.

#### Body
```
{
  data: {
    "email": "<user email>",
    "password": "<user password>"
  }
}
```
#### Response
```
{
    "data": {
        "about": "", // perhaps a tagline or something?
        "created": "<timestamp; format is 1970-12-31T23:59:59>",
        "email": "<user email>",
        "first_name": "<first name>",
        "full_name": "<first and last name>",
        "gender": <number>, // male is 1, presumably 2 is female?
        "id": "<user id>",
        "image_url": "<url to 200x200 jpg>",
        "last_name": "<last name>",
        "status": 1, // not sure what this means
        "thumb_url": "<url to 50x50 jpg>",
        "type": "user",
        "url": "<clean url to user profile>",
        "vanity": "<custom url username, if set>""
    }
}
```