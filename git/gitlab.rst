*************
Git
*************


Overview
*************

.. note:: Estimated time to completion: **90 MINUTES**

Git is a distributed revision control and source code management system with an emphasis on speed. Git was initially designed and developed by Linus Torvalds for Linux kernel development and is a free software distributed under the terms of the GNU General Public License.

This lab guides users how to use Git for project version control in a distributed environment while working on source code and non-source code projects.

The Lab will help beginners learn the basic functionality of Git version control system. After completing this lab, participants will have a moderate level of expertise in using Git version control system to build upon.

This lab assumes participants are going to use Git to handle various projects. It's good to have some exposure to software development life cycle and working knowledge of application development but not required.


Basic Concepts
***************
Version Control System (VCS) is software that helps software developers work together and maintain a complete history of their work.

Listed below are the functions of a VCS:

- Allows developers to work simultaneously.
- Does not allow overwriting each other’s changes.
- Maintains a history of every version.

Git Advantages
***************

Open Source
===========
Git is released under GPL’s open source license. It is distributed freely. You can use Git to manage projects without licensing costs. Because it's open source, you can download the source code and make changes that have affinity with your project requirements.

Fast and Compact
=================
Most of the operations are performed locally providing benefit in terms of speed. Git doesn't depend on a central server; which is why there's no need to interact with remote server(s) for every operation. Git's core is written in C, which avoids runtime overhead associated with other high-level (interpreted) languages. Git mirrors an entire repository, the size of the data on the client side is small, illustrating the efficiency of Git at compressing and storing data on the client side.

Implicit backup
================
The chances of losing data are reduced because there are multiple distributed copies. Data present on any client mirrors the repository so it can be used in the event of data corruption or disk crash

Security
=========
Git uses a widely adopted cryptographic hash function called secure hash function (SHA1), to name and index objects within its database. Every file and commit is check-summed and retrieved by its checksum at the time of checkout.

Simple branching
=================
Typical CVCS systems use cheap copy mechanisms when creating a new branch. Git branch management is very simple taking only a few seconds to create, delete, and merge branches.

Terminologies
**************

Local Repository
================
VCS tool provide private workplace(s) for working copies. Prject contributors (developers) make changes/modifications within their private workplace then commit changes. These committed changes then become a part of the repository. Git takes it one step further by providing a private copy of the entire repository. Users can then perform multiple operations on the repository such as add file, remove file, rename file, move file, commit changes, etc... .

Working Directory vs. Staging Area (Index)
==========================================
The working directory is where files are checked-out. In other CVCS systems, developers typically make modifications and commit their changes directly to the repository. Git however doesn’t track every modified file. When users perform a commit operation, Git looks for the files present in the *staging area*. Only files within the staging area are candidates for commit.

Let's look at a basic Git workflow:

Step 1 : User1 modifies a file(s) within the working directory.

Step 2 : User1 then adds the modified file(s) to the staging area.

Step 3 : User1 performs a commit operation which moves the file(s) from the staging area. After a push operation, it persists the changes permanently to the Git repository.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/git/image1.png

Suppose you modified two files, namely *foo.c* and *foobar.c* and you want two different commits for each operation. You can add one file in the staging area and do commit. After the first commit, repeat the same procedure for another file.

.. code-block:: bash

  # First commit
  [bash]$ git add foo.c

  # adds file to the staging area
  [bash]$ git commit –m “Added foo operation”

  # Second commit
  [bash]$ git add foobar.c

  # adds file to the staging area
  [bash]$ git commit –m “Added foobar operation”
  

Blobs
=====
Blob stands for Binary Large Object. File versions are represented by blob. Blobs contain file data (no metadata).  Git database, it's tagged as SHA1 hash of the file. 

Trees
=====
Tree's are objects that represent a directory containing blobs and sub-directories. Tree's are binary files that contain
references to blobs and trees which are also named as SHA1 hash of the tree object.

Commits
=======
A Commit maintains the current state of the repository and named by SHA1 hash. You might consider a commit-object as a node within a linked list, where each commit object has a pointer to the parent commit object. For a given commit, you can traverse back by looking at the parent pointer to view the history of a commit. If a commit has multiple parent commits, then that particular commit has been created by merging two branches.

Branches
========
Branches are used to create an alternate development stream. By default, Git maintains a master branch (a.k.a trunk). Branches are typically created to control workflow (i.e. a new feature or update). Once the feature or update is completed, it's merged with the master (trunk) branch. Every branch is referenced by HEAD, which points to the current commit within the branch. When commits are submitted, the HEAD pointer is updated to reflect the latest commit.

