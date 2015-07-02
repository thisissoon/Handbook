# SOON_ Developer Handbook
We love our dorks, we want them to be happy developers, they make things happen. If you currently do work for us or are thinking of doing so here is how we work.

## Tech, everywhere and anywhere
We aren't one of those places where the tech guru’s are locked away in a padded cell in the darkest depths of corporate HQ, they are the stars and we involve them at every stage of a build, nothing is siloed.

We want to foster a culture where each developer can grow and contribute to the company as a whole, our process, our technology stack and so on. Want a book to learn a new language? Maybe go on a course to get better at something else? We can help with that.

## Style Guides
 * [Python](styleguides/python.md)
 * [CSS](styleguides/css.md)
 * [JavaScript](styleguides/js.md)

## Your Editor
Your development environment is something that is very personal to you and how you work so we will never enforce a specific editor or terminal emulator or operating system upon you (but VIM rocks), so your free to set your system up how you like but ZSH is a cool alternative to the regular bash shell which you might like, FISH is also cool.

### `~/.editorconfig`
Having said we won't enforce what editor you use we do however ask you install a plugin for it called [Editor Config](http://editorconfig.org/). This plugin allows us to set some basic syntax standards across a wide range of languages for the team which will ensure spaces are used instead of tabs for Python, 2 spaces are used for YAML and so forth.

All you have to do is run this command: `curl https://raw.github.com/thisissoon/.editorconfig/master/.editorconfig > ~/.editorconfig`

