# git Usage

The local git repos have been established to make keeping documentation and other shared resources up to date across the entire user base. The usage of git in this instance need not employ any of the advanced version management features. A summary of the commands useful in this environment are below.

## Cloning Repos

This is the first step in working with a repo. You get the address for a repo, and then clone it to your local machine. Note that this clone should live in the home folder of the user cloning the repo.

1. Log into the Gogs server via a web browser: `http://<gitserveraddress.domain>:8080/`
1. Browse to the repo you wish to clone. On the main repo page, on the right side of the page will be a text field with an `HTTP` and `SSH` building next to it. Click **HTTP** and copy the URI in the text field.
1. In the terminal on the local machine, navigate to parent folder where you would like the repo to reside  
    **Ex:** `$ cd /home/username/workspace/git/`
1. Clone the repo into the folder using the `git clone` command and the URI copied in the earlier step  
    `$ git clone http://<gitserveraddress.domain>:8080/<owner_username>/<repo_name>.git`  
    ***Note:*** *If it's not already there, you'll need to add the `:8080` in the URI.*
1. Once the folder has been cloned you can navigate to the folder, and view/edit the files as desired in your text editor (e.g., gedit)

## Updating Repos

If you add to or update a repo, you must **commit** your changes (including a **message** about what you did, and then **push** the repo back to the git server). You can commit as many times as you like before pushing to the server. In all cases, before making any changes to the contents of a repo, you should **pull** any changes that have been made by other users to the repo in the meantime.

### Changing Existing Files

1. Navigate to the repo folder  
    `$ cd /home/username/workspace/git/repo_name`
1. Pull new changes  
    `$ git pull`
1. Make any changes to the files in the repo you desire. If you are reorganizing the structure/organization of the file/folders in the repo, see below for more information.
1. Once changes are complete, commit them to the repo. You must enter a commit message that should include a short note about what you've done.  
    `$ git commit -am "Updated the TOC and corrected formatting in FreeIPA walkthrough"`  
    ***Note:*** *The `-a` flag tells git to commit* ***all*** *changes you've made. The `-m` flag (here combined with the `-a` flag for simplicity) allows you to set the commit message as part of the commit command. If left off, and no message is included, you'll be redirected to the default editor in the terminal (e.g., `vi` or `nano`) to edit/save your message.*
1. Push the changes to the server  
    `$ git push`  
    ***Note:*** *You will need to authenticate to the Gogs server when pushing changes. To avoid this, see the Advanced Usage section below for instructions on setting up SSH keys.*

### Adding a New File(s)

If you add one or more new files, you need to let the repo know to track them. This is done with the `git add` command, and must be done before the `git commit` command in the sequence above.

1. `$ git add *`  
    ***Note:*** *You can add files to the folder and not have the repo track them. If you do this, replace the* `*` *above with only the name(s) of the file(s) you wish to add. In general, having untracked files in the repo folder can get confusing, and should probably be avoided.*

### Reorganizing/Moving/Deleting Files

If you delete or move a file in the repo folder, git will not know about the change. This becomes problematic for several reasons, so to avoid trouble, make sure that git knows about the changes by prefacing your commands with `git`. Make these changes before your `commit`/`push` commands.

* Moving files  
    `$ git mv /path/to/filename /new/path/to/filename`
* Delete files  
    `$ git rm filename`

# (Sightly) Advanced Usage

To speed up the push/pull process, as well as offer better security, you can add an SSH key to your Gogs user account. To do this, you'll want to generate an SSH key pair (if you don't already have one [this key pair can be added to your FreeIPA account to enable passwordless login to various servers]).

## Generate an SSH Key Pair (Optional)

1. In a terminal, generate the key pair using `ssh-keygen`. The `-t` flag specifies the key type (we're using RSA), `-b` the bit depth (2048), and `-f` the path/filename of our key pair.  
    `$ ssh-keygen -t rsa -b 2048 -f ~/.ssh/mykeyname`
1. When prompted for a passphrase, press enter to have no passphrase.
1. The entire process should look like this:  

```
$ ssh-keygen -t rsa -b 2048 -f ~/.ssh/mykeyname
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/jhaefele/.ssh/mykeyname.
Your public key has been saved in /Users/jhaefele/.ssh/mykeyname.pub.
The key fingerprint is:
3b:11:8c:14:48:6e:71:63:83:ce:98:f8:49:86:9d:bd jhaefele@jhaefele.local
The key's randomart image is:
+--[ RSA 2048]----+
|   .ooB.         |
|   .o= =         |
| + Bo . o        |
|o B.+    .       |
| + . .  S        |
|  o E    o       |
|        o        |
|         .       |
|                 |
+-----------------+
```

## Add Private Key to `ssh-agent`

ssh-agent makes your private key readily available to the OS, and also passes it along with your user profile when you log into a remote server. The command below can be added to your `.bashrc` or `.bash_profile` to make this available each time you log in.

1. `$ ssh-add ~/.ssh/mykeyname`

## Add your public key to Gogs

1. Copy the contents of your `mykeyname.pub` file. **Be sure to use the `.pub` version!**
1. In the Gogs web interface, click the drop-down arrow next to your avatar in the upper-right corner, and select **Your Settings**
1. In the menu on the left, select **SSH Keys**
1. Click the **Add Key** button
1. Supply a name for the key. It may or may not match the name of the actual key file.
1. Paste the public key contents (copied above) into the *Content* field, and click **Add Key**

Once you have a key pair between your local machine and Gogs, you can use the **SSH** URI when cloning the repo. From then on, as long as you have a matching private key available in terminal, you will not need to authenticate every time you push to the server.