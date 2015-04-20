# Patreon API
Patreon uses an API of sorts for a lot of actions on their website; it's just not documented. I'm doing some digging and am going to try my best to provide some documentation for it. I am not at all affiliated with Patreon and provide no warranty that this information will work in the future or even now. I'm just publishing it in the hope that it will be useful and will help people extend the Patreon platform in awesome ways.

## Making requests

All requests are based on the domain https://api.patreon.com. Requests can be GET or POST (usually POST). POST requests generally take JSON data and require the header "Content-type: application/json" to avoid a 500 error.

All requests and responses take this general form:
```
{
  "data" : {
    //some data...
    "type": "<what kind of object this is>" // known data types are documented below
  }
}
```
The fields contained within the "data" object vary by request, but generally it is an object representation of the thing requested by this method.

### Linked objects in responses

If your request demanded that additional objects be included, the API will link them in the response, and use this more complex format (i.e. if you do /current_user?include=cards, the user's data will be in the data array, and the credit card data will be in linked):

```
{
  "data" : {
    "links": {

      "TYPE OF OBJECT": {
        "ids": [
          // string ids of all linked objects
        ],
        type: "TYPE OF OBJECT"
      },

      // etc...

    },

    //some data...

  },
  "linked": [] // array of linked objects corresponding to the ids in "links"
}
```

## Methods

### /login (POST)

Method for iniating a user session. Also retrieves a lot of information about the logged in user.

#### Body data

* email: user's patreon login email
* password: user's patreon login password

#### Response
data: user object

In addition to returning a JSON object with information about the user, the server also responds with a Set-Cookie header that sets a cookie named "session_id" to a unique session ID on the domain "patreon.com". This cookie appears to expire a month later by default.


### /user (POST)
Seems to be an authentication thing for Facebook?

#### Body data

* fb\_access\_token: presumably an access token for Facebook's API?
* email: the email associated with the user's Facebook account?

####  Response
unknown


### /current_user (GET)

Retrieves information about the currently logged-in user. Appending "?include=cards" to the URL causes credit card information to be included as well.

#### Response
data: user object

If "?include=cards", card objects (see below) will be linked.


## Data types

These are the types of objects that the API may include in its responses, either as the main "data" object, or as an object in the "linked" array.

### User

A user object contains information about a Patreon user, usually the currently logged-in user.

```
{
  "about": "", // perhaps a tagline or something?
  "created": "<timestamp; format is 1970-12-31T23:59:59>",
  "email": "<user email>",
  "first_name": "<first name>",
  "full_name": "<first and last name>",
  "gender": <number>, // male is 1, presumably 2 is female?
  "id": "<user id>", // is always numeric afaik, but appears as a string nonetheless
  "image_url": "<url to 200x200 jpg>",
  "last_name": "<last name>",
  "status": 1, // not sure what this means
  "thumb_url": "<url to 50x50 jpg>",
  "type": "user",
  "url": "<clean url to user profile>",
  "vanity": "<custom url username, if set>""
}
```

### Card

Represents a credit card or other payment method (i.e. PayPal)

```
{
  "card_type": "<name of payment method>", // known possibilities: "PayPal", "Visa"
  "expiration_date": "<expiration date of card in YYYY-MM-DD format>", // will be null for PayPal
  "id": "<unique identifier>",
  "links": {
    "user": {
      "id": "<user id>",
      "type": "user"
    }
  },
  "number": <last four digits of credit card>, // will be 0 for PayPal
  "type": "card"
}
```