# ODK Docs

![Platform](https://img.shields.io/badge/platform-Sphinx-blue.svg) [![License](https://img.shields.io/badge/license-CC%20BY%204.0-blue.svg)](https://creativecommons.org/licenses/by/4.0/) [![Build status](https://circleci.com/gh/getodk/docs.svg?style=shield&circle-token=:circle-token)](https://circleci.com/gh/getodk/docs/) [![Slack status](http://slack.getodk.org/badge.svg)](http://slack.getodk.org/)

This repo is the source for ODK documentation.

The published documentation is at:

 - https://docs.getodk.org

Please [file an issue](https://github.com/getodk/docs/issues) if you can't find what you are looking for.

## Building and viewing documentation locally

There are two options for building and viewing ODK docs locally: [using Docker](#docker) or [setting up a local Python/Sphinx environment](#python-environment). We generally recommend starting with the Docker image unless you already have a Sphinx environment set up. The [Contributor Guide](https://docs.getodk.org/contributing) describes the philosophy behind the docs, style considerations, how to write restructured text and more.

## <a name="docker"></a>Using Docker

[Docker](https://www.docker.com/) is a platform that makes it easier to package applications so that they can work on any computer. This is particularly valuable when setting up development environments which can work very differently based on versions of the tools involved. 

### Prerequisites
 * Install Docker
   * Windows and Mac users should follow the instructions in [the get started guide](https://www.docker.com/get-started)
   * Linux users should follow the instructions for their specific distribution: [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/), [Debian](https://docs.docker.com/install/linux/docker-ce/debian/), [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/), [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/), [Binaries](https://docs.docker.com/install/linux/docker-ce/binaries/)
   
   More info at the [Docker CE docs page](https://docs.docker.com/install/)
 * Install [git](https://git-scm.com/downloads)
 * Install [Git-LFS](https://git-lfs.github.com/)

### Cloning the repo

Clone the docs repo. For example, at the command line:

```
git clone https://github.com/getodk/docs.git
```

It can take a long time (>10 minutes) to clone the repo due to the large number of images in the docs. If you get an error such as `Smudge error` or `GitHub's rate limit reached`, run `git checkout -f HEAD` until you get the message `Checking out files: 100% done`.

### Building the Docker image

Next, you need to build the Docker image with all the tools you will be using to work with ODK's docs.

```
docker build -t odk-docs .
```

It can take a long time to build the Docker image, but you only need to do this once.

**Windows users**
 * All commands should be run in an elevated PowerShell window. Right click on PowerShell and select the "Run as administrator" option.
 * Ensure Docker is running by checking your system tray. If Docker is not running, launch "Docker for Windows" app and wait until a notification confirms that Docker is running.

### Building and serving the docs locally 

Build and serve the docs locally with:
 * Windows: `.\run-task.bat odk1-autobuild`
 * Linux/macOS: `./run-task.sh odk1-autobuild`
 
Once your terminal shows a "Serving on http://0.0.0.0:8080" message, you can then view the docs in your browser at http://localhost:8080.

Changes you make in the source files will automatically be built and shown in your browser.

Press `Ctrl-C` on your keyboard to stop the build server. It could take a while to effectively stop, and you can always close the terminal window.

If you get a `The name "odk-docs" is already in use by container` error message, run the following command:

```
docker kill odk-docs
```

### Other build tasks  

You can also use the `run-task` script described above to run just a portion of the build process. See available [build tasks](#tasks) below.

## <a name="python-environment"></a>Python environment

### Prerequisites

 * Install [Python 3](https://www.python.org/downloads/)
 * Install [git](https://git-scm.com/downloads)
 * Install [Git-LFS](https://git-lfs.github.com/)
 
We highly recommend you use a virtual environment like [virtualenv](https://virtualenv.pypa.io/en/stable/) or a Python version management like [pyenv](https://github.com/pyenv/pyenv). (Type `python --version` to see your current version.)

- Instructions for setting up virtual environment:

      A `virtual environment`_ is a Python construct
      that lets you download and install tools for a specific project
      without installing them for your entire computer.

      .. _virtual environment: https://docs.python.org/3/tutorial/venv.html

      #. Create the virtual environment.

         .. tabs::
   
            .. group-tab:: Bash

               .. code:: console

                  /odk/ $ python3 -m venv odkenv

            .. group-tab:: PowerShell

               .. code:: powershell

                  /odk/ > python -m venv odkenv

      #. Activate the virtual environment.

         .. tabs::

            .. group-tab:: Bash
      
               .. code:: console

                  /odk/ $ source odkenv/bin/activate
                  (odkenv) /odk/ $

            .. group-tab:: PowerShell

               .. code:: console

                  /odk/ > source odkenv/bin/activate
                  (odkenv) /odk/ >

         The ``(odkenv)`` before the prompt shows that the virtual environment is active.
         You will need to have this active any time you are working on the docs.
      
         If the file cannot be found, your activate file may be located under odkenv/scripts/activate.

         Later, to deactivate the virtual environment:

         .. tabs::

            .. group-tab:: Bash
      
               .. code:: console

                  (odkenv) /odk/ $ deactivate
                  /odk/ $

            .. group-tab:: PowerShell

               .. code:: console

                  (odkenv) /odk/ > deactivate
                  /odk/ >
		  

### Cloning the repo

Clone the docs repo and make sure all the requirements are installed:

```bash
$ git clone https://github.com/getodk/docs.git
$ cd docs/
$ pip install -r requirements.txt
```

It can take a long time (>10 minutes) to clone the repo due to the large number of images in the docs. If you get an error such as `Smudge error` or `GitHub's rate limit reached`, run `git checkout -f HEAD` until you get the message `Checking out files: 100% done`.

### Building the docs 
### Building the docs

Once your environment is set up, build and serve the docs locally with:

```bash
$ make odk1
$ cd odk1-build
$ python -m http.server 8000
```

You can then view the docs in your browser at http://localhost:8000.

You can also use `make` to build just a portion of the build process. See available [build tasks](#tasks) below.

## <a name="tasks"></a>Build tasks


|          | Build & Serve | Build |    Copy     |     LaTeX     |     Style Check     |    Spell Check     |    Check All   |
| -------- | :-----------: | :---: | :---------: | :-----------: | :-----------------: | :----------------: | :----------------: |
| **Options** | odk1-autobuild | odk1-build | odk1-copy |  odk1-latex |  odk1-style-check | odk1-spell-check |    odk1-check     |


## How to contribute?

We are open for new issues and pull requests.

 - Please read the [Contributors Guide](https://docs.getodk.org/contributing) before working on the documentation.
 - Find issues to work on.
    - First time contributors are encouraged to complete a [line edit](https://github.com/getodk/docs/issues/96) as a way to get familiar with our contribution process.
	- Issues labelled [easy](https://github.com/getodk/docs/labels/easy) do not require much specific technical knowledge.
	- Issues labelled [contributor friendly](https://github.com/getodk/docs/labels/contributor%20friendly) are usually self-contained and don't require extensive knowledge of the ODK ecosystem as a whole.
	
You can also...

 - [Discuss the documentation from a user perspective in our forum](https://forum.getodk.org/c/development/documentation).
 - [Discuss the documentation from a contributor perspective in our developer Slack](slack.getodk.org). (Use the #docs-code channel.)
 - [File an issue](https://github.com/getodk/docs/issues) for any needed improvements.
 - [Watch](https://github.com/getodk/docs/subscription) and star this repo, to keep up with what we're doing.

## Troubleshooting
- If you get an `extension error` or a `configuration error`:
  - Make sure your virtual environment is activated.
  - Type `python --version` to check your current python version (it should be 3.x).
  - Run `pip install -r requirements.txt`.
  