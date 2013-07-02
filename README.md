Overview
--------

`servers` is a script which leverages the content of *~/.ssh/config* file and prints *HostName* values corresponding to *Host* entries matching the patterns passed as arguments.

For example, listing all *test* servers can be accomplished as follows:

	$ servers test

	s007.company.com
	s008.company.com
	s009.company.com

given a sample [*~/.ssh/config file*](examples/sample_config):

	...
	Host app1-pro-db-s006
	HostName s006.company.com

	# App1 TEST

	Host app1-test-web-s007
	HostName s007.company.com

	Host app1-test-app-s008
	HostName s008.company.com
	...


Chaining this command with other commands gives interesting results. Checking the disk space on all the *test* servers above can be as simple as:

	$ servers test | xargs -n1 -i ssh {} "hostname; df"

Another example could be to download all the log files for application *app1* into a set of directories named after the source host names:

	$ for s in $(servers app1); do mkdir $s; scp "$s:/var/log/app1/*" $s; done


Installation and Usage
----------------------

To install it, download and place `servers` script somewhere in your *$PATH*.

With no arguments `servers` will print all the host names from *~/.ssh/config* file. To get the full command syntax use *-h* argument:

	$ servers -h


Host Naming Conventions
-----------------------

`servers` uses the host names matching symbolic *Host* values in *~/.ssh/config* file. Using a naming convention allows to classify the corresponding host names to later select them based on this classification. The naming convention used in the [example data file](examples/sample_config) is as follows:

	{Application Name}-{Lifecycle: pro, test, dev}-{Role: web, app, db}-{Base hostname}

Based on the above you can select hosts for given application, lifecycle, role, or a combination of those:

	$ servers app2
	$ servers pro
	$ servers web
	$ servers "app2.*web"

In the last example a regular expression was used to select hosts based on a combination of criteria.