Tags
====
Tag's are used to assign meaningful names within a specific version of a repository. Tags are similar to branches, but are immutable - which implies a branch with no modification. Once a tag is created for a particular commit, even if you create a new commit, it will not be updated. Tags are typically used for product releases.

Clone
=====
Clones create a repository instance. Clone operations check-out working copies, as well as mirror the repository locally. Users can then perform multple operations on the local repository, followed by a pull (synchronizing distributed/cloned repositories over a network).

Pull
====
Pull operations copy changes from a remote repository instance to a local instance. The pull operation is used for synchronization between two repository instances.

Push
====
Push operations copy changes from a local repository instance to a remote instance, and is used to persist changes in a Git repository. 

HEAD
====
HEAD is a pointer, which points to the latest commit within a branch. Whenever you make a commit, HEAD is updated with the latest commit and are stored in **.git/refs/heads/** directory.

.. code-block:: bash
  
  [NTNX CentOS]$ ls .git/refs/heads/
  master

  [NTNX CentOS]$ cat .git/refs/heads/master
  2348387fded58fa4deadbeef6c21344ceda0289

Revision
========
Revisions represent the version of the source code. Revisions in Git are triggered by commits identified by SHA1 secure hashes.

URL
===
URLs represent the Git repository location. Git URLs are stored in the config file.

.. code-block:: bash

  [NTNX CentOS foo_repo]$ pwd
  /home/foo/foo_repo

  [NTNX CentOS foo_repo]$ cat .git/config
  [core]
  repositoryformatversion = 1
  filemode = true
  bare = false
  logallrefupdates = true
  [remote "origin"]
  url = gituser@git.server.com:project.git
  fetch = +refs/heads/*:refs/remotes/origin/*
  
Workflows
**********
General workflows are as follows:

- You clone the Git repository as a working copy.
- You modify the working copy by adding/editing files.
- If necessary, you also update the working copy by taking other developer's changes.
- You review the changes before commit.
- You commit changes. If everything is fine, then you push the changes to the repository.
- After committing, if you realize something is wrong, then you correct the last commit	and push the changes to the repository.
- Shown below is the pictorial representation of the work-flow.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/git/image2.png
  
Setup Lab Environment
*********************

Create Guest VMs
================
Create 3x CentOS Guest VMs using the cluster's configured network.

**[Git Server]**

- VM Name: CentOS
- Image: CentOS QCOW2
- vCPU:2
- Cores/vCPU: 1
- Mem: 4GiB
- IP Address: 10.21.X.47

**[User yogi]**

- VM Name: CentOS
- Image: CentOS QCOW2
- vCPU:2
- Cores/vCPU: 1
- Mem: 4GiB
- IP Address: 10.21.X.48

**[User booboo]**

- VM Name: CentOS
- Image: CentOS QCOW2
- vCPU:2
- Cores/vCPU: 1
- Mem: 4GiB
- IP Address: 10.21.X.49

Install git
===========
Power up each VM.  With each of the Guest VM's powered up and booted to runlevel 5, ssh to each Guest VM as *root* and use *yum* to install git as follows:

.. code-block:: bash

  [root@CentOS]$ yum -y install git-core

Once Git installation has completed check the version:

.. code-block:: bash
    
  [root@CentOS]$ git --version
  git version 1.8.X.X
  [root@CentOS]$ 
  
Create the users for each Guest VM:

**Guest VM:** 10.21.X.47 (Git Server)

.. code-block:: bash

  [root@CentOS ~]# groupadd dev
  [root@CentOS ~]# useradd -G dev -d /home/gituser -m -s /bin/bash gituser
  [root@CentOS ~]# passwd gituser
   Changing password for user gituser.
   New password:        <--------------------------------- set to nutanix/4u
   Retype new password: <--------------------------------- set to nutanix/4u
   passwd: all authentication token updated successfully.
  [root@CentOS]# 

**Guest VM:** 10.21.X.48 (User Yogi)

.. code-block:: bash
    
  [root@CentOS]# adduser yogi
  [root@CentOS]# passwd yogi
   Changing password for user yogi.
   New password:        <--------------------------------- set to nutanix/4u
   Retype new password: <--------------------------------- set to nutanix/4u
   passwd: all authentication tokens updated successfully
   [root@CentOS]# logout
   
**Guest VM:** 10.21.X.49 (User booboo)

.. code-block:: bash
    
  [root@CentOS]# adduser booboo
  [root@CentOS]# passwd booboo
   Changing password for user booboo.
   New password:        <--------------------------------- set to nutanix/4u
   Retype new password: <--------------------------------- set to nutanix/4u
   passwd: all authentication tokens updated successfully
   [root@CentOS]# logout
   

Customize Git Environment
**************************
Git provides the git config tool, which allows you to set configuration variables. Git stores all global configurations in */home/<user>/.gitconfig* file, located in the users home directory. To set these configuration values as global, add the *--global* option.  

