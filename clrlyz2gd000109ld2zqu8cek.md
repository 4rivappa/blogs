---
title: "Building PrePush"
datePublished: Sat Jan 20 2024 11:11:41 GMT+0000 (Coordinated Universal Time)
cuid: clrlyz2gd000109ld2zqu8cek
slug: building-prepush
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1705749478288/f9172af5-06ee-4683-8fb8-62f5283883ae.png
tags: development, github-actions

---

This is the documentation of process, building a side project prepush (haha xD, yeah inspired from pre-commit).

## The context

Ok, let me explain what was going on at that time. As part of my internship I was supposed to add a feature to an existing GitHub workflow, which will be triggered on push to particular folder in that git repository. I made all the required changes, and everything is working good. After this I need to test these changes on GitHub itself, by pushing to my remote branch (so that I can check the triggered workflows, if they are going as expected or not).

And this is when, I created a mess. In order to check the workflow, I made some modifications to some files in local (just adding a comment or so). I don't know why, but there is a dockerfile in it too. I made a commit for these changes, and pushed it to the remote. And there we go, I triggered another workflow, which is related to the updating the new docker image to private image registry. Ohmygod, I was like .. what the heck happened ? And then for some dependency issue, these triggered workflows are failed (didn't try to push new docker images to private registry).

## Problem, I observed

I know, this is a small issue. But nevertheless we have the possibility to invoke workflows (which are directly connected to production and develop cloud instances). That means there are possibilities, where disasters can happen. At that time, my mind was like thinking .. "I am so dumb, that I didn't check the other workflows .. " and after some time I was like "Who on earth, can go through all the other workflows of a large repository .. " and later "Is there any place, where every developer notes down the possibilities of a workflow invocation"  
and then I thought .. "Is there any tool, which can check for the possible triggers in workflow before we push .. "  
I searched about this, I found None. So I was like "Ok cool, lets make one - which indicates all the possible workflow triggers after you push to the remote"  
This is where, it started.

## Availability decision

Before all this, I was using some tool called pre-commit. Which checks for all the indentation and other rules which are followed by our team. This is a command, which can executed on a git repository with config.  
I thought like, this should be the case ... anyone can be able to install it using just a simple command and use it through command line, with just a single command ...

I was like, ok lets go an build some package to 'pip' and hell yeah this is my first pip project

## Solution

After thinking about the problem, there are some required inputs .. which are needed to calculate these expected workflows (which are going to be triggered)

* we need modified files, from the previous commit
    
* we need all the workflow trigger conditions (like on push for path '\*')
    

And then we are good to go. Yeah that happened, I calculated the first one, by executing git commands from the python itself and the other by yaml parser (pyyaml)

After getting these, we have the task to check for all possible matches across these files and patters. I thought, this will be easier task. But turns out to be hardest one to research a lot to use, which modules .. first I used `pathlib`'s PurePath, but then it didn't work for all the test cases we have, and at last I landed on using `fnmatch`.

## Publishing to PIP

Just created an account on pypi, and then enabled mfa. After then went through some blogs (realpython, haha xD), came to know the process to build and publish a package. Made the necessary changes, at last pushed it to pypi. After some more modifications and other fixes, at last settled at functional pack version.

For all these, I was up until 3:50 am. It was a nice experience to start working on my personal projects again and also with pypi. It was good ✨

At this point, I would like to share some words to my prev self ..

"start, at what you are .. don't worry about things going wrong .. that is a normal thing, and also start bringing all of your thoughts to real world .. xD"

"Don't compare yourself to other"

prepush: [https://pypi.org/project/prepush/](https://pypi.org/project/prepush/)