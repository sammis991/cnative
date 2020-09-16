# cnative
Install GIT on CentOS - Ref1 <https://www.linux.com/training-tutorials/lets-git-it-installing-git-centos/>
Install GIT on CentOS - DittoRef1 <https://www.linux.com/training-tutorials/lets-git-it-installing-git-centos/>

GIT Basic branching <https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging>

1. Login with root credentials.

2. Use YUM to install it using

yum install git

3.  The installation will handle itself automatically, just hit ‘y‘ when prompted.

4. Once GIT is installed you can check the version using

git --version

This basically does it. But let’s keep going and make sure you’re up to date:
Updating GIT

1. You need to know which distro and version you’re running. You can check this quickly with:

uname -a

2. While getting the base version of GIT is simple, you do need a couple of prerequisites. The first is getting RepoForge (formerly RPMForge repository, that you can grab from repoforge.org/use. Find and download the appropriate package for your distro.

3. Once it’s downloaded, you will need to edit the repo and set it to enabled = 1 using

vi /etc/yum.repos.d/rpmforge.repo

4. Finally run the install command to update it

yum install git

As far as this method goes, that’s all there is to it. But as you can see, to use this method you really need to have more information about your system beforehand. However, if you want to use GIT independent of the distro you’re running. 

Method 2

In order to use this method, you’ll need a longer list of prerequisites installed. 

1. Use these commands to install the prerequisite software

sudo yum groupinstall "Development Tools"

sudo yum install gettext-devel openssl-devel perl-CPAN perl-devel zlib-devel

2. Afterward go to the GITHUB projects releases page and download the latest one:

wget https://github.com/git/git/archive/v2.13.3.tar.gz

You can also rename the file if you like in the same command using:

wget https://github.com/git/git/archive/v2.13.3.tar.gz git.tar.gz

3. Once it’s downloaded, extract it and enter GIT’s root directory /git. Then configure it using the command:

Make Configure
./configure --prefix=/usr/local

4. Now that it’s configured you can finall install it to your system with:

sudo make install

Aaaan you’re good! Now that it’s installed, you can verify which version you’re using:

git --version
Integrating With GITHUB

Finally all that’s left is integrating your local GIT with the GITHUB. To begin, you’ll need to put some information into your local configuration, used to identify your changes in commit messages.

git config --global user.name "Your Name" 
git config --global user.mail "your@email.here"

Note: Hopefully it’s obvious that you should be using your own infromation in the above script

Now just use the following command to see if your information:

git config --global -e

You can also use

git config --list

Generating SSH Keys:

SSH keys are great because they let you connect to other servers without using your password every single time. Check out our article about other neat things you can do with SSH, since it’s a pretty versatile tool. 

For our purposes, we’re going to generate a key and add it to our .ssh directory:

ssh-keygen -t rsa -b 2048 -C "your@email.here"
ssh.add ~/.ssh/id_rsa

Now what’s left is adding the pub file (which is id_rsa) to the GITHUB profile.
Adding Pub File to GITHUB

Login to your GITHUB profile, go into your Personal settings page and get to the SSH and GPG keys panel. Name the key in the Title field.

Then open the pub file you created (id_rsa), copy its contents and paste them into the Key textbox in the SSH and GPG keys. 

Now that this is done, your local git can now maintain an SSH connection with remote GITS! 

Cloning a Repository from GITHUB:

You’ll usually need to get a repository from GITHUB, just due to the nature of what “version control” implies; you need to get the latest version of the materials you’ll be working with. 

If you’re connecting to GITHUB using SSH, like we helped you set up above, you’ll be using this command:

git clone ssh://repo path

If you’re not going to be connecting SSH, you can use a universal command:

git clone https:// repo path

That about does it for this article! We went over finding the right file, download, and installation of GIT on your local machine. Then we went over connecting with SSH and cloning a remote repository. If you got any questions about this, or any other article, you can comment here to hit us up on Facebook or Twitter. 

Finally, we’ll leave you with a table of common commands you’ll be using with GIT. 

-Until next time

by Santosh Kumar D
git clone 	Copy repos from remote servers
git add 	Add files to your local machine
git status 	Get a list of how many files are being committed
git pull 	Pull code from remote repo to a local one
git push 	Push code from your local repo to a remote one
git rm 	Remove files from local repo
git branch -a “remote url”                 	Create a branch in master
git merge 	Merge branches
git reset 	Resets your index and working directory to the last time you made a commit
git log 	Log of all commits made
git rebase 	Merges a side branch into the master branch
 