Login to each Guest VMs; *10.21.X.51* and *10.21.X.52* for the assigned users; *yogi* and *booboo* respectively and set the following git paramaters:

**Guest VM:** 10.21.X.48 (User Yogi)

.. code-block:: bash

  [yogi@CentOS]$ git config --global user.name "yogi bear"
  [yogi@CentOS]$ git config --global user.email "yogi@jellystone.com"
  [yogi@CentOS]$ git config --global branch.autosetuprebase always
  [yogi@CentOS]$ git config --global color.ui true
  [yogi@CentOS]$ git config --global color.status auto
  [yogi@CentOS]$ git config --global color.branch auto
  [yogi@CentOS]$ git config --global core.editor vim
  [yogi@CentOS]$ git config --global merge.tool vimdiff
  
  [yogi@CentOS project]$ git config --list
  user.name=yogi bear
  user.email=yogi@jellystone.com
  push.default=nothing
  branch.autosetuprebase=always
  color.ui=true
  color.status=auto
  color.branch=auto
  core.editor=vim
  merge.tool=vimdiff

**Guest VM:** 10.21.X.49 (User booboo)

.. code-block:: bash

  [booboo@CentOS]$ git config --global user.name "booboo bear"
  [booboo@CentOS]$ git config --global user.email "booboo@jellystone.com"
  [booboo@CentOS]$ git config --global branch.autosetuprebase always
  [booboo@CentOS]$ git config --global color.ui true
  [booboo@CentOS]$ git config --global color.status auto
  [booboo@CentOS]$ git config --global color.branch auto
  [booboo@CentOS]$ git config --global core.editor vim
  [booboo@CentOS]$ git config --global merge.tool vimdiff
  
  [booboo@CentOS project]$ git config --list
  user.name=booboo bear
  user.email=booboo@jellystone.com
  push.default=nothing
  branch.autosetuprebase=always
  color.ui=true
  color.status=auto
  color.branch=auto
  core.editor=vim
  merge.tool=vimdiff

  
Create Operation
*****************
In this section, we'll create a remote Git repository/Git Server for collaboration...
  
Create an empty Repository
==========================
We'll initialize a new repository by using *init* command followed by *--bare* option. This initializes the repository without a working directory. By convention, the bare repository must be named as *.git*.

*ssh* to the Git-Server: 10.21.X.47 as *gituser*.  Create/configure the repository as follows:

.. code-block:: bash

  [gituser@CentOS ~]$ pwd
  /home/gituser

  [gituser@CentOS ~]$ mkdir project.git
  [gituser@CentOS ~]$ cd project.git/
  [gituser@CentOS project.git]$ git --bare init
  Initialized empty Git repository in /home/gituser-m/project.git/

  [gituser@CentOS project.git]$ ls
  branches config description HEAD hooks info objects refs

Generate Public/Private RSA Key Pair
====================================
We'll step through the process of configuring ssh keys and add them to the Git-Server for each user.

*SSH* to each Guest VM for the assigned user (i.e. yogi:10.21.X.51 and booboo:10.21.X.52), create the users ssh-keys, and  push them to the Git-Server:

**Guest VM:** 10.21.X.48 (User Yogi)

.. code-block:: bash

  [yogi@CentOS ~]$ pwd
  /home/yogi
  [yogi@CentOS ~]$ ssh-keygen

  Generating public/private rsa key pair.
  Enter file in which to save the key (/home/yogi/.ssh/id_rsa): -----> Press Enter Only
  Created directory '/home/yogi/.ssh'.
  Enter passphrase (empty for no passphrase): -----------------------> Press Enter Only
  Enter same passphrase again: --------------------------------------> Press Enter Only
  Your identification has been saved in /home/yogi/.ssh/id_rsa.
  Your public key has been saved in /home/yogi/.ssh/id_rsa.pub.
  The key fingerprint is:
  df:93:8c:a1:b8:b7:67:69:3a:1f:65:e8:0e:e9:25:a1 yogi@CentOS
  The key's randomart image is:
  +--[ RSA 2048]----+
  | |
  | |
  | |
  |
  .
  |
  | Soo |
  | o*B. |
  | E = *.= |
  | oo==. . |
  | ..+Oo
  |
  +-----------------+
  
