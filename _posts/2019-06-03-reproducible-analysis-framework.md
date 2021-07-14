---
layout: post
title: Building a reproducible analysis framework
date: 2019-06-03 22:13:56
description: 
---

Since starting in research I've learnt some home truths about reproducibility in science. 
Thanks some fantastic researchers (listed at the end) I've also been taught some amazing lessons about 
reproducibility, and how to do it. So much so that I can't see why you would work any other way, which is itself
testament to the power of educating a new generation of scientists with lessons learned. 

My main focus so far has been creating a repoducible analysis framework for my quantitative work. How can I ensure that, 
given the same data, someone else (or, future me) could either reproduce or replicate what I have done?

I'm following some pretty basic principles that I have picked up since starting that apply from the very beginning of a project
to the end. Many of them are related to good data management, recording and programming practices - it's just a matter of remembering
to do them all together, all the way through!

## 1. Record the variables you decide to use, why you chose them, and how you refined them. 
In my case I had several spreadsheets which I selected from a cohort study database, and slowly refined so that I know exactly 
which variables I've pruned from my dataset and why. I've kept the source ID for each variable
as well as my own description of what it is. This is essentially a data dictionary. 

If you are planning to do any work with data then keeping a well defined and thorough data dictionary is a must. Personally, working
with large numbers of variables it has been so useful for my own work, but I also know I could share it with anyone. 
DO NOT use colours in your data dictionary - if a colour represents something then turn it into a variable. Anything that won't export to 
csv does not belong there! 


## 2. Folder Structure
I went to a great conference in February called DataTech, run by The Data Lab. One of the sessions was about reproducible code, and gave some 
great advice about working with data. 
Always have a folder where you store your original data exactly as you received it. Lock this down, never edit it. 
Then copy this file for use in your analysis. You never want to be in a situation where you cannot remember what the original layout of your
file looked like because you've overwritten it so many times. 
My file set up tends to look like this: 

```
Project Folder  
├─ raw-data-DO-NOT-TOUCH  
├─ processed-data  
├─ analysis  
├─ figures  
└─ manuscript
```  

I might tweak in the future but at least for now everything is consistent and I can find all my scripts...

## 3. Scripting 
This one kind of dependings on how far you want to go, but we can start small with one simple rule. 

**Avoid pre-processing in Excel.**

Imagine trying to reproduce someone's work - you have the original data file, but the file used for the analysis in their code is completely different. 
You have zero clue how to turn the original data into the analysis-ready data because they did it all using Excel.

To avoid this you have two options. For those who are really code-phobic, you could theoretically use Excel's handy macro feature to record the work you
do on the dataset in Excel. This should produce an Excel Macro script (written in Excel's native language VBA) which would show exactly what you did and
hopefully allow someone else to reproduce it by running the script. 

Similarly in SPSS you could use the internal scripting language to record your actions so that anyone else could see exactly what steps you took to 
create your work. 

Alternatively, you could commit to learning R or Python and write a script to do this for you. A step-up from this 
might be producing functions, or even a package, with your whole analysis framework in it. 

Aside from pre-processing, it's good practice to write scripts for your data analysis. This is harder in GUI based systems such as SPSS, which is why
I would always recommend biting the bullet and just getting stuck in with R. 
My R files used to be a mess of attempted analyses and unordered operations. Even if you had the right dataset, you wouldn't have 
a clue how to replicate my results. Aim to write your analysis as a script that calls all the relevant dependencies and, if executed by a user, could
just run through and output all the results that you have used. Which leads me to my next point...


## 4. Commenting
COMMENT YOUR CODE. 
Apparently, the golden rule is that there should be as much commenting as there is code. 
I find that overkill, but at the very least explain what every method or function does. Why have you used that number as the input there?
What does that obscure argument do?
If someone else went through your script would they know where to edit it to make it fit their data? Would they ask you a million questions 
about how bits of it work?


## 5. Version Control 
This is one I'm working on a lot. At the beginning of a project I have a lot of draft scripts. To begin with they don't look pretty - 
they're messy, full of attempts to do things that don't work and take a lot of development to get to a point where they're ready to share. 

What should happen throughout that process is version control. This is most commonly done using a version control system called git. Git
can be run from the commmand line and basically makes sure you are saving versions of your work as you go through the project. Then, at the end
you have a record of every iteration you went through to get to the final product. 

I'm hoping to do another post soon about using git as part of my analysis system on Windows 10 (which seems to be slightly trickier than unix based
systems e.g linux and mac). 

I hope this has given you some ideas if you are just thinking about how to develop a reproducible analysis framework for working with data. 


P.S. Some people and sites I've found helpful! 
[@amyorben](https://twitter.com/OrbenAmy)
[@kirstiewhittaker](https://twitter.com/kirstie_j)
[Papers with Code](https://paperswithcode.com/)

