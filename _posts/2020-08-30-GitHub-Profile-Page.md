---
layout: post
title:  "GitHub Profile Page"
date:   2020-08-15
desc: "Customizing your GitHub Profile page"
keywords: "GitHub, Profile, Page"
categories: [HTML,DevOps]
tags: [GitHub, Profile, page, profile page]
icon: icon-html
---

Building your network and sharing your work are probably the best ways to get your names out there in the community.

As some of you may already know, GitHub recently added a hidden feature that lets you customize your GitHub profile page.

<img src="/static/assets/img/blog/gitprofile/gitprofile.png" width="550" height="550" />


And here is how to do it.
Once you log into your GitHub account, create a new repository and name it as your Username.
Also, make it Public and check the "Initialize this repository with a README" file button. 
With this step you will get some default README template, and its very good for starting point. 


After that you pin gists and repositories to your profile so other people can quickly see your best work.

You can add even list of some YouTube channels or Blogs from pages. And in order to do this you need to create some Action workflows in the github.
Here are the steps to do this:
* Create a folder named `.github` and create `workflows` folder inside of it. 
* Create a new file named in the `workflowws` directory, named `youtube-workflow.yml` with the following contents inside.

**Note**: Not forget to change the `CHANNEL-ID` with your channel id from YouTube.  

For more details on the Actions Workflows you can check the [gautamkrishnar](https://github.com/gautamkrishnar/blog-post-workflow) GitHub.

```yaml
name: Latest YouTube Videos
on:
  schedule:
    # Runs every hour
    - cron: '0 * * * *'
  workflow_dispatch:

jobs:
  update-readme-with-youtube:
    name: Update this repo's README with latest videos from YouTube
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: gautamkrishnar/blog-post-workflow@master
        with:
          comment_tag_name: "YOUTUBE"
          feed_list: "https://www.youtube.com/feeds/videos.xml?channel_id=<CHANNEL-ID>"
```

There are different feed list that you can add, like from "Stackoverflow" or "Medium". 