*ssh-keygen* has generated two keys, first one is private (i.e., id_rsa) and the second one is public (i.e., id_rsa.pub).

Add the public keys to the Git-Server:

.. code-block:: bash

  [yogi@CentOS ~]$ ssh-copy-id -i ~/.ssh/id_rsa.pub gituser@10.21.X.50

If/when prompted, provide the password for *gituser* and hit <enter> to complete the key installation.


**Guest VM:** 10.21.X.49 (User booboo)

.. code-block:: bash

  [booboo@CentOS ~]$ pwd
  /home/booboo
  [booboo@CentOS ~]$ ssh-keygen

  Generating public/private rsa key pair.
  Enter file in which to save the key (/home/booboo/.ssh/id_rsa): -----> Press Enter Only
  Created directory '/home/booboo/.ssh'.
  Enter passphrase (empty for no passphrase): -----------------------> Press Enter Only
  Enter same passphrase again: --------------------------------------> Press Enter Only
  Your identification has been saved in /home/booboo/.ssh/id_rsa.
  Your public key has been saved in /home/booboo/.ssh/id_rsa.pub.
  The key fingerprint is:
  df:93:8c:a1:b8:b7:67:69:3a:1f:65:e8:0e:e9:25:a1 booboo@CentOS
  The key's randomart image is:
  +--[ RSA 2048]----+
  | |
  | |
  | |
  |
  .
  |
  | Soo |
  | o*B. |
  | E = *.= |
  | oo==. . |
  | ..+Oo
  |
  +-----------------+
  
*ssh-keygen* has generated two keys, first one is private (i.e., id_rsa) and the second one is public (i.e., id_rsa.pub).

Add the public keys to the Git-Server:

.. code-block:: bash

  [booboo@CentOS ~]$ ssh-copy-id -i ~/.ssh/id_rsa.pub gituser@10.21.X.50

If/when prompted, provide the password for *gituser* and hit <enter> to complete the key installation.

**Note:** PRIVATE KEYs should never be shared with others.

Push Changes to the Repository
==============================
We've now created an empty repository on the Git-Server and allowed access for two users (yogi and booboo). From now on, yogi and booboo can push their changes to the repository by adding it as a remote.

Git init command creates .git directory to store metadata about the repository every time it reads the configuration from the .git/config file.

yogi creates a new directory, adds README file, and commits his change as initial commit. After commit, yogi verifies the commit message by running the git log command.

.. code-block:: bash

  [yogi@CentOS ~]$ pwd
  /home/yogi
  [yogi@CentOS ~]$ mkdir yogi_repo
  [yogi@CentOS ~]$ cd yogi_repo/
  [yogi@CentOS yogi_repo]$ git init
  Initialized empty Git repository in /home/yogi/yogi_repo/.git/

  [yogi@CentOS yogi_repo]$ echo 'TODO: Add contents for README' > README
  [yogi@CentOS yogi_repo]$ git status -s
  ?? README
  
  [yogi@CentOS yogi_repo]$ git add .
  [yogi@CentOS yogi_repo]$ git status -s
  A README
  [yogi@CentOS yogi_repo]$ git commit -m 'Initial commit'

  [master (root-commit) 19ae206] Initial commit
  1 files changed, 1 insertions(+), 0 deletions(-)
  create mode 100644 README
  
yogi checks the log message by executing the git log command.

.. code-block:: bash

  [yogi@CentOS yogi_repo]$ git log

  commit 19ae20683fc460db7d127cf201a1429523b0e319
  Author: Yogi Bear <yogi@jellystone.com>
  Date: Wed Feb 11 07:32:56 2018 +0530

  Initial commit
  
Yogi committed his changes to the local repository. Now, it’s time to push the changes to the remote repository. But before that, we have to add the repository as a remote, this is a one-time operation. After this, yogi can safely push the changes to the remote repository.

.. note:: By default, Git pushes only to matching branches: For every branch that exists on the local side, the remote side is updated if a branch with the same name already exists there. In our tutorials, every time we push changes to the origin master branch, use appropriate branch name according to your requirement.

