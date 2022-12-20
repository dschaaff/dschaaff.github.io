---
author: dschaaff
categories:
- techology
comments: true
date: "2018-02-09T18:42:27Z"
link: http://danielschaaff.com/2018/02/09/better-jenkins-notifications-in-declarative-pipelines/
slug: better-jenkins-notifications-in-declarative-pipelines
tags:
- groovy
- jenkins
- pipeline
- slack
- technology
title: Better Jenkins Notifications in Declarative Pipelines
wordpress_id: 867
---

I’ve been using declarative pipelines in Jenkins for a while with the Slack [plugin](https://github.com/jenkinsci/slack-plugin) to send build notifications to Slack. The plugin does what it says on the tin but gives you a pretty boring message by default.

![E8F315D1-04A6-4DC4-B0D8-1E1E7ED42D08.png]({{ site.url }}/assets/img/e8f315d1-04a6-4dc4-b0d8-1e1e7ed42d08.png)

I used the environment variables available in the pipeline to make things a little bit better and link back to the job.

![08A97422-A7E2-4AB5-A65B-68EF7B5AE196.png]({{ site.url }}/assets/img/08a97422-a7e2-4ab5-a65b-68ef7b5ae196.png)

But I was still always disappointed the notifications didn’t contain more information. Thankfully version 2.3 of the [plugin](https://github.com/jenkinsci/slack-plugin) added support for the attachments portion of the [Slack message API](https://api.slack.com/docs/message-formatting). I was able to leverage the attachments feature to get better message formatting. Meanwhile, I took some inspiration from [this thread](https://stackoverflow.com/questions/39920437/how-to-access-junit-test-counts-in-jenkins-pipeline-project) to incorporate test result summaries.

I store this in a shared pipeline library to avoid repeating the code in every Jenkinsfile. This way you can simply call it in a post step like this.

```groovy
post {
  always {
    notifySlack currentBuild.result
  }
}
```

Here is the code in the pipeline library

<script src="https://gist.github.com/dschaaff/bd3275c1395a48cef1a82708d3a55f5d.js"></script>

The end result is a much more informative message.

![Screen Shot 2018-02-09 at 11.22.37 PM.png]({{ site.url }}/assets/img/screen-shot-2018-02-09-at-11-22-37-pm.png)