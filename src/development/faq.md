# Frequently Asked Questions (FAQ)

## What is the difference between a Platform, a Project and an Environment?

Platform or Platform.sh is the infrastructure which is running all your projects.

A project is the site that you're working on. Each project can contain multiple applications and be deployed in environments.

An environment is a standalone copy of your live site (the Master environment) which can be used for testing, implementing new features...

## How can I cancel my subscription?

If you want to delete your project and cancel your subscription, simply go to your user profile and click on "Edit plan" on the project you want to delete. Then you can click on the link: "delete your Platform.sh plan".

This will delete your project and stop invoicing for this project. If you have multiple projects, your subscription will continue until you don't have any projects left.

## Can I skip a build in Git push?

Yes, use the followings and your Git push will skip the build and deploy process.

```
GIT_SSH_COMMAND='ssh -o "SendEnv PLATFORMSH_PUSH_NO_WAIT"' PLATFORMSH_PUSH_NO_WAIT=1 git push
```

## How can I export my data?

If you have file mounts, you can keep a copy of the files using `rsync` like below.

```
rsync -r [SSH-URL]:/app/private/ .
```

Data stored in database can be copied to you local file system via [Platform.sh CLI](https://docs.platform.sh/user_guide/overview/cli/index.html).

```
platform sql-dump
```

Regarding the data in Solr, if you have to make a copy, you have to use `platform tunnel` from [Platform.sh CLI](https://docs.platform.sh/user_guide/overview/cli/index.html) and then run [Solr replication](http://wiki.apache.org/solr/SolrReplication) moving your data to local box. Though, if the data are just the search index of your site, you can always rebuild that from scratch easily.

## Do you support MySQL?

[Platform.sh](https://platform.sh) uses MariaDB to manage and store your databases. It's a fork of MySQL which is more stable and has more interesting features.

## Do you support other database services?

We support PostgreSQL, Elasticsearch, Redis and MongoDB Out of the box. Batteries included.No need for add-ons. We aim to support all the most popular database services...

## Does branching an environment duplicate services?

Yes ! Branching an environment creates an exact copy (snapshot) of the parent environment, containing the files, the database, the services...

##Do you have a local writable file-system?

Yes! Unlike other PaaS providers Platform.sh supports non-ephemeral
storage. When you configure your application you can tell us what directories
you want to be read/write (these are called mounts). These will be mounted
on a distributed file system (which is transparent for you). When you back-up
your environment they will be backed up as well. When you create a new staging
environment... these will be cloned with the rest of your data.

##So, why do I get an error with "Read-only file system" when I try to manually modify files on Platform.sh?
Everything except the mounts you declared as Read/Write will be read-only. Read/Write
is there for your data, for your uploads, for logs and temporary files. Not for
your code. In order to change code on Platform.sh you have to go through Git.

This is what gives you all of the benefits of having repeatable deployments,
consistent backups, traceability, magic creation of staging/dev environments.

In Platform.sh you can not just "hack production". It is a constraint, but it
is a good constraint.

During the build phase of your application everything is still read/write so
you can while deploying do whatever you want (compile stuff or generate
anything you need on the storage). But once deployed the main file-system
will turn read-only.

## Do you add custom HTTP headers?

We add the following custom HTTP headers to give the application information about the connection.

* "X-Forwarded-Proto": The protocol forwarded to the application, e.g. "http", "https".
* "X-Client-IP": The remote IP address of the request.
* "X-Client-SSL": Set "on" only if the client is using SSL connection, otherwise the header is not added.
* "X-Original-Route": The route in `.platform/routes.yaml` which is used currently, e.g. "http://{default}/".

## How do I change timezone settings?

We do not allow changing the system timezone of your application, though you can change the timezone in your services.

* PHP runtime - You can change the timezone by providing a [custom php.ini](https://docs.platform.sh/user_guide/reference/toolstacks/php/configure-php.html#custom-php-ini).
* Node.js runtime - You can change the timezone by starting the server with `env TZ='<timezone>' node server.js`.
* Python runtime - You can change the timezone by starting the server with `env TZ='<timezone>' python server.py`.
* MySQL - You can change the per-connection timezone by running SQL `SET time_zone = <timezone>;`.
* PostgreSQL - You can change the timezone of current session by running SQL `SET TIME ZONE <timezone>;`.

You may also refer to the list of [supported timezones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).

## I am using Drupal and  I see "File not found" why?

If you see the "File not found" when accessing your site within a browser, this means that you’ve pushed your code as a vanilla project but no *index.php* has been found.

Make sure your repository contains an *index.php* file as the root, or that your make files are properly named.

## What happens if I push a local branch to my project?

If you push a local branch that you created with Git, you create what we call an `inactive environment`, ie. an environment that is not deployed.

This means there won't be any services attached to this branch.

You are able to convert an `inactive environment` into an `active environment` and vice versa back from the environment configuration page or using the CLI.

## How does Master  (the live site) scale?

Master gets all the resources that are divided into each service (PHP 40%, MySQL 30%, Redis 10%, Solr 20%…). Each Development environment gets the Development plan resources.

## What exactly am I sshing into?

You're logged in to the PHP service. It's a read-only file system.

## Can I edit a quick fix on a Platform environment without triggering a rebuild?

No ! Since the PHP service you access via SSH is a read-only file system, you'll have to push your fix to be able to test it.

## What do I see when I push code?

We try to make the log as self-explanatory as possible, so you should see the Git output and also output from the drush make...

You can also find it back by clicking on the status of the activity in the `Platform UI`.

## What Linux distribution is Platform.sh using?

Platform.sh is built on Debian.

## If I choose the Development plan, can I use that plan for production?

The Development plan provides all the tools to build your website. You can create as many development profiles as you wish for yourself and for your team.
Once your project is built and ready for production, you can choose another plan to go live. These plans are listed on the [pricing page](<https://platform.sh/pricing/).

## Can I please use tabs in my YAML files?

No.

## I am getting weird errors when I push (something with paramiko..)

Please validate the syntax of your YAML file. Don't use tabs. And if all fails, contact support.

## Which geographic zones does Platform.sh cover?

Platform leverages the powerful AWS Infrastructure.
We can deploy your site in a  [datacenter](https://aws.amazon.com/about-aws/globalinfrastructure/regional-product-services/) that is very close to your target audience.

## Why did you choose the .sh extension for your domain?

'sh' is the short version of shell.
According to Wikipedia™, in computing, a [shell](http://en.wikipedia.org/wiki/Shell_(computing)) is a user interface  for access
to an operating system's services. Generally, operating system shells use either a [command-line interface ](http://en.wikipedia.org/wiki/Command-line_interface) (CLI) or
[graphical user interface](http://en.wikipedia.org/wiki/Graphical_user_interface) (GUI).
This is exactly what Platform.sh is about: Giving developers tools to build, test, deploy,
and run great websites!

".sh" is also the TLD for Saint Helena that looks like a lovely island, and whose motto is:
"Loyal and Unshakeable" which we also strive to be.


## IDE Specific Tips

MAMP pro:

In order for MAMP to work well with the symlinks created by the
[Platform.sh CLI](https://github.com/platformsh/platformsh-cli), add the
following to the section under Hosts \> Advanced called “Customized
virtual host general settings.” For more details visit [MAMP Pro
documentation
page](http://documentation.mamp.info/en/documentation/mamp/).

```bash
<Directory>
        Options FollowSymLinks
        AllowOverride All
</Directory>
```

> [Laravel Forum
> Archives](http://forumsarchive.laravel.io/viewtopic.php?pid=11232#p11232)

> **note**

> When you specify your document root, MAMP will follow the symlink and
> substitute the actual build folder path. This means that when you
> rebuild your project locally, you will need to repoint the docroot to
> the symlink again so it will refresh the build path.