If you want you can also clone the repository at [GitHub](https://github.com/thisissoon/.editorconfig) and symlink it.

Any changes made you should be told to update.

## Security
Security is one of the most important things to get right in software development, the last thing we want to do is build a sweet awesome application and someone steal all our data.

### Don't check in passwords
Never ever ever check in passwords or secret keys etc into version control. Credentials of any kind should be loaded from an OS environment variable that can be set during deployment or into a key value store which can be used to access the data. ETCD is a good way to store configuration data and handle service discovery.

### Encrypt your Hard drive
Just in case you loose your laptop or someone yanks it from your bag after a heavy night encrypt your hard drive so data stored on there cannot be decoded. For OSX you just need to turn on FileVault.

## Version Control
Version control is extremely important, we use Git because its flexible and you can always work your way out of any mistake.

### Git Flow
We use Git flow which gives us a nice structure for branch management. To install just follow the [Git Flow Install Guide](https://github.com/nvie/gitflow/wiki/Installation). A good primer on git flow can also be found here: http://jeffkreeftmeijer.com/2010/why-arent-you-using-git-flow/.

Once you have a new repository or cloned an existing one you just need to run `git flow init` and accept the defaults.

Example branch schema:

 * `master`: This is the currently active deployed production version of code, hotfixes come from this branch and are back merged into the develop branch.

 * `develop`: The develop branch is stable code waiting for the next release. Any new code in this branch should have been code reviewed and have unit tests to prove it works.

 * `feature/x`: Feature branches are where current work is being done on active features by one or many developers. Once finished the feature should be pull requested into the develop branch and give a shout out for someone to give it a look over. To start a new feature branch just run git flow feature start my-new-thing and when you are done just run git flow feature finish my-new-thing and it will be merged with the develop branch.

### Where repositories live
We use two 3rd party services for hosting our code. We use BitBucket for private archived closed source projects and GitHub for open source and active closed source projects. You will need accounts on both services and you will need to add your SSH pub key to them.

### General Git Guidelines
We only have a couple of rules when it comes to Git:

1. Never force push on the develop / master branches - its the nuclear option, the last resort.
1. Only rebase your local history

### Pull request guidelines
When issuing pull requests there are a couple of things to consider the following:

Add a descriptive description for the pull request, what does it do, what feature or bug does it add or fix, any key aspects of the code you're unsure of and would like guidance on and links to any documentation you may have written.

## Code Review
We mentioned code reviews in the Version Control section of the Handbook. Don’t fret, this isn’t some long winded get the code signed off in triplicate process.

Basically once you have finished writing your feature branch, squashed the commits, fire a pull request and get someone else on the team to look it over, put a shout out on Slack, someone will pick it up.

Don’t worry, there is no blame or finger pointing if somethings picked up, we want to help you grow and learn and be a better developer.

## Tests
You should have confidence in your code and unit / integration tests help build that confidence. You should always aim to get 100% code coverage but we don’t realistically expect you to meet that, but it’s awesome if you could get as close to that as possible.

Don't be this guy!

![I don't always test my code, but when I do, I do it in production](https://lh6.googleusercontent.com/-Oqv0TQ29P4Y/UWBZBBYl6GI/AAAAAAAAO_E/V1RDLPsoeYQ/w800-h800/production.jpg)

### Continuous Integration
Services such as Travis or Drone.io allow us to automatically run tests suites in a clean environment every time and takes the head ache away of maintaining something like Jenkins. We have gone for Drone.io which is a docker implemented system with a simple configuration system and used by the Googles. On pushes to the master / develop and on pull requests the test suite will be run and the result emailed to us. It also has cool looking badges!

## Environment

### Fig and Fugu
We are using [Fig](http://www.fig.sh/) for setting up our environment. A fig file is placed in each our repositories and defining system requirements. Unfortunately Fig runs everything in really monolithic way, so for development we recommend [Fugu](https://github.com/mattes/fugu). Fugu defines parameters for container to run, which can be easily restarted or replaced by another. To start up an admin container, which is defined in an Fugu [example file](https://github.com/thisissoon/Handbook/blob/feature/environment/examles/fugu_example.yaml) you can run following:
```
fugu run admin
```

### Start containers automatically (autoenv)
If you don't want to run required containers manually, you can set up [autoenv](https://github.com/kennethreitz/autoenv) and run then when you `cd` into your repository directory.
```
docker start doitapi_postgres_1
docker start doitapi_redis_1
docker start doitapi_rabbitmq_1
docker start doitapi_memcached_1
```


## Releasing

### Version numbering
Instead of going with the standard versioning schema of Major, Minor, Patch, we are going to go something a little more explicit, based on dates.

Every piece of software we write should expose a `--version/-v` flag when run from the terminal and perhaps even expose the version number in the CMS.

Lets say for example we release code on the 24th of January 2014, the version number for this release would be:

`2014.01.24.0`

The first three numbers represents the release version based on date, this means we can tell quickly just from running a `--version` flag when the initial release of deployed code took place. The last number represents the hotfix number / patch number, for example if 3 bug fixes were released as 3 separate hot fixes the version number would be:

`2014.01.24.3`

When we do the next release a week or 2 later on the 2nd of February the version number would change to:

`2014.02.02.0`

## Change log
Each repository should contain a `CHANGELOG.rst` which is a reStructuredText formatted file which contains a log of all the releases and changes to the code, for example:

```
Change Log
==========

Next Release
------------
- Feature: New Feature: Pull user tumblr feeds

2014.01.14.1
------------
- Hotfix: Posting new topics to message boards would display the incorrect post count

2014.01.14.0
------------
- Feature: Message boards
- Feature: Added queue manager for processing instagram feeds
- Bug: Twitter streamer would not display correct retweet count
- Bug: Users could not update E-Mail address
```

### When to change the Change Log
There are 2 times when you should change the `CHANGELOG.rst`.

#### 1. When completing feature branches for merge into develop

When new features are finished it’s important we keep track of them once they are merged into develop otherwise we might not be sure what we are releasing when we come to it. At the top of the CHANGELOG.rst should be Next Release section which lists all the features and / or bug fixes waiting for deployment.

Make sure you have updated the `CHANGELOG.rst` when submitting your pull request.

#### 2. When releasing code
When code is being released the Next Release section should just be amended with the new version number as described above.

### Be descriptive
When adding entries to the change log make sure you are descriptive but concise. Also ensure to prepend your description of the change with the following:

* Hotfix: Emergency mission critical bugs
* Bug: Bugs when can wait till the next release
* Feature: A new feature added to the code base

## Docker Tags

We use docker heavily as part of our development and deployment process, the tagging we use is tied to our git branch management.

* `master`: Master will always be our stable production code, so we tag docker images built from master as `prod`
* `develop`: Develop will contain our latest code that will be pushed to QA and production shortly, so these images are tagged with `latest`
* `release/xxx`: Release branches contain code pending production release and so we tag these images with `qa`

In the case of `prod` and `qa` images we will also tag the image with a version number so it's always easy to rollback to
a specific version of the code.

This is all handled during our CI build process.

### Examples

Pulling the latest production image:

```
docker pull soon/some-project:prod
```

Pulling a specific version of production code:

```
docker pull soon/some-project:2015.5.12.2
```

Pulling the current QA release:

```
docker pull soon/some-project:qa
```

Pulling a specific QA version:

```
docker pull soon/some-project:2015.5.12.2.qa
```

Pulling the latest development version (with our without the `latest` tag since `latest` is the default in docker):

```
docker pull soon/some-project / docker pull soon/some-project:latest
```
