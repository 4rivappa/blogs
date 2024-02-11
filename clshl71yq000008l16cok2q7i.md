---
title: "Prevent accidental push to master"
datePublished: Sun Feb 11 2024 14:14:37 GMT+0000 (Coordinated Universal Time)
cuid: clshl71yq000008l16cok2q7i
slug: prevent-accidental-push-to-master
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707658209014/f29fc6e5-0733-4a81-b65a-1d336765507f.png
tags: github, git

---

This is how I added some script to my `.zshrc`, so that I won't accidentally push my code to master.

### Context

I was in hurry to test a small change made to an application, which is a GitHub action which will be triggered after a certain event is happened. For testing that small change, I was like crushing my keyboard keys to get it fast. That is when this event happened, I have committed the changes and I was about to execute `git push origin master`. That is master branch of the repository, which has lot of GitHub workflows attached to it. Everything in production will be messed up, like creating or altering the services of AWS and all.

In my university days, we often only deal with single branch (that is master). At this point typing that command `git push origin master` became my mussel memory, where I don't even think about it. This is how it is possible for me, make that mistake again. So I thought of preventing will be great, than let it crash.

### Existing solutions

There is one solution, where we can setup pre-push hook in git. For that we need to make a script file which will be located in `.git/hooks` folder and then make it executable. This is a viable solution, but still it is fixed to its own repository. We can have that rules, in the other repositories. And also, we need to setup the executable permission to that hooks, after every clone of this repo.

This approach is not a fixed solution. I need something which will work for every git repository on my machine, and also there should be no maintenance after every clone. Here comes the another approach, which is basically adding a little script to your `.zshrc` so that, on every command trigger this will be executed first and then the command will be executed. By using the `preexec` zsh hook, we can achieve this. Now this became more reliable solution, as this requires no maintenance for every repository on the machine.

### preexec hook

This is a power hook, which can be used to handle any checks or verification need to be done before executing any command. Basically takes a function or script, as an input and execute that before any command will be executed. In that input function, we can have our logic (which will take care of not having any push to master branch).

That is the overview of idea. By using this hook, I was able to print or verify anything before command execution. But I was not able to figure out how to stop the current command if the condition is not met. Then I got to know, that there is a quick and dirty way to stop the current process, by executing `zsh` on that step, so that all the processes will be stopped and a new zsh instance will be opened. Thanks for Rees Pozzi for his [blog](https://reespozzi.medium.com/cancel-a-terminal-command-during-preexec-zsh-function-c5b0d27b99fb), about cancelling terminal command.

### Final script

The final script, which handles the verification of "push to master" or "push to develop" commands. This basically, checks for "master" and "develop" regex in the current executing command. By this we can prevent directly executing certain commands and have a reconfirmation before execution.

```bash
###################### hold-on for push to master #############################
holdon_message="
     __          __    __             
    / /_  ____  / /___/ /  ____  ____ 
   / __ \/ __ \/ / __  /  / __ \/ __ \\
  / / / / /_/ / / /_/ /  / /_/ / / / /
 /_/ /_/\____/_/\__,_/   \____/_/ /_/                                       
"
###############################################################################
function verify () {
    if [[ ${1} =~ "master" || ${1} =~ "develop" ]]; then
    	echo $holdon_message
        echo "you are trying to execute: '${1}'"
        echo -n "confirm that you know:"
        read confirm_password
        lowercase_pwd=$(echo "$confirm_password" | tr '[:upper:]' '[:lower:]')
        if [[ "$lowercase_pwd" == "i know what i am doing" ]]; then
            echo "executing ...\n"
        else
            echo "\nterminating prematurely ..."
            zsh
        fi
    fi
}

autoload -Uz add-zsh-hook
add-zsh-hook preexec verify
###############################################################################
```

### End state

This is how every command flows through the which ever verification we require.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707656234598/4b20687c-41c7-4f5e-b9e2-f6891fe431f5.png align="center")