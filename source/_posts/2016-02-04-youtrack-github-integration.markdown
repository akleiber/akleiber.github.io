---
layout: post
title: "Youtrack GitHub integration"
date: 2016-02-04 13:42:40 +0100
comments: true
categories:
  - YouTrack
  - GitHub
  - GIT
  - project management
  - JetBrains
---

## The challenge

You can link your YouTrack projects to GitHub projects. When you have setup the VCS integration successful you can edit Youtrack issues via GIT commits.
When I tried it some time ago with my personal account it just worked out of the box. But when I started using an organization account things stopped working. After overcoming all obstacles I got it working. My frustration level reached a maximum when I tried to monitor only the master branch. It just again did not work as described in JetBrains documentation.
I am so glad YouTrack's support team is fast as hell, as far as I can tell.

So in order to make your life easier I will show you the neccessary steps to setup GitHub integration in YouTrack using an organization account and monitoring specific branches.

## The way

We assume you have setup YouTrack and GitHub accounts.

### Setup VCS integration in your YouTrack Project

{% img ../images/2016-02-04-youtrack-github-integration/vcs-setup.webp YouTrack VCS setup %}

1. Navigate to your YouTrack project `project - edit project - VCS`
2. Add a new GitHub VCS integration
3. Create an auth token (just press the link)
4. Repository owner = your organization account name

{% img ../images/2016-02-04-youtrack-github-integration/branch-setup.webp YouTrack VCS branch settings %}

1. Choose the desired repository
2. To only log commits from the master, use `+:refs/heads/(master)` for "Monitor Branches"
3. When you are finished, YouTrack changes the VCS Login from your organization's name to the account name that owns the OAuth token

### Edit YouTrack issues via GIT commits

You can put every command that you can use via the YouTrack command dialog (ctrl + option + J on OSX) in your GIT commits.
Just make sure your commit email matches your YouTrack account email.

{% img ../images/2016-02-04-youtrack-github-integration/issue.webp YouTrack VCS issue %}
{% img ../images/2016-02-04-youtrack-github-integration/commit.webp YouTrack VCS commit %}
