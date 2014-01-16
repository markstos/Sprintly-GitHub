Sprintly-GitHub
===============

[Sprint.ly](http://sprint.ly/ 'Sprint.ly') is a great tool for managing work 
items; [GitHub](http://github.com 'GitHub') is a great tool for managing your 
code. The tools in this repository will help you take advantage of the 
integration with GitHub that Sprint.ly offers without drawing your attention 
away from the terminal.

Each git repository can be associated with a Sprint.ly product. Use the 
`sprintly` command line tool to get a list of all items assigned to you for the 
current product, or `sprintly --all` to get all items in all projects.

Install the `commit-msg` hook to facilitate Sprint.ly's GitHub integration.

How to use `sprintly`
---------------------

See `sprintly --help` for basic usage instructions.

	usage: sprintly [-h] [--all] [--install-hook] [--uninstall-hook]

	optional arguments:
	  -h, --help        show this help message and exit
	  --all, -a         show items for all products
	  --install-hook    install commit-msg hook in current git repository
	  --uninstall-hook  uninstall commit-msg hook in current git repository

Installing `sprintly`
---------------------

Clone this repository, then run the setup script.

	git clone https://github.com/tremby/Sprintly-GitHub.git
	cd Sprintly-GitHub
	sudo python setup.py install

Configuring `sprintly`
----------------------

The git configuration keys `sprintly.user` and `sprintly.key` are expected to 
exist, with the Sprint.ly email address and API key respectively. They can be 
set in the repository-specific or global Git config file by hand or with the 
`git-config` tool. For example:

	$ git config sprintly.user user@example.com
	$ git config sprintly.key abc123abc123

or, globally,

	$ git config --global sprintly.user user@example.com
	$ git config --global sprintly.key abc123abc123

Once these are set, running `sprintly` from anywhere but a Git repository will 
give a list of all items for this user.

Running `sprintly` in a directory which is part of a Git repository requires a 
further configuration key, `sprintly.product` to exist. This could be set 
globally but you'll likely want it set for each separate repository. If it is 
not set, the script will fetch products from the Sprint.ly API and offer a 
choice, then save this in the Git repository configuration for you.

*Note: (1) your Sprint.ly API Key can be found 
[here](https://sprint.ly/account/profile/). (2) you will only be asked to enter 
a sprint.ly product id if you have more than one product.*

GitHub Integration: Installing the `commit-msg` hook
----------------------------------------------------

The `sprintly` tool can install the hook for you. Navigate to a git repository and run:

	$ sprintly --install-hook
	Creating symlink...
	Hook was installed at <repository>/.git/hooks/commit-msg

**Important: you MUST install this manually in every git repository. This is a 
limitation of the way git implements hooks. Don't blame me!**

Tip: make sure that your git `user.email` matches your Sprint.ly username, or 
the hook won't work. If this happens, you will see the following message:

	$ sprintly --install-hook
	Creating symlink...
	Hook was installed at <repository>/.git/hooks/commit-msg
	WARNING: Your git email (user@site.org) does not match your sprint.ly username (user@company.com)
	WARNING: Don't worry - there is an easy fix. Simply run one of the following:
		'git config --global user.email user@company.com' (all repos)
		'git config user.email user@company.com' (this repo only)

*Note: the hook installed is actually a symbolic link to a shared copy of the 
hook found wherever Python setuptools decided to put it. By doing this, the hook 
can be easily updated for all users and all repositories by installing a new 
version of Sprintly-GitHub.*

###Uninstalling the `commit-msg` hook

The `sprintly` tool can uninstall the hook for you as well. Navigate to the git 
repository in question and run:

	$ sprintly --uninstall-hook
	Hook has been uninstalled.

Changing the Configuration
--------------------------

If at some point you wish to change your configuration (you get a new username 
or API key) just use `git-config` again to update whatever is necessary or edit 
the configuration files by hand. To choose the Sprint.ly product from the list 
again you can just remove the `sprintly.product` key with `git config --unset 
sprintly.product`. See `man git-config` for full details.

Sample Output
-------------

Let's run through a few examples of how to take advantage of this tool.

Type `sprintly` at a command prompt to see a list of your current Sprint.ly 
items:

	Product: Example Company (https://sprint.ly/product/#/)
		#1: As a developer, I want to open source our Sprintly-Github tools so that...
			#5: Publish it.
			#4: Write README
		#2: As a developer, I want a better set of unit tests so that changes to our...
			#3: Add tests to widget creation page

With the `commit-msg` hook installed, whenever a commit is made and pushed, a 
comment will be automatically published on the corresponding Sprint.ly item. 
Head to Sprint.ly for more 
[details](http://support.sprint.ly/kb/integration/available-scmvcs-commands 
'Sprint.ly SCM/VCS Commands').

	$ git commit -m "Normal commit message here."
	Product: Example Company (https://sprint.ly/product/#/)
			#1: As a developer, I want to open source our Sprintly-Github tools so that...
				#5: Publish it.
				#4: Write README
			#2: As a developer, I want a better set of unit tests so that changes to our...
				#3: Add tests to widget creation page
	#0 - Proceed without Sprint.ly item number.
	Enter 1 or more item numbers separated by a space: 4
	[master 1e71283] References #4. Normal commit message here.
	 1 files changed, 1 insertions(+), 1 deletions(-)

To save time, include an item number at the beginning of your commit to 
automatically reference that item. You won't be prompted to select an item 
number if you go this route:

	$ git commit -m "#42 Adding README"
	[master 555a912] References #42 Adding README
	 0 files changed, 0 insertions(+), 0 deletions(-)
	 create mode 100644 README

Or include Sprint.ly keywords followed by an item number anywhere in your 
message. Again, you won't be prompted to select an item number if you go this 
route:

	$ git commit -m "Adding some samples. Closes #42. Refs #54."
	[master bfb7a8b] Adding some samples. Closes #42. Refs #54.
	 0 files changed, 0 insertions(+), 0 deletions(-)
	 create mode 100644 sample

vim: ts=4 sts=4 sw=4 noet tw=80 fo=crqwnlt
