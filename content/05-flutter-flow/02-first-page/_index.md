---
title: "First Page"
pre: "2. "
weight: 20
---

{{< youtube asdf >}}

Now that we've created a new project in FlutterFlow, let's explore the process of creating our first page by adding some widgets and data.

## The Concept - "To Do" Application

For this tutorial, we're going to build a simple "To Do" application to help us keep track of our tasks. Our application should have the following features:

- [ ] To Do tasks should include a short title and longer description.
- [ ] To Do tasks should track the date it was created, the date it is due, and whether it has been completed or not.
- [ ] Tasks may optionally have an address associated with the task, and the application should allow the user to request directions to that address.
- [ ] When a task is completed, it should track when (and optionally where) it was completed.
- [ ] To Do tasks should be assigned to different priorities (Low, Medium, High) and sorted according to completion, due date, and priority.
- [ ] Our application should track the percentage of tasks completed on-time (before the due date).
- [ ] Our application should include user accounts so that multiple users can use the app.
- [ ] User accounts should use an email address and password to log in.
- [ ] Each user's data should be stored securely and not accessible by other users.
- [ ] Users should be able to configure their display name and update their password.
- [ ] Users should be able to delete their account and all associated data.
- [ ] User profile pictures should be visible from [Gravatar](https://gravatar.com/)
- [ ] The app should display a global leaderboard showing the users with the highest on-time completion percentage.
- [ ] The app should have a page to display the current weather.

That looks like a long list of features, but we'll slowly work through this list over several steps to build the desired functionality for our application. Our hope is that this list of features will give you enough building blocks to start building your own applications using these features and more!

## Data

Now that we have a basic concept for our application in mind, a great place to start is by thinking about the data our application will need to store. For this initial page we are developing, we'll start with our To Do tasks. For many web and mobile applications, data is often stored and communicated in the [JavaScript Object Notation (JSON)](https://www.json.org/json-en.html) format. Thankfully, we don't need to understand all of the details of JSON to use it in our application, and it is fairly easy to read and understand with a bit of programming background.

So, let's start by creating a basic structure for a single to do task in a JSON format:

```json
{
  "title": "This is a title",
  "description": "This is a longer description for the task",
  "dateCreated": "2026-01-01 00:00:00.000 GMT",
  "dateDue": "2026-02-01 00:00:00.000 GMT",
  "address": "1701D Platt St., Manhattan KS 66506",
  "isCompleted": false,
  "dateCompleted": null,
  "priority": "Low"
}
```

