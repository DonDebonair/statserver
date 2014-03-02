# Statserver

Because I wanted to become a responsible developer, and have a complete measuring and monitoring solution for my own projects, I decided to cook up a _Playbook_ in Ansible, that installs the following tools and its dependencies:

* StatsD
* Graphite
* Sentry

This provides you with the capabilities to measure anything you want in your application and otherwise, collect it with StatsD and store and visualize it with Graphite. As an added bonus, [Sentry](https://getsentry.com) provides you with a way to collect and monitor the exceptions that occur in your applications.

Note that this installs the aforementioned tools with a production-ready configuration. So no SQLite and silly portnumbers.

For a write-up on why and how I created this, have a look here: http://dandydev.net/blog/statserver-setting-up-monitoring-and-statistics-with-ansible

For a guide on how to do meaningful measurements in your applications, have a look here: http://matt.aimonetti.net/posts/2013/06/26/practical-guide-to-graphite-monitoring/

## Components

*  PostgreSQL database
*  Nginx webserver/reverse proxy
*  Python, Pip & VirtualEnv
*  NodeJS
*  Memcached
*  The 3 core Graphite components:
	* [Carbon](https://github.com/graphite-project/carbon)
	* [Whisper](https://github.com/graphite-project/whisper)
	* [The Graphite webapp](https://github.com/graphite-project/graphite-web)
* StatsD
* The latest stable version of **Sentry** and all of its Python dependencies 

## Installation instructions

If you want to install this on a real server, you'll only need Ansible. Instructions for installing Ansible, [can be found on their website](http://docs.ansible.com/intro_installation.html), but I'll sum up here how I'd recommend installing Ansible:

* **Linux**: Use [PIP](http://www.pip-installer.org/) to install Ansible by running `sudo pip install ansible`.
* **OS X**: Install Ansible with Homebrew by running `brew install ansible`. Installation using Homebrew PIP (you're not using OS X's default Python, are you?) doesn't work [because of hardcoded paths](https://github.com/Homebrew/homebrew/pull/21602#issue-17540275). 
* **Windows**: Please get yourself a proper development machine. (Windows [isn't supported](http://docs.ansible.com/intro_installation.html#control-machine-requirements))

If you want to try Statserver out locally, on a virtual machine, you'll need [Vagrant](http://www.vagrantup.com/) and a Virtual Machine provider of choice ([VirtualBox](https://www.virtualbox.org/) is free and works out of the box with Vagrant).

After meeting the requirements, clone the repo:

```bash
$ git clone https://github.com/DandyDev/statserver
$ cd /path/to/statserver
```
You should now edit the `statserver.yml` file and modify a few variables:

* `graphite > server`: The hostname of the URL on which you want to access Graphite. For running a local VM, _graphite.local_ is fine. In production, this could be something like _graphite.mystatserver.com_.
* `db_graphite > password`: a secure password for the Graphite database
* `db_sentry > password`: a secure password for the Sentry database
* `superuser_sentry > email, username, password`: details of the Sentry superuser account that will automatically be created
* `sentry > server`: The hostname of the URL on which you want to access Sentry. For running a local VM, _sentry.local_ is fine. In production, this could be something like _sentry.mystatserver.com_.
* `sentry > url`: This should be set to the value you set for the `sentry > server` variable, prefixed with the desired protocol (_http_ or _https_)

Now, for creating a VM locally to try this out, you can do the following:

```bash
$ echo "graphite.local" >> /etc/hosts
$ echo "sentry.local" >> /etc/hosts
$ vagrant up
```

For provisioning a remote server with Sentry, StatsD and Graphite, do the following:

```bash
# replace mystatserver.com with the domainname or IP address on which your server can be reached.
$ echo "mystatserver.com" >> myinventory 
$ ansible-playbook statserver.yml -i myinventory
```

Within 5 - 10 minutes or so, you should be able to access Graphite and Sentry on the specified url's.

If you're happy with the results, and want something to measure, you can start by adding [Diamond](https://github.com/BrightcoveOS/Diamond) to all your servers. This will send all kinds of nifty system metrics to your statserver. Be sure to configure it to send its metrics to StatsD instead of Graphite, for less overhead.

## Different OSes

By default, the Vagrant box runs Ubuntu 12.04, but the playbook supports Debian 7 and CentOS 6.4 as well! To try those out locally, uncomment the appropriate lines in the Vagrantfile and comment out the Debian lines. For remote servers, you don't have to change anything.


## Superuser for Graphite

To create a superuser for Graphite, log in as root, or turn to root on your server, and issue the following command:

```
export PYTHONPATH=/opt/graphite/webapp/; /opt/graphite/bin/python /opt/graphite/bin/django-admin.py createsuperuser --settings=graphite.settings
```

## Known issues / TODO

* This hasn't been tested on other Providers than VirtualBox yet
* On CentOS, the firewall is completely closed by default (at least on the box I tried it with). So you have to manually open the relevant port (80) using `iptables`, or if you're not concerned about security (because you're running it locally through Vagrant), you can always flush the firewall with `iptables -F`.

## Contribute

If you have any suggestions, feel free to create an issue here on Github and/or fork this repo, make changes and submit a pull request!
