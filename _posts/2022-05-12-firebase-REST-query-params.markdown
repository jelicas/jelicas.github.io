---
layout: post
title:  "Firebase REST API - Filtering by query params"
date:   2022-05-12 10:51:03 +0200
categories:
---

On Mobile App Dev (MAD) course we are communicating with Firebase Realtime Database through exposed Firebase REST API using Angular Http Client (in order to students that arent familiar with REST could get a grasp of it). We implemented simple CRUD operations through API. The frequent mistake of students is filtering data on client side instead of leaving that logic to Firebase to do it for us. I created this post so I could just send my students link for it when I get the question regarding this.

In this post I am going to show you how to fetch quotes made by specific user through Firebase REST API from Firebase Realtime Database. 

Quotes collection from MAD course has following structure. 

![Quotes collection](/assets/images/post3/quotescollection.png)

Under [Realtime Database documentation](https://firebase.google.com/docs/database/rest/retrieve-data#filtering-by-a-specified-child-key) you can find how to structure your query in order to fetch filtered data.

We are going to filter quotes collection by a child key - userId and that would be a value inside quotation marks for orderBy query parameter in URL. For second parameter we choose equalTo and its value would be userId.
To fetch quotes made by certain user we should know his userId (for example "1" - if its number omit quotation marks) and structure the http get request URL like this:

```
https://quotes-app-termin-10-default-rtdb.europe-west1.firebasedatabase.app/quotes.json?orderBy="userId"&equalTo="1"
```

Of course you should not hard code userId, but you get the point.

If you want to filter data by specific child key, in this case by userId, database rules must be updated with .indexOn rule for userId.

[Indexing data](https://firebase.google.com/docs/database/security/indexing-data) docs.

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

More options on filtering data for fetching you can find on [documentation](https://firebase.google.com/docs/database/rest/retrieve-data#filtering-by-a-specified-child-key).