.. code-block:: bash

  [yogi@CentOS yogi_repo]$ git remote add origin gituser@git.server.com:project.git
  [yogi@CentOS yogi_repo]$ git push origin master
  
The above command will produce the following result.

.. code-block:: bash

  Counting objects: 3, done.
  Writing objects: 100% (3/3), 242 bytes, done.
  Total 3 (delta 0), reused 0 (delta 0)
  To gituser@git.server.com:project.git
  * [new branch]
  master −> master
  
Now, the changes are successfully committed to the remote repository.

Clone Operation
****************
We now have a bare repository on the Git server and yogi pushed his first version. Now, booboo can view his changes. The Clone operation creates an instance of the remote repository.


With booboo logged into his server (10.21.X.49), he creates a new directory *booboo_repo* in his */home* directory and performs the *clone* operation.

.. code-block:: bash

  [booboo@CentOS ~]$ mkdir booboo_repo
  [booboo@CentOS ~]$ cd booboo_repo/
  [booboo@CentOS booboo_repo]$ git clone gituser@10.21.X.47:project.git
  
The above command will produce the following result.

.. code-block:: bash

  Initialized empty Git repository in /home/booboo/booboo_repo/project/.git/
  remote: Counting objects: 3, done.
  Receiving objects: 100% (3/3), 241 bytes, done.
  remote: Total 3 (delta 0), reused 0 (delta 0)

booboo changes the directory to new local repository and lists its directory contents.

.. code-block:: bash

  [booboo@CentOS booboo_repo]$ cd project/
  [booboo@CentOS project]$ ls
   README


Add Operation
**************
booboo has successfully cloned the repository and decides to add a file. So he creates file booboo.md using his favorite editor.  The contents for file booboo.md is as follows:

.. code-block:: bash

  #booboo is smarter than the average bear.

booboo saves the file and now he can safely add it to the repository.

The Git *add* operation adds the file to the staging area.

.. code-block:: bash

  [booboo@CentOS project]$ git status -s
  ?? booboo
  ?? booboo.md

  [booboo@CentOS project]$ git add booboo.md
  
Git is showing question marks (??) before the file names because these files are not a part of Git, and Git does not know what to do with them. 

booboo has added the file to the staging area - git *status* command will show files present in the staging area.

.. code-block:: bash

  [booboo@CentOS project]$ git status -s
  A booboo.md

To commit the changes, booboo will use the git *commit* command followed by –m option. If we omit –m option. Git will open a text editor where we can write multiline commit message.

.. code-block:: bash

  [booboo@CentOS project]$ git commit -m 'Publish booboo's file'

The above command will produce the following result:

.. code-block:: bash

  [master cbe1249] Implemented my_strlen function
  1 files changed, 24 insertions(+), 0 deletions(-)
  create mode 100644 string.c

After commit, booboo can view log details, by executing the git log command. It will display the information of all the commits with their commit ID, commit author, commit date and SHA-1 hash of commit.

.. code-block:: bash

  [booboo@CentOS project]$ git log

The above command will produce the following result:

.. code-block:: bash

  commit cbe1249b140dad24b2c35b15cc7e26a6f02d2277
  Author: booboo bear <booboo@jellystone.com>
  Date: Wed Feb 11 08:05:26 2018 +0530

  Publish booboo's file


  commit 19ae20683fc460db7d127cf201a1429523b0e319
  Author: yogi bear <yogi@jellystone.com>
  Date: Wed Feb 11 07:32:56 2018 +0530

  Initial commit
  

Push Operation
***************
booboo added a new file to the repository and commited has updates/changes and is ready to push operation. The Push operation stores data permanently to the Git repository allowing other project team members to see booboo's changes.

Prior to pushing his changes he wants to view the updates by:

1. Viewing the log history:

.. code-block:: bash

  [booboo@CentOS project]$ git log
  commit d1e19d316224cddc437e3ed34ec3c931ad803958
  Author: booboo bear <booboo@jellystone.com>
  Date: Wed Feb 11 08:05:26 2018 +0530
  
2. Viewing the contents:

.. code-block:: bash

  [booboo@CentOS project]$ git show d1e19d316224cddc437e3ed34ec3c931ad803958
  commit d1e19d316224cddc437e3ed34ec3c931ad803958
  Author: booboo bear <booboo@jellystone.com>
  Date:   Wed Feb 11 17:55:13 2018 -0800

    Publish booboo's file

  diff --git a/booboo.md b/booboo.md
  new file mode 100644
  index 0000000..4c861b1
  --- /dev/null
  +++ b/booboo.md
  @@ -0,0 +1 @@
  +#booboo is smarter than the average bear.

  
