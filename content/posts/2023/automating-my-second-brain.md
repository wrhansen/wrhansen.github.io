---
title: "Using Github Actions to Automate My Second Brain"
date: "2023-03-12T20:15:00-04:00"
author: "Wesley Hansen"
authorTwitter: "wrhansen"
cover: ""
tags:
    - second-brain
    - notion
    - github-actions
    - steam
description: "Using Github Actions to integrate Steam Web API with Notion API"
showFullContent: false
readingTime: true
draft: false
---

## Building a Second Brain with Notion

For the past year I have been building my second brain[^1] in Notion. I use
Notion daily to store web clippings, research and meeting notes for work, the
books I read, book highlights[^2] and notes, and even the upcoming movies and
TV shows that I want to watch. The Notion database provides a great user experience
for capturing, filtering and sorting lists of data like that.

## Tracking Perfect Games on Steam Deck

Recently I ran into a scenario that led me to want to track the achievement
progress I've made on games played on my Steam Deck. In Steam, they call this
stat a "Perfect Game". Your profile page on Steam actually tracks this stat and
I would check it occasionally after completing a game on Steam just to see my
latest "Perfect Games" count. Here's an example of what my profile looks like:

{{< image src="/resources/steam-profile.png" alt="Steam Profile" position="center" style="border-radius: 8px;" >}}

The scenario that led me to want to track my own achievements occurred one day
when I checked my profile on Steam and noticed that the "Perfect Game" count
*actually decreased*! How could this happen? After some looking around I
discovered that this occurred because a game I had unlocked all achievements for
recently had a new update to the game that included a few new achievements as
well. Okay, so all I have to do to regain the 100% achievements unlocked for such
a game is to unlock the new achievements that were added? The unfortunate part
is that the Steam profile page does not really indicate which game(s) that were
once perfect are no longer so that's why my second brain comes in.

## How the Integration Works

It's simple, really. I can use the Steam Web API[^3] to get a list of all the
games that I have played with some progress, note which ones are "perfect games"
and keep track of them when they no longer become "perfect". This is because the
Steam Web API allows you to retrieve your achievement status for each game you've
played. After downloading the current state of my game achievements from the
Steam Web API, I can use the Notion API to add them to my "Games" database. This
is a special database I created in Notion where I want to keep track of all the
games I've played and the current achievements status for them. So with the current
state from the Steam Web API, I can use the Notion api to query the current state
of all the games that I've saved in my second brain, compare them, and then patch
any updates to my second brain with the Notion API. This comparison is key, because
it's where I can track, through a property on my Notion "Games" database if a
game "was perfect". This way, I can know exactly which games I've got a "Perfect"
on that are no longer considered "Perfect" in Steam. I wrote this integration
in a simple python script which you can check out here:
<https://github.com/wrhansen/steam-games-tracker>.

## Automating it with Github Actions

Finally, with my `game_data_fetcher.py` python script complete, I needed a way
to run this periodically to keep my Notion second brain up to date with the latest
achievement data from Steam. I *could* just run this script whenever I feel like
from my laptop, but that's no fun, so I should automate it. So I started thinking
of ways that I could deploy this as simply as possible to run it on a schedule.
The script itself doesn't require much, there is a miniscule amount of processing
and some networking to retrieving the data from the two sources and that's it.
Initially I was thinking of deploying it to a AWS, but realized that was overkill
and I could really just get away with using Github Actions to run the simple
python script. Take a look at `.github/workflows/update.yaml` in the repo linked
above for the final version of the github action that I created. Refer to the
following diagram for a summary of how this automation/integration works:

{{< image src="/resources/steam-to-notion-integration.png" alt="Steam Profile" position="center" style="border-radius: 8px;" >}}

## Takeaways

Automation is fun, but sometimes it takes some training to identify where you
might need it. Github Actions is a really fun, free[^4] and easy to create simple
automations for a lot of your personal needs. I am going to start thinking more
about what things I can automate and capture inside my Notion second brain. The
Notion API is really simple to use and has great documentation, so I look forward
to using it again in the future.

[^1]: Highly recommend this book: <https://www.amazon.com/Building-Second-Brain-Organize-Potential/dp/1982167386>
[^2]: I use Readwise to help automate my kindle book highlights: <https://readwise.io/>
[^3]: <https://developer.valvesoftware.com/wiki/Steam_Web_API#GetNewsForApp_.28v0001.29>
[^4]: For public repos, you get a lot of cool Github Actions freebies: <https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions#included-storage-and-minutes>
