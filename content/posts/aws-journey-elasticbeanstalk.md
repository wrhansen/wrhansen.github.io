---
title: "TIL: AWS Elastic Beanstalk"
date: "2022-12-28T20:15:00-04:00"
author: "Wesley Hansen"
authorTwitter: "wrhansen"
cover: ""
tags:
    - aws
    - til
    - eb
    - heroku
description: "My Experience Learning How To Use Elastic Beanstalk"
showFullContent: false
readingTime: true
draft: false
---

## Background

My immediate family (8 of us total) has a private chat group using the *GroupMe* app [^1]
that we have been using for a couple of years now. When we first started using *GroupMe*
for our chat, I was happy because it offered an [API](https://dev.groupme.com/)
that you use to enhance the group chat. So I played around with it one weekend
and the [hansenbot](https://github.com/wrhansen/hansenbot) was created.

The `hansenbot` is a pretty simple django app that exposes a hook URL that you
can setup that *GroupMe* will forward all messages in your group to. I created the
`hansenbot` to respond to certain commands that are typed in the group chat that
have the following form: `!hb <command>` where `!hb` tells the bot that the given
message is a command that it needs to respond to and `<command>` is one of the
preconfigured commands that I created that report information back to the
*GroupMe* group. These commands I created range from telling "dad jokes", giving
current weather information for all the locations where my family lives and
information about the next upcoming birthday.

Eventually I also added a "daily digest" feature, implemented as a django management
command, that can be run every morning to give an update on all the things the
family cares about. In our case that's upcoming birthdays, reminders for important
dates and weather forecasts.

## Heroku is Easy to Setup

It was relatively easy to whip up a simple django app but in order to configure
the *GroupMe* callback, I needed to deploy this somehwere publicly. Personally
I don't like spending a lot of time on the logistics of deploying stuff so I opted
to use *Heroku* for such a thing. With Heroku, I essentially just had to install
a couple lines of code in the django settings, set up a `Procfile`, connect your
github account and repository and away it goes.

My initial setup was great, and worked really well with *Heroku* because everything
was free, including the "dyno", postgres database, and redis instance. Everything
a dev needs to create a high functioning site. *Heroku* had a pretty decent "free"
tier to get developers hooked on using their platform and
seeing the benefits first-hand. It wasn't until I wanted to introduce a "cron"
job that my *Heroku* setup required some form of payment. The introduction of a
"cron" job (in my case, I created a celery beat process) required an additional
dyno, heroku's concept of a container, which seems to be limited to a single
process. So, bumping up the dynos from one to two moved me a $14/month payment
plan. I have to admit, it was quite high in my opinion but overall I decided it
was still worth it because I really do not like messing around with deployments
and this was still super convenient.

## The Problem: Heroku Made Some Big Changes

This past year, *Heroku* decided to make a pretty significant change to the
pricing on their platform. You can read more about these changes [here](https://blog.heroku.com/next-chapter).
Essentially these changes were going to do away with the "Hobby" tier by no
longer offering free versions of database and redis. Once the change came out my
database and redis instance were gone and my simple little bot no longer worked.
I looked into it and it just wasn't worth it anymore to have my tiny little bot
site deployed on Heroku anymore (it would have doubled my monthly payments to
keep it going)!

So I set out to find a replacement platform where I could deploy my bot site to
at a reasonable price.

## The Switch to AWS

I ultimately decided that I wanted to learn more about AWS, as I am knowledgable
of some services that AWS offers, but wanted to get more of an understanding of
it in general since we use it a lot at work and our use cases are currently growing.
I know AWS is a vast collection of cloud-based tools and services and didn't really
know what the best option for my bot site is. So I asked some of my colleagues
for suggestions on how I should go about deploying this site on AWS. One colleague
suggested I use Elastic Beanstalk and others suggested Lambda. I knew about
serverless lambda as a concept, but I really wanted to try something that was
closer in scope to Heroku's platform to minimize the changes I'd need to make to
my bot site.

So, I decided to create my own private AWS account, and sign up for a year
of "Free Tier", which you can do [here](https://aws.amazon.com/free/?nc2=h_ql_pr_ft&all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all).
My experience with setting up the account was fine, it's fairly simple to enter
in all the standard information, and credit card, etc. When I finally get through
the account creation process and landed on the management console homepage...I was
a bit overwhelmed. You get a sense that you're into something much bigger than
yourself. There's seriously millions of things you can do with this account and
it isn't necessarily immediately clear. To be fair to Amazon, they know this
about AWS (and cloud in general) and provide plenty of links to "getting started"
articles, documentation and tutorials to get you going on your journey into AWS.

{{< image src="/static/resources/aws-help-widget.png" alt="AWS Console Widget" position="center" style="border-radius: 8px;" >}}

More to come...

<!-- ## Elastic Beanstalk Is Confusing...At First

Personally, before I

## Finding A Good Tutorial

## Finding -->

[^1]: hate this service but that's another story
