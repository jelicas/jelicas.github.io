---
layout: post
title:  "Facebook sign in Ionic with capacitor-firebase-auth - draft"
date:   2022-05-10 12:51:03 +0200
categories:
---

In this post I am going to show you how to implement Firebase Authentication with Facebook provider for Ionic web and android app with a help of Capacitor plugin for Firebase Authentication [capacitor-firebase-auth](https://github.com/baumblatt/capacitor-firebase-auth). 
This post is 

**Requirements**: Installed Node, Ionic CLI, Android Studio and followed [first part of Ionic Firebase Authentication tutorial](https://jelicas.github.io/2022/05/10/google-sign-in-ionic-with-capacitor-firebase-auth-draft.html) in which Ionic app and Firebase project is created.

Project code can be found [here](https://github.com/jelicas/ionic-firebase-auth).

# Update Ionic project

Add button for facebook sign in.

sign-in/sign-in.page.html
```
<ion-header>
 <ion-toolbar>
   <ion-title>sign-in</ion-title>
 </ion-toolbar>
</ion-header>
 
<ion-content>
 <ion-buttons class="ion-justify-content-center ion-margin-top">
   <ion-button color="danger" fill="solid" (click)="signInWithGoogle()>
     Google sign in
   </ion-button>
   <ion-button color="primary" fill="solid">
     Facebook sign in
   </ion-button>
 </ion-buttons>
</ion-content>
```



# Facebook developer setup

Go to [Facebook for developers](https://developers.facebook.com/apps) and create new app.

![Facebook for devs](/assets/images/facebookdev.png)

Select consumer type of app.
![Facebook Consumer Type App](/assets/images/facebooktype.png)

Insert display name.
![Facebook create App](/assets/images/facebookcreateapp.png)

Go to facebook for developers dashboard. Go to Settings. Choose Basic. Here you should see App ID and Secret which you will need shortly.
![Facebook Id Secret](/assets/images/facebookidsecret.png)

# Firebase Facebook Auth provider setup 

Go to [Firebase console](https://console.firebase.google.com) and select [previously](https://jelicas.github.io/2022/05/10/google-sign-in-ionic-with-capacitor-firebase-auth-draft.html) created project ionic-firebase-auth.

In side menu on the left go to Authentication.

![Auth start](/assets/images/firebaseauth.png)

Go to Sign-in method and choose Facebook provider. Enable it with a toggle button, then copy App Id and Secret from facebook app. 

![Facebook enable](/assets/images/facebookenable.png)

To complete set up, add this OAuth redirect URI to your Facebook app configuration. 

Go to Facebook Dashboard and select Facebook login setup.
![Facebook redirect](/assets/images/facebookloginsetup.png)

Select Facebook Settings from side menu and copy OAuth Redirect URI and then save changes.
![Facebook redirect](/assets/images/facebookredirect.png)

Get back to Firebase and click the save button.

# Firebase setup for Web App

Add method signInWithFacebook()

src/app/sign-in/sign-in.page.ts
```
import { Component, OnInit } from '@angular/core';
import { cfaSignInGoogle, cfaSignInFacebook } from 'capacitor-firebase-auth';
import firebase from 'firebase/app';
 
@Component({
 selector: 'app-sign-in',
 templateUrl: './sign-in.page.html',
 styleUrls: ['./sign-in.page.scss'],
})
export class SignInPage implements OnInit {
 
 constructor() { }
 
 ngOnInit() {
 }
 
 signInWithGoogle() {
   cfaSignInGoogle().subscribe(
     (user: firebase.User) => console.log(user)
   )
 }
 signInWithFacebook() {
   cfaSignInFacebook().subscribe(
     (user: firebase.User) => console.log(user)
   )
 }
}
```

Call signInWithFacebook method when button is clicked.

app/src/sign-in/sign-in.page.html

```
<ion-button color="danger" fill="solid" (click)="signInWithFacebook()">
     Facebook sign in
</ion-button>
```


Now, when a user clicks on facebook sign in button, a popup will be shown for signing in with facebook. 

![Chrome pop up](/assets/images/facebookchromepopup.png)

If signing in is successful in devtools for Chrome in console logs you should see user data for registered user. 

![Console log](/assets/images/facebookconsolelog.png)

Checking Authentication in firebase project you should see the user registered.

![Firebase user](/assets/images/facebookfirebaseuser.png)

Now, when everything works fine in web app, we are going to setup android app.

# Configure Android to Project

Update capacitor.config.ts file as follows. 

```
import { CapacitorConfig } from '@capacitor/cli';
 
const config: CapacitorConfig = {
 appId: 'com.ionicfirebaseauth.mobile',
 appName: 'ionic-firebase-auth',
 webDir: 'www',
 bundledWebRuntime: false,
 plugins: {
   CapacitorFirebaseAuth: {
     providers: ['google.com', 'facebook.com'],
     languageCode: 'en',
     nativeAuth: true,
   },
   permissions: {
     google: ['profile'],
     facebook: ['public_profile', 'email', 'user_friends'],
   },
 },
};
 
export default config;
```

{:refdef: style="text-align: center;"}
`ionic capacitor sync android`
{: refdef}

Open generated android folder in android studio and change to project view

![Andorid project view](/assets/images/projectview.png)

# Firebase Facebook provider setup for Android App:

Follow Android [configuration](https://github.com/baumblatt/capacitor-firebase-auth#facebook-android-specific-configurations) that will be also shown in following paragraphs.

Add the dependency for Facebook SDK to your app-level build.gradle file.

android/app/build.gradle

```
dependencies {
    ...
    implementation 'com.facebook.android:facebook-android-sdk:13.2.0'
}
```
![Facebook SDK](/assets/images/fbsdk.png)

Update AndroidManifest.xml
android/app/src/main/AndroidManifest.xml
```
<meta-data android:name="com.facebook.sdk.ApplicationId"
    android:value="@string/facebook_app_id"/>
<meta-data android:name="com.facebook.sdk.ClientToken"
          android:value="@string/facebook_client_token"/>

<activity
    android:name="com.facebook.FacebookActivity"
    android:configChanges="keyboard|keyboardHidden|screenLayout|screenSize|orientation"
    android:label="@string/app_name" />

<activity
    android:name="com.facebook.CustomTabActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="@string/fb_login_protocol_scheme" />
    </intent-filter>
</activity>
```
![Android Manifest](/assets/images/androidmanifest.png)

Add values (Replace [APP_ID] and [CLIENT_TOKEN] with your Facebook App ID and Client token which can be found on the Dashboard for app on Facebook for developers from which you should navigate to Settings > Advanced > Security > Client token.)
android/app/src/main/res/values/strings.xml
```
<string name="facebook_app_id">[APP_ID]</string>
<string name="facebook_client_token">[CLIENT_TOKEN]</string>
<string name="fb_login_protocol_scheme">fb[APP_ID]://authorize</string>
```

On Facebook Developer on Settings Basics add platform Android, choose Google play.
![Add Android platform](/assets/images/androidplatform.png)

Insert package name and class.
Generate hash key following this command (pass is android):
```
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```
![Set data](/assets/images/keyhash.png)

For testing purposes provide Privacy Policy URL and User data deletion with any links if you can not save changes. 
![Dummy URLS](/assets/images/urls.png)

Sync project with gradle files. (File/Sync Project with Gradle Files)

Make a project and run it on an avd emulator (Pixel 2 API 28).

![Emulator](/assets/images/emulatorfb.png)
