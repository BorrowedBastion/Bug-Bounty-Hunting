# Installing GO on Kali Linux 2021

## TL;DR

_____________________________________

In your terminal:

1. `cd ~`
2. `wget -c https://golang.org/dl/go1.16.2.linux-amd64.tar.gz -O - | tar -xz`
3. Using a text editor (Vim, Nano, etc.) copy and paste `export PATH=$PATH:~/go/bin`  to the very end of `~/.bashrc` and `~/.zshrc`.
For example:

    ```
    nano ~/.bashrc
    nano ~/.zshrc
    ```

4. Close terminal, open a new terminal.
5. Check the install with `go -version`
6. If it worked, enjoy! If it didn't, read on and troubleshoot. Then yell curses into the sky if it still doesn't work. 

____________________________________________

If you're bug bounty hunting, you've probably needed to install Go (golang) at some point. If you haven't yet, you should. I don't even like Go, but this isn't about me. A lot of awesome security research tools are written in it.

At the time I'm writing this (04/2021) a lot of Go installation instructions are *wrong* for Kali Linux. You can't get it to work... at all. If it was working, it's not now. You follow the official documentation to install and it f*!ks everything up. Getting more desperate, you look on StackOverflow, that turns your terminal into spaghetti. You read random Kali specific blog posts and now you think you've compromised your whole system. So you lookup current troubleshooting guides. And that brings you here. Reading a random page because with a potentially compromised system that still wont consistently run Go. 

I might be able to help clarify some common mistakes. Most likely you are getting it almost right.

You may already know this and just need a reminder to connect the dots. That's ok. I would say "feel free to skip this part," but if you're here you probably should read it.

Before going through this, try your best to clean up any changes made previously when trying to install Go. My best guess is deleting any lines you added in .bashrc, .bash_profile, and .zshrc for both root and users. Delete any go specific folders or files.

Most of the issues I see boil down to two issues: the wrong environment, or the wrong terminal.

## Environments

On any fresh install of a Kali system you have two users. The first is the admin named `sudo` which stands for "super user do". The second is the user. In my case I'm using the name  `kali` , however you may have a different user name. Type `whoami` in the terminal to see your current username.

![image](/assets/images/whoami.png)

`sudo` and `kali` are two separate profiles with different environments. 

You've probably needed to use `sudo` when doing something too exciting for your normal user. Like writing files to places you shouldn't, messing up your drives, or in a last ditch effort trying to install golang.

The user `kali` won't see Go that has been installed using `sudo` in the root directory because you installed it for the admin, not the user. That applies the other way around as well. `Sudo` by default won't use something that has been installed for the user `kali`. 

We want to install Go in the user home. If you're bug bounty hunting you don't want to install it in root, because you'll be downloading semi-sketchy, quasi-random programs from the society of l33t hax0rs. You should be in a vm somewhere, your testing box should *not* be your main OS! You shouldn't trust the programs you're downloading and you shouldn't have to.  

In the image above, see the `~` after kali?  It's telling us we are in the home directory for the user. This is not the top directory! It is the user's home.  Just `cd ..` (go up a level) once and you'll see it change to `/home` , twice and you'll end up at `/` , the root directory. To see a bunch of lovely configuration files use `ls -a`. If you are installing something with a `~` before the directory name, then you'll be installing it for the current the user. 

## Different Terminals

Most install guides for Go tell you to edit your bashrc or bash_profile. Often in Kali, you are NOT using bash! You may be using zsh. 

This is what zsh in Kali looks like:

![image](/assets/images/zsh.png)

This is what bash in Kali looks like:

![image](/assets/images/bash.png)

Your zsh terminal wont see the path configuration (or anything else) put into bashrc or bash_profile. Even worse, trying to get it to see your changes using `source .bashrc` in zsh will mess up your terminal, it's the wrong instruction file.  For the cringe, here is a screenshot of what that looks like: 

![image](/images/scary_wrong_source.png)

To get rid of that nonsense, either close your terminal and open a new one or run `source ~/.zshrc`. 

![image](/assets/images/fix_source.png)

By putting go's PATH variable in both .zshrc and .bashrc, and then closing the terminal and opening a new one, go should work from now on in both kinds. You can use source if you'd like, just make sure you source .bashrc in a bash terminal, or .zshrc from a zsh one.

# Install Go in Kali - Long Version

Let's make some decisions:

1. We are installing Go for the user.
2. We want both the zsh terminal and the bash terminal to use Go.

I am using Kali 2021.1 and go 1.16.2. To use the most current version, open [https://golang.org/dl/](https://golang.org/dl/), right click copy the link for Linux under featured downloads. If it's different than the one I use here, update the steps with your version's file name.

![image](/assets/images/Version.png)

## Get and Unpack the Installation Files

1. Change to user's home directory:

    ```
    cd ~
    ```

2. Run this command to get the file and unpack it in one go.  ;)

    ```
    wget -c https://golang.org/dl/go1.16.2.linux-amd64.tar.gz -O - | tar -xz
    ```

## Adding the PATH

1. Still in `~`, add `export PATH=$PATH:~/go/bin` to the end of the `~/.zshrc` and `~/.bashrc` files.

    ```
    vim ~/.zshrc
    ```

    Just in case you haven't used Vim, I'll walk you through it. 

    In Vim:

    - Go to end of file by pressing `shift + g`
    - Press `i` to insert
    - Copy and paste `export PATH=$PATH:~/go/bin` to the very end of the file.
    - Press `esc` to exit the insert mode
    - Type `:wq` to write and quite Vim.
    - Repeat the process with `~/.bashrc`
2. Close your terminal window and open a new one to have the new settings.

## Testing Go

Type `go version` in to see if it returns the installed go version. Try to go get something. If you don't know where to start, try one of [TomNomNom's](https://github.com/tomnomnom/) tools.

Cheers!
-Bastion

Feel free message @borrowedbastion for help.
