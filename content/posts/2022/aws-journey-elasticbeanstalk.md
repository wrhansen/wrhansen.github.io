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
of "Free Tier", which you can do [here](https://aws.amazon.com/free/).
My experience with setting up the account was fine, it's fairly simple to enter
in all the standard information, and credit card, etc. When I finally get through
the account creation process and landed on the management console homepage...I was
a bit overwhelmed. You get a sense that you're into something much bigger than
yourself. There's seriously millions of things you can do with this account and
it isn't necessarily immediately clear. To be fair to Amazon, they know this
about AWS (and cloud in general) and provide plenty of links to "getting started"
articles, documentation and tutorials to get you going on your journey into AWS.

{{< image src="/resources/aws-help-widget.png" alt="AWS Console Widget" position="center" style="border-radius: 8px;" >}}

## Elastic Beanstalk Is Confusing...At First

So I started my AWS journey by watching a few videos and reading through some
of the basic overview articles that the above help widget provided. It walked
me through some basic definitions of concepts and services that AWS offers.

I also got acquainted with installing [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
and configuring [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
to give me a separate user admin user account from the root and also setup
additional security like MFA login for the account.

Finally the tutorials lead me to try deploying a simple web app on
[AWS Elastic Beanstalk](https://aws.amazon.com/getting-started/guides/deploy-webapp-elb/).
This tutorial was fairly small and simple to walk through although I must say it
did nothing to help me really understand what Elastic Beanstalk (EB) does. The
tutoral has you write out a bunch of custom javascript that actually create
infrastructure like EC2 instances and S3 buckets. I kinda got the idea, but hoped
that maybe there was a better, more simpler way without having to write all this
custom code (especially in javascript).

I told myself to stick to the tutorial even though I was a little worried about
the direction it was going, and felt like I wasn't exactly learning about what
EB actually was.

## Finding A More Suitable Tutorial

Eventaully I finished that tutorial, and ended up with the product as desired,
and only had to suffer through a few snags in the tutorial where the tutorial
instructions were not entirely up to date with the latest libraries you installed.
A few minutes of searching around the internet was able to clear up any of these
issues so it was fairly straight forward.

After finishing this particularly confusing (to me) tutorial, I decided to dig
a little deeper and see if there was a tutorial that more directly suited my
needs now that the particular track I was following through the AWS help widget
had ended. So I did a quick search for "django aws eb tutorial" and the very first
result was another tutorial on the AWS documentation [here](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-django.html).
I was surprised because the title was verbatim what I was looking for:
*"Deploying a Django application to Elastic Beanstalk"*. Perfect!

## The Right Tutorial

I was able to skip through the first three sections of the tutorial because they
simply involved setting up a python virtualenv, installing django and getting
a basic django project created. Instead, I was just going to tweak my existing
`hansenbot` site to work with this thing. I urge you to follow through the tutorial
linked in the previous section, as I won't detail it here. Instead I just want to
highlight the things that I learned and had to do to get my old heroku site ported
over to EB. So here's a fairly quick rundown of what I had to change:

* Create a `.ebextensions` folder in the root of the project.
* Install `awsebcli` tool. (Note: I chose to do this through [homebrew](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install-osx.html))
* Removed any references to django-heroku library (no longer using it in the project)
* Change database to go off environment variables for RDS connection
* From the aws EB management console, configure a RDS database for the application.
    [link](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-rds.html#python-rds-create)
* Removed any reference to redis (for now) and configured celery tasks to run "ALWAYS_EAGER"
* Configure an *ebextension* config file to run django `migrate` and `collectstatic`
    on container deployment: [link](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-django.html#python-django-migrate-site)
* Configure an *ebextension* cron job to run the bot's "daily digest" command:
    [link](https://aws.amazon.com/premiumsupport/knowledge-center/cron-job-elastic-beanstalk/)

## Things I Learned

* EB works off of commits to your git repository, [by default](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb3-cli-git.html)
* EB can use ProcFiles like Heroku: [link](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/python-configuration-procfile.html)
* You can use `eb shell` to gain shell access to the EC2 instance that is running
    your site. [link](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb3-ssh.html)

## Use Github Actions for Automation

Now that I got everything working minimally, I wanted again to move the deployment
from my local shell (using the `awsebcli` commands) to some form of automation so
that I can "set it and forget it". This was actually relatively easy by configuring
Github Actions on the repository. With a quick google search, I was able to find
someone already had a custom action for building and deploying EB environments.

This was easy to do, by following the readme for the github action defined
[here](https://github.com/einaregilsson/beanstalk-deploy). In the end my github
action ([here](https://github.com/wrhansen/hansenbot/blob/master/.github/workflows/deploy-eb.yaml))
looks like this:

```yaml
name: deploy-aws-eb
on:
  push:
    branches:
      - master
jobs:
  build:
    runs-on: ubuntu-latest
    environment: hansenbot
    steps:
      - name: Checkout Source
        uses: actions/checkout@v2

      - name: Generate deployment package
        run: zip -r app.zip . -x '*.git*'

      - name: Deploy to EB
        uses: einaregilsson/beanstalk-deploy@v21
        with:
          aws_access_key: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws_secret_key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          application_name: ${{ secrets.APPLICATION_NAME }}
          environment_name: ${{ secrets.ENVIRONMENT_NAME }}
          existing_bucket_name: ${{ secrets.AWS_BUCKET_NAME }}
          version_label: app-${{ github.SHA }}
          region: ${{ secrets.AWS_REGION }}
          deployment_package: app.zip
          wait_for_deployment: true
          version_description: ${{ github.SHA }}
```

There isn't really too much to say here, other than this workflow has 3 steps:

* checkout the latest commit from the master branch
* generate a zip for the latest application "build"
* deploy the app on EB with all the environment-specific stuff defined in secrets

With this workflow file installed on my repository, I can now simply merge new
changes to the master branch and that kicks off a job that deploys the new changes
to EB. Excellent!

## Improvements I'd like to make, eventually.

As of the writing of this blog post, it is a fully functioning bot with all the
same features as the heroku version, however the deployment is quite perfect and
so I have a few things that I'd like to work out. Perhaps these could be separate
blog posts when I actually get around to it.

**Configure Celery to work with AWS SQS**. I'd like to do this eventually, as
having dedicated background processes to performing bot commands frees up the
web requests and makes the bot chat feel much more responsive. For now, having
everything executed without Celery (or at least ALWAYS_EAGER) is a "good enough"
solution.

**Configure a "dev" EB environment that works off a dev branch**. I'd like to get
to the point where I have a dev branch that I can work off of that deploys to a
separate "dev" environment of the EB site. This way I can test new features under
development without effecting the production version. This is less of an issue
since the bot is so small in features and is only used by a handful of family
members, but still they can sometimes be annoyed if I flood our chat channel with
lots of spam messages that I might generate while testing new features.

## Some Useful References

[EB workflow](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/platforms-linux-extend.html)


[^1]: I hate *GroupMe*, but that's another story
