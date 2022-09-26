#### Why GitHub?
Version control and centralized collaboration. Being able to push code and have it revised. Being able to see code and use it from anywhere without having to copy and paste emails or exchange SD cards and hard drives is crucial. Even if you're on a satellite team, you'll be using GitHub because it makes code review and collaboration that much easier. 

## GitHub Desktop    
GitHub Desktop is an application that adapts the Git environment into GUI form. GitHub Desktop is recommended for everyone first starting out with the Git environment and should make things a lot easier for everyone. I can't stress the importance of pushing code changes and version control.
#### Installation    
1. Go to the git page [here](https://git-scm.com/download/win) and click "64-bit git for Windows Setup."
2. Complete the install. There are a lot of options, but the ones for you are usually already selected.
3. Go to the GitHub Desktop page [here](https://desktop.github.com) and click "Download for Windows (64bit)."
4. Go through the installation.
5. Now that you have all of your Desktop applications set up for GitHub, make sure you have an account. 
6. Login and setup your account.

#### Basics
Let's say that we write a codebase for Robotics, and we want to share that codebase with the programming sub team so they can also make contributions. What we do is publish that codebase onto GitHub. Everyone can **pull** the repo (what the codebase is called on GitHub). What pulling actually does is it creates a local repository on your device, which you can make direct changes to. After your done editing your local repository, you can **commit** the changes, which essentially confirms all the changes you made onto your local repo. Then, you can **push** your local repo, which essentially replaces the repo on GitHub with your local repo. Changes that your friend has made can be **pulled** at any time.
> I highly suggest doing the "Create a tutorial Repository" introduction on GitHub Desktop. 

#### How to Clone a Repo
Cloning a repo gives you a local version of the repo. This local version is what you'll always be using to commit to the GitHub repo.    
A quick guide to cloning a repo:
1. Click "clone an existing repo from the internet."
2. Find the URL for the repo you want to use. That can be found by finding the repo page on GitHub, and clicking that green button labeled "Code," and copying that HTTPS link.
3. Paste that URL into GitHub Desktop.
4. Now, you have a copy of the repo on your computer. Make sure you save the location of the folder you saved that repo in, because you'll need it to find it on WPILib.
5. Make sure to click "Fetch Origin" at the top on the farthest right box.
6. 