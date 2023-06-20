---
title: "Initialize"
pre: "3. "
weight: 30
---

## Create Repository

Start by creating a new GitHub repository. We'll let it initialize the repository using a `README` file, and also select the `.gitignore` file for a Node project.

![Create Repository](../images/repo.png)

If you've already created the repository, you can get the `.gitignore` file for Node projects from GitHub's [gitignore repository](https://github.com/github/gitignore/blob/main/Node.gitignore)

## Check out Repository

Next, check out the repository:

```bash
# Terminal
git clone git@github.com:russfeld/fullstack.git
cd fullstack
code .
```

{{% notice tip %}}

Change the repository URL to match your repository!

{{% /notice %}}

This should open the repository in Visual Studio Code.

## Create the File Structure

Create the following folders within your project:

* `client`
* `server`

These folders will store the frontend and backend applications, respectively.

At this point, you should have the following structure:

![Initial Structure](../images/initial.png)