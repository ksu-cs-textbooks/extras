---
title: "Features"
pre: "8. "
weight: 80
---

{{< youtube asdf >}}

Now that we have a mobile application with most of the basic features implemented, let's go through a quick process to implement some additional features that we included in our specification that are relatively easy to add.

As a reminder, here is our current progress on our application:

- [X] To Do tasks should include a short title and longer description.
- [X] To Do tasks should track the date it was created and the date it is due.
- [X] To Do tasks should track whether it has been completed or not.
- [X] Tasks may optionally have an address associated with the task.
- [X] To Do tasks should be assigned to different priorities (Low, High).
- [X] When a task is completed, it should track the date and time when it was completed.
- [X] Users should be able to create, edit, and delete tasks.
- [X] Tasks should be sorted according to completion, due date, and priority.
- [X] Our application should include user accounts so that multiple users can use the app.
- [X] User accounts should use an email address and password to log in.
- [X] User data should be stored in the cloud so they can use the app across devices.
- [X] Each user's data should be stored securely and not accessible by other users.
- [ ] If a task has an address, the application should allow the user to request directions to that address.
- [ ] If the user gives permission, it should also track the location where a task was completed.
- [ ] Our application should track the percentage of tasks completed on-time (before the due date).
- [ ] Users should be able to configure their display name and update their password.
- [ ] Users should be able to delete their account and all associated data.
- [ ] User profile pictures should be visible from [Gravatar](https://gravatar.com/)
- [ ] The app should display a global leaderboard showing the users with the highest on-time completion percentage.
- [ ] The app should have a page to display the current weather.

Let's start working on a few of these!

## Request Directions

One quick feature we can add is the ability to request directions to a location stored in the task if one is present. To do this, let's go to our **ToDoItemComponent** and add an action to the **AddressIconButton** widget. In the Action flow editor, we'll start by adding a **Conditional Action** to to the **On Tap** action to check if the application is running on the web using the **Is Web** entry under the **Global Properties** list. 

On the **TRUE** branch, we'll use the **Launch URL** action found under the **Share** option. Then, we'll create a quick **Inline Function** that uses the `address` field as a parameter and includes this code:

```dart
"https://www.google.com/maps/search/?api=1&query=" + Uri.encodeComponent(address)
```

This will construct a Google Maps URL that will search for that address on the web.

On the **FALSE** branch, we can find the **Launch Map** action under the **Widget/UI Interactions** section, and then set the **Place Type** to **Address** and then link it to the `address` field in our task.

The full process is shown in the animation below:

![FlutterFlow Map Action](images/mapaction.gif)

Once that is done, we can test it out in **Test Mode** to confirm that it works!

![FlutterFlow Map Action Test](images/maptest.gif)

As we can see, many of these more complex actions can be handled directly by FlutterFlow!

## Location Tracking

Another feature we should include in our application is the ability to track the location where a task was completed. To enable this, we first must go to the **Settings** page and look for the **Permissions** section under **Project Setup**, and then enable the **Location** permission. When we install our application on a device, this will request access to the device's location so we can use it in our application.

![FlutterFlow Location Permission](images/location.png)

{{% notice note "Follow the Guidelines" %}}

When publishing an application to an app store, it must abide by guidelines concerning the data collected and how it is stored and processed. As a developer, it is our job to make sure we read, understand, and adhere to those guidelines. Thankfully, there are helpful links at the top of the **Permissions** settings page where we can find the current guidelines for each app store.

{{% /notice %}}

We can also add a new `locationCompleted` field using the **Lat Lng** data type (short for "Latitude Longitude") to our `to_do_tasks` collection in Firestore to store this data:

![FlutterFlow Add Location Field](images/addloc.png)

Now that we've enabled that setting, we can update the **On Toggled On** action attached to our **Switch** widget in the **ToDoItemComponent** to also set the `locationCompleted` field to the value in the **Current Device Location** option under **Global Properties**

![Update Location](images/updateloc.png)

We may also want to make a similar change to the **On Toggled Off** action that clears the `locationCompleted` field.

Now, when we run our app in **Test Mode**, we should see our application try to store the location where a task is completed via the [Firebase Console](https://console.firebase.google.com/)

{{% notice note "Location Limitations" %}}

Unfortunately, the FlutterFlow **Test Mode** does not support actually loading the user's current location through the web browser, so we'll only see the coordinates `[0° N, 0° E]` stored in Firebase. On a real device, this location would be read from the phone's settings.

Of course, this assumes that the user has allowed the application to read the device's location, and that location services are enabled. A more advanced app would include some logic here to check if the location is enabled and available first, and if not it can prompt the user to enable it. 

{{% /notice %}}

## Change Password

We already have a place where user's can update their profile's display name and other settings, but we don't currently have a way for users to change their password. So, let's change that!

First, we need a component that we can load to prompt the user to change their password. While we _could_ build this component by hand, let's try some of the AI assisted features in FlutterFlow to see how they work.

First, we'll go to the **Page Selector** and then click the **Generate with AI** button at the top to add new content to our application.

![FlutterFlow Generate with AI](images/aibutton.png)

In the window that appears, let's provide an easy to follow prompt for the system. The prompt we'll use is

> Generate a component to be loaded as a bottom sheet to allow the user to edit their password. It should include two password text fields, as well as save and cancel buttons. The design should follow the standard theme used throughout the application with helpful colors and icons. 

Finally, click the **Component** button at the bottom, then click the **Up Arrow** button to let the AI start generating. At the top, you'll see a new icon start spinning while the AI is working in the background, and after a minute or so, you should be able to click on it and see the newly generated component. Here's what our example looked like:

![FlutterFlow Generated Component](images/generate.png)

In this view, we can customize our prompt, set some color schemes, and more. If we are satisfied, we can click the {{% button href="#" color="blue" %}}Insert Component{{% /button %}} button at the bottom to add that component to our application.

Now, let's go to our `auth_2_Profile` page and add a new button to allow users to change their password. We'll just quickly duplicate the **Content View** widget used for the `Edit Profile` button and tweak it a bit to match our needs. Remember that you can duplicate a widget simply by right-clicking on it and choosing **Duplicate**.

Once it is customized, we'll set the **On Tap** action to load our new component in a bottom sheet:

![FlutterFlow Load Change Password](images/changepassword.png)

Finally, on our new component, we need to configure the **Cancel** button to dismiss the bottom sheet:

![FlutterFlow Cancel Change Password](images/cancelpassword.png)

Likewise, we need to configure the **Save Password** button to actually update the user's password using a the **Update Password** action as shown in the animation below:

![FlutterFlow Change Password Action](images/changepw.gif)

Once that has been updated, try the application in **Test Mode** and see if you can update your password!

{{% notice note "Recent Authentication" %}}

As a security measure, Google Firebase requires a recent authentication in order to change a user's password. A simple way to do this is log out and log back in before trying to change the password.

In a real application, often we would add additional functionality to ask the user to input their current password again to perform a new authentication first, then show the interface for updating the password. 

{{% /notice %}}





