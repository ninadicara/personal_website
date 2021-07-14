---
layout: post
title: Paper-a-day bot with no code required
date: 2021-01-12 11:00:00
description: Using AirTable and Slack to make yourself a bot
---

This year I wanted to have a go at a paper-a-day challenge; reading one paper every (working) day. Over the last year I have been building up a list of papers 'to-read' but never getting around to them, and generally not making reading much of a priority when other things have been busy. Trying to read a paper a day seems (probably naively) like a good way to quickly get through some of my outstanding reading lists, and prepare for doing a lot more thesis writing over the next year or so. 

I know that being prepared for my motivation to be minimal is essential to completing... anything. Personally my solution to that is usually to over-prepare early on in a project when I feel enthusiastic about it, and hope that that carries me over the times where I don't feel like putting much time or effort in.

For this project the solution is setting up a spreadsheet of papers that I want to read, and a Slack bot that sends me a new paper every morning. That way there is no faffing around trying to decide on a paper, find an open access link, or even remembering to do it in the first place.

Now, whenever I see a new paper I'd like to read I also add it to the bottom of the list so I know I'll get around to it eventually.

So, how does is work? 

## 1. Build an AirTable 'base'

Sign up for or log-in to Airtable. 

Start a new 'base' - this is, conceptually, very like a spreadsheet, with the advantages of being able to behave like a database when needed. 

Below is an embedded version of my base as it is currently. 

<iframe class="airtable-embed" src="https://airtable.com/embed/shrBYG3tarMetbmZh?backgroundColor=cyan&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

The key column is each day of this year (type the date in the top then drag down as you would in Excel/Sheets). I then have columns for some basic information, a multi-select column to categorise the topic of the paper (hoping this might be useful later), and most importantly a URL to the pdf or open access page. 

## 2. Create a Slack channel to send your papers to 

In your Slack workspace create a new channel to send your papers to. As far as I have found it needs to be a channel, rather than a direct message to yourself, for example. 

## 3. Set up automations on AirTable

On the top right in AirTable, click 'Automations', 'New Automation'. 

You want the Trigger to be run on the table you just made, called 'Papers' here. It should act when the Date (the first column in the example above) is the same as today, and when the Paper URL is not empty (this would probably mean I've forgotten to put something in it).   

<img src="/images/automation_1.png">

Next, you choose what action you want to happen when that trigger is activated. 

Click on 'add action', and choose to send a Slack message. You will need to connect your Slack account, and authorise AirTable to post to Slack.

This automation will then send a messgae with the title, the authors, journal and date of the paper followed by the URL link you have set, to the Slack channel you have chosen. 

<img src="/images/automation_2.png">

## 4. Happy reading!

Just after midnight each day your bot will post that day's paper to Slack, ready for you first thing 😄

<img src="/images/slack.png">

Something I haven't done yet is set it to only send me papers on working days, but you could do this in a couple of ways: 

1. Only include weekday dates in the date list
2. Add a column for day of the week, and include the logic to ignore Saturdays and Sundays in the automation. 



There you go, a no-code solution to making a paper-a-day bot! 