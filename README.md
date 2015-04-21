# Patreon API
Patreon is developing an API of sorts. It's currently not used for a lot of actions on their website, other than for a few cleaned up features. It's also not documented yet. I'm doing some digging and am going to try my best to provide some documentation for it. I am not at all affiliated with Patreon and provide no warranty that this information will work in the future or even now. I'm just publishing it in the hope that it will be useful and will help people extend the Patreon platform in awesome ways.

## Notes

Some important information to look at before diving into this API.

### Making requests

Patreon is making use of the [JSON API spec](http://jsonapi.org) so some implementation details can be found by plumbing that documentation.

### API keys

Some requests require that you have an API key when you make them. This is done by appending "?api_key=YOUR API KEY" to the end of the URL. At present, it looks like there's only one API key shared by everyone: 1745177328c8a1d48100a9b14a1d38c1. Methods that require an api key are marked as such.


### Timestamps

Unless otherwise indicated, assume all timestamps are in this format: 1970-12-31T23:59:59



## Methods


### /login (POST)

Method for iniating a user session. Also retrieves a lot of information about the logged in user.

**Body data**

* email: user's patreon login email
* password: user's patreon login password

**Response**

data: user object

In addition to returning a JSON object with information about the user, the server also responds with a Set-Cookie header that sets a cookie named "session_id" to a unique session ID on the domain "patreon.com". This cookie appears to expire a month later by default.


### /user (POST)

Creates a user, currently used for FB registrations

**Body data**

* fb\_access\_token: presumably an access token for Facebook's API?
* email: the email associated with the user's Facebook account?

**Response**

User object


### /user/:userid (GET)

Retrieves data about a given user **[requires api key]**

**Response**

data: user object


### /current_user (GET)

Retrieves information about the currently logged-in user. Appending "?include=cards" to the URL causes credit card information to be included as well.

**Response**

data: user object

If "?include=cards", card objects (see below) will be linked.



### /notifications

Unfinished endpoint for an unreleased feature

**Response**

data: currently an empty array



### /campaign (GET)

Retrieves a list of campaigns. Only seems to work if "?filter=subbable" is appended to the URL

**Response**

data: array of campaign objects



### /campaign/current_campaign (GET)

Unfinished endpoint for an unreleased feature **[requires api key]**



### /campaign/:campaignid (GET)

Retrieves information about a particular campaign **[requires api key]**

**Response**

data: array of all category objects
included: array of all reward objects and the user object for the creator of this campaign



### /campaign/:campaignid/drafts (GET)

Unfinished endpoint for an unreleased feature **[requires api key]**



### /categories (GET)

Gets a list of all the categories on the site (this is part of an unreleased feature) **[requires api key]**

**Response**

data: array of all category objects



### /post (GET)

Creates a post??? Or maybe creates an ID for a post, which can then be modified with another call??? **[requires api key]**


### /post/:postid (GET)

Unfinished endpoint for an unreleased feature (but seems to be relatively functional at present).

Retrieves a given post by its numeric ID **[requires api key]**

**Response**

data: a post object


### /post/:postid (POST)

Unfinished endpoint for an unreleased feature

Modifies an existing post **[requires api key]**


### /post/:postid (DELETE)

Unfinished endpoint for an unreleased feature

Deletes an existing post **[requires api key]**


### /post/:postid/undelete (GET?)

Unfinished endpoint for an unreleased feature

Undeletes a previously-deleted post **[requires api key]**


### /post/:postid/attachments (GET)

Unfinished endpoint for an unreleased feature

Retrieves the attachments for a given post **[requires api key]**

**Response**

data: an array, presumably of attachments?


### /post/:postid/attachments (POST)

Unfinished endpoint for an unreleased feature

Uploads an attachment **[requires api key]**


### /post/:postid/attachments (DELETE)

Unfinished endpoint for an unreleased feature

Deletes an existing attachment **[requires api key]**





## Data types

These are the types of objects that may be included in requests or responses, either as the main "data" object, or as an object in the "linked" array.

### Campaign

Represents a campaign (i.e. a content creator's patreon page, essentially). Often, the 'included' array will include all the reward objects for this campaign and a user object for the creator of this campaign.

```
{
  "about": "<description of campaign>", // can contain HTML markup
  "created_at": "<timestamp>"
  "creation_name": "<name of campaign>",
  "creator": <number>, // seems to be a Subbable-only thing??
  "creator_name": "<name of creator>", //seems to be a Subbable-only thing??
  "id": <number>, // campaign ID number
  "image_small_url": "<url to 320x320 image>",
  "image_url": "<url to 714x402 image>",
  "is_monthly": <boolean>, // does this campaign charge per-month or per-creation?
  "is_nsfw": <boolean>,
  "links": {
    "rewards": {
      "linkage": [
        // array of reward objects
      ]
    }
  }
  "main_video_embed": "<html>", // an embed tag for the campaign's main video
  "main_video_url": "<url>", // an url to the campaign's main video
  "one_liner": "<short description of campaign>",
  "patron_count": <number>,
  "pay_per_name": "<pay per what?>" // what are supporters being charged for? (e.g. "month", "video", etc.)
  "pledge_sum": <number>, // pledged amount in cents
  "published_at": "<timestamp>",
  "summary": "<html markup>",
  "thanks_embed": "<html>", // embed tag for patron thanks video (null if no video),
  "thanks_msg": "<text>", // message shown to thank patrons
  "thanks_video_url": "<url>", // url for patron thanks video (null if no video),
  "type": "campaign",
  "url": "<url to patreon campaign>" // generally https://www.patreon.com/<username>
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

### Category

Represents a category of campaign (e.g. Music, Comics, etc)

```
{
  "id": <numeric id>, // at time of writing, categories are ids 0-14, except the "All" category, which has id 99
  "name": <display name for category>,
  "type": "category"
}
```


### Comment

Represents a comment on a post

```
{
  "type": "<comment|like|success|other>",
  "body": "<body of the comment>",
  "links": {
    "parent": {
      "linkage": {
        "type": "<comment|like|success|other>",
        "id": "<id of parent comment>"
      }
    }
  }
}
```


### Post

Represents a post or creation on a campaign

```
{
  "campaign_id": "<id of associated campaign>",
  "category": <category if relevant>, // null if no comment set
  "cents_pledged_at_creation": <number>, // what was the total sum of all pledges at the time when this was posted?
  "comment_count": <comment count>,
  "content": "<body text>",
  "created_at": "<timestamp>", // presumably can't be null
  "current_user_has_liked": <boolean>, // has the logged in user liked this post?
  "deleted_at": <timestamp>, // null if not deleted
  "edited_at": <timestamp>, // null if not edited
  "embed": { // stuff for embedding videos. if it's not a video, all sub-fields will be set to null
      "description": null, // video description
      "domain": null, // web domain of platform where video is uploaded
      "html": null, // video embed code
      "subject": null, // video title
      "url": null // video url (i.e. on youtube or whatever other platform)
  },
  "id": "<id of this post>",
  "image_height": <height of large image>, // 0 if no image
  "image_large_url": "<url of full size image>", // null if no image
  "image_url": "<url of image shrunk to 320px wide>", // null if no image
  "image_width": <width of large image>, // 0 if no image
  "is_creation": <boolean>, // is this a creation or just a post?
  "is_paid": <boolean>, // true if this is an automated post after a month/content is completed, else false
  "like_count": <number of likes>,
  "min_cents_pledged_to_view": <number>, // minimum individual pledge necessary to view post
  "post_type": "<image|text_only|link>", // may be other unknown values
  "published_at": "<timestamp>", // null if unpublished
  "thumbnail_url": "<url of 75x75 image>", // null if no image
  "title": "<post title>",
  "type": "post",
  "url": "<url to this post>",
  "user_id": "<id of user who posted>"
}
```


### Reward

Represents a reward tier for a campaign. There are two reward objects, id:-1 "Everyone" and id:0 "Patrons Only", that seem to be global and constant for all campaigns.

```
{
  "amount": <number>, // pledge in cents required to access this reward (null if unrestricted)
  "created_at": <timestamp>, // null for global rewards
  "creator_id": null, // only appears for global rewards
  "description": "<html markup>", // description of what this 
  "id": "<unique identifier>",
  "links": {
    "creator": {
      "linkage": {
          "id": "<user id of creator of this reward>",
          "type": "user"
      }
    }
  },
  "requires_shipping": <boolean>, // does patreon require a shipping address from users who enter at this level?
  "type": "reward",
  "user_limit": <number> // how many users are allowed to claim this reward? 0 if unlimited, null for global rewards
},
```


### User

A user object contains information about a Patreon user, usually the currently logged-in user.

```
{
  "about": "<about me>", // an optional bio, can be null
  "created": "<timestamp>",
  "email": "<user email>",
  "first_name": "<first name>",
  "full_name": "<first and last name>",
  "gender": <number>, // 0 if unset, 1 if male, 2 if female
  "id": "<user id>", // is always numeric afaik, but appears as a string nonetheless
  "image_url": "<url to 200x200 jpg>",
  "last_name": "<last name>",
  "status": 1, // not sure what this means, usually it's 1 but I have seen 0
  "thumb_url": "<url to 50x50 jpg>",
  "type": "user",
  "url": "<clean url to user profile>",
  "vanity": "<custom url username>" // null if no vanity username set
}
```