3. Push the changes.

.. code-block:: bash

  [booboo@CentOS project]$ git push origin master
  Counting objects: 4, done.
  Compressing objects: 100% (3/3), done.
  Writing objects: 100% (3/3), 517 bytes, done.
  Total 3 (delta 0), reused 0 (delta 0)
  To gituser@git.server.com:project.git
  19ae206..d1e19d3 master −> master

booboo's changes have been successfully pushed to the repository; now other team members can view his changes by performing clone or update operation.

Update Operation
****************
Logged in as *yogi* on host:10.21.X.51 execute the *clone* operation from the */home/yogi/* directory.

.. code-block:: bash

  [yogi@CentOS ~]$ git clone gituser@10.21.X.50:project.git
  Cloning into 'project'...
  remote: Counting objects: 6, done.
  remote: Compressing objects: 100% (3/3), done.
  remote: Total 6 (delta 0), reused 0 (delta 0)
  Receiving objects: 100% (6/6), done.

Change directories to the new/updated *project* folder and execute *ls*

.. code-block:: bash 

  [yogi@CentOS project]$ cd project
  [yogi@CentOS project]$ ls
  booboo.md  README
  [yogi@CentOS project]$ 

Execute the git *log* command:

.. code-block:: bash 

  [yogi@CentOS project]$ git log
  commit 4c6f875bae08459055de8b8301d2dd52f2190c6b
  Author: booboo bear <booboo@jellystone.com>
  Date:   Sun Feb 25 17:55:13 2018 -0800

      Publish booboo's file

  commit aacd700320437b39e11e83dac2e5dd154fd38bdd
  Author: yogi bear <yogi@jellystone.com>
  Date:   Sun Feb 25 16:59:57 2018 -0800

      initial commit

yogi can now see the changes made earlier by booboo.

Modify/replace the contents in the file *booboo.md* with *#yogi is smarter than the average bear.*  using *vi* and save the contents.

Execute a diff on the repository.  It should appear similar as follows:

.. code-block:: bash

  [yogi@CentOS project]$ git diff
  diff --git a/booboo.md b/booboo.md
  index 4c861b1..528c627 100644
  --- a/booboo.md
  +++ b/booboo.md
  @@ -1 +1 @@
  -#booboo is smarter than the average bear.
  +#yogi is smarter than the average bear.

View the status of the repository to show the file was modified (M):

.. code-block:: bash

  [yogi@CentOS ~]$ git status -s
  M booboo.md
  
Add the modified file, *commit* the changes, and check the git-log for status.

.. code-block:: bash

  [yogi@CentOS project]$ git add booboo.md
  [yogi@CetnOS project]$ git commit -m 'Corrected: Yogi is the smartest bear'
  [master 109328f] Corrected: Yogi is the smartest bear
   1 file changed, 1 insertion(+), 1 deletion(-)
   
  [yogi@CentOS project]$ git log
  commit 109328f78754dd98c6a98e0c356082dccce25186
  Author: yogi bear <yogi@jellystone.com>
  Date:   Sun Feb 25 19:50:27 2018 -0800

      Corrected: Yogi is the smartest bear

  commit 4c6f875bae08459055de8b8301d2dd52f2190c6b
  Author: booboo bear <booboo@jellystone.com>
  Date:   Sun Feb 25 17:55:13 2018 -0800

      Publish booboo's file

  commit aacd700320437b39e11e83dac2e5dd154fd38bdd
  Author: yogi bear <yogi@jellystone.com>
  Date:   Sun Feb 25 16:59:57 2018 -0800

      initial commit
      
And finally push it to the branch:

.. code-block:: bash

  [yogi@CentOS project]$ git push origin master
  Counting objects: 5, done.
  Delta compression using up to 2 threads.
  Compressing objects: 100% (2/2), done.
  Writing objects: 100% (3/3), 327 bytes | 0 bytes/s, done.
  Total 3 (delta 0), reused 0 (delta 0)
  To gituser@10.68.69.52:project.git
     4c6f875..109328f  master -> master
     
     
Takeaways
**********
- Learned how to configure a distributed **Git** development environment with repository.
- Stepped through a common workflow of creating, adding, and modifying files using **Git** command line operations.
- Successfully managed a central **Git** repository shared across the network...
- Learned how newly created files are staged prior to persisting within a repository.
- Observed how to check change-log status and change history...
 
