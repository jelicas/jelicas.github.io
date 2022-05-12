---
layout: post
title:  "Firebase REST API - Filtering by query params - draft"
date:   2022-05-12 10:51:03 +0200
categories:
---

In this post I am going to show you how to fetch quotes from Firebase Realtime Database made by specific user.

Quotes collection has following structure. 
![Quotes collection](/assets/images/post3/quotescollection.png)

Under [Realtime Database documentation](https://firebase.google.com/docs/database/rest/retrieve-data#filtering-by-a-specified-child-key) you can find how to structure your query in order to fetch filtered data.

To fetch quotes made by certain user we should know his userId (for example 1) and structure the http get request URL like this:

```
https://quotes-app-termin-10-default-rtdb.europe-west1.firebasedatabase.app/quotes.json?orderBy="userId"&equalTo="1"
```

Database rules must be updated with .indexOn rule for userId.
[Indexing data](https://firebase.google.com/docs/database/security/indexing-data)

```
{
  "rules": {
        ...
      
      "quotes": {
         ".indexOn": ["userId"]
    }
  }
}
```

More options on filtering data for fetching you can find on [documentation](https://firebase.google.com/docs/database/rest/retrieve-data#filtering-by-a-specified-child-key)