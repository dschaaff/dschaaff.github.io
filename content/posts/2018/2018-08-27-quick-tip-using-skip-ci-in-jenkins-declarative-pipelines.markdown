---
author: dschaaff
categories:
- techology
comments: true
date: "2018-08-27T22:57:53Z"
link: http://danielschaaff.com/2018/08/27/quick-tip-using-skip-ci-in-jenkins-declarative-pipelines/
slug: quick-tip-using-skip-ci-in-jenkins-declarative-pipelines
title: 'Quick Tip: Using [skip-ci] in Jenkins’ Declarative Pipelines'
wordpress_id: 877
---

You’ve spent the past hour meticulously crafting a Readme update and its time to commit. Great, but what if you don’t want that commit to trigger automated testing, deploys, and other actions? If you’re using Jenkins declarative pipelines there’s a pretty simple solution.

Add the below when block to each stage you wish to skip in your Jenkinsfile.

```groovy
when {
    not {
        changelog '\\[skip-ci\\]'
    }
}
```

We can also expand upon this for other actions if desired. For example, use the following around a deploy stage to avoid deploying pull requests, anything not on the master branch, and to respect the `[skip-ci]` param in a commit message.

```groovy
stage('Deploy'){
    when {
        allOf {
            branch 'master'
            not {
                anyOf {
                    changeRequest author: '', authorDisplayName: '', authorEmail: '', branch: '', fork: '', id: '', target: '', title: '', url: ''
                changelog '\\[skip-ci\\]'
            }
          } 
       }
}
steps {
    deploy()
    }
}
```
