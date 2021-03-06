# Cookiecutter for Computational Chemistry Python Packages
[//]: # (Badges)
[![Travis Build Status](https://travis-ci.org/choderalab/cookiecutter-compchem.png)](https://travis-ci.org/choderalab/cookiecutter-compchem)
[![AppVeyor Build status](https://ci.appveyor.com/api/projects/status/plygjybxqxy04rad/branch/master?svg=true)](https://ci.appveyor.com/project/lnaden/cookiecutter-compchem/branch/master)
[![Documentation Status](https://readthedocs.org/projects/compchem-cookiecutter/badge/?version=latest)](http://compchem-cookiecutter.readthedocs.io/en/latest/?badge=latest)


A [cookiecutter](https://github.com/audreyr/cookiecutter) template for those interested in developing computational 
chemistry packages in Python. Skeletal starting repositories can be created from this template to create the 
file structure semi-autonomously so you can focus on what's important: the science!

The skeletal structure is designed to help you get started, but do not feel limited by the skeleton's features 
included here. Just to name a few things you can alter to suite your needs: change continuous integration options, 
remove deployment platforms, or test with a different suite.

## Features
* Python-centric skeletal structure with initial module files
* Pre-configured `setup.py` for installation and packaging
* Pre-configured Window, Linux, and OSX continuous integration on AppVeyor and Travis-CI
* Choice of dependency locations through `conda-forge`, default `conda`, or `pip` 
* Basic testing structure with [PyTest](https://docs.pytest.org/en/latest/)
* Automatic `git` initialization + tag
* GitHub Hooks
* Automatic package version control with [Versioneer](https://github.com/warner/python-versioneer)
* Sample data inclusion with packaging instructions
* Basic documentation structure powered by [Sphinx](http://www.sphinx-doc.org/en/master/)
* Automatic license file inclusion from several common Open Source licenses (optional)

## Requirements

* Python 3.5, or 3.6
* [Cookiecutter](http://cookiecutter.readthedocs.io/en/latest/installation.html)
* [Git](https://git-scm.com/)

## Usage

With [`cookiecutter` installed](https://cookiecutter.readthedocs.io/en/latest/installation.html#install-cookiecutter), 
execute the following command inside the folder you want to create the skeletal repository. 

```bash
cookiecutter gh:choderalab/cookiecutter-compchem
```

Which fetches this repository from github automatically and prompts the user for some simple information such as 
package name, author(s), and licences. 

[![The cookiecutter in action](http://img.youtube.com/vi/_E7AlaG8zbk/0.jpg)](http://www.youtube.com/watch?v=_E7AlaG8zbk "Computational Chemistry Cookieucutter Example")

## Next steps and web integrations
The repository contains a number of "hooks" that integrate with a variety of web services. To fully integrate the project
with these web services and to get started developing your project please proceed through the following directions.

### Local installation
For development work it is often recommended to do a "local" python install via `pip install -e .`. This command will insert your
new project into your Python site-packages folder so that it can be found in any directory on your computer.

### Setting up with GitHub
Upon creation, this project will initialize the output as a `git` repository compatible with 
[Versioneer](https://github.com/warner/python-versioneer). However, this does not automatically register the 
repository with GitHub. To do this, follow the instructions for 
[Adding an existing project to GitHub using the command line](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/). 
Follow the first step to create the repository on GitHub, but ignore the warnings about the README, license, and 
`.gitignore` files as this repo creates them. From there, you can skip to after the "first commit" instructions and 
proceed from there.

### Testing
The Python testing framework was chosen to be [pytest](https://pytest.org) for this project. Other testing frameworks are available;
however, the authors believe the combination of easy [parametrization of tests](https://docs.pytest.org/en/latest/parametrize.html),
[fixtures](https://docs.pytest.org/en/latest/fixture.html), and [test marking](https://docs.pytest.org/en/latest/example/markers.html)
make `pytest` particularly suited for computational chemistry.

To get started additional tests can be added to the `project/tests/` folder. Any function starting with `test_*` will automatically be
included in the testing framework. While these can be added in anywhere in your directory structure, it is highly recommended to keep them
contained within the `project/tests/` folder.

Tests can be run with the `py.test -v` command. There are a number of additional command line arguments to 
[explore](https://docs.pytest.org/en/latest/usage.html).

### Continuous Integration
Testing is accomplished with both [Appveyor](https://www.appveyor.com) (for Windows testing) and 
[Travis-CI](https://travis-ci.org) (for Linux testing). These frameworks are chosen as they
are completely free for open source projects and allow you to automatically verify that your project works under a 
variety of OS's and
Python versions. To begin please register with both Appveyor and Travis-CI and turn on the git hooks under the project 
tabs. You will also want to correct the badges which appear on the output README file to point to the correct links

You may notice that our scripts 
[check the MD5 hash for the Miniconda installer](%7B%7Bcookiecutter.repo_name%7D%7D/devtools/travis-ci/before_install.sh) 
before installing. In general, it is often good idea to check the MD5 of any file which you are pulling from the net
automatically, 
especially if there are mirrors, as a simple (but not fool-proof) method of ensuring you got the expected file for 
effectively free.
However, there are a couple other reasons we check the MD5 for the Miniconda installer:

* Prevent getting the wrong Miniconda version. Sometimes the Miniconda maintainers will update their download links for 
  `latest` version before updating the MD5 hashes on the site. This can lead to some unexpected behavior, 
  especially on major Conda version upgrades. Thus, the MD5 check helps trap that.
* Should Miniconda ever change their distribution method, this check will fail and you the maintainer can find out 
  what has changed to update your code as needed. 
* Some projects may need to pin to very specific, or maximum Conda versions. This helps ensure version expectations. 
  It should be noted this is a very rare case. 
  
#### Pre-caching common build data

Some continuous integration platforms allow for caching of build data, which you may, or may not, find advantageous. 
The general purpose of the caches are to store and fetch files and folders which may take a long time to either 
generate or download every time you want to run a CI build; often because build (and developer) time is limited. 
However, if the cached data changes any time during a build, then the whole targeted cache is updated and uploaded. 
So, you should only cache things you do not expect to change. 

You may be tempted to cache the Conda installer or Python dependencies fetched from `conda` or `pip`, however, this 
is an ill advised idea for two main reasons:

1. Your package's dependencies are constantly updating,  
   so you want catch things which break due to dependencies before your user does. Having CI automatically trigger when 
   you make changes and at scheduled intervals helps catch these things as soon as possible.
    * Because you should expect dependencies updating, you will have to upload a new cache each build anyways, somewhat 
      invalidating one of the advantages of a cache. 
2. It is a good idea to make sure your testing emulates the most basic user of your code if possible. 
   If your target users include people who will try to download your package and have it "just work" for their project, 
   then your CI testing should try to do this as well. This would include getting newest, updated installer and 
   dependencies. One example 
   of this may be industry, non-developer users, who do not know all the nuances and inner workings of package 
   dependencies or versions. It is not reasonable to expect them to know these nuances either, its why you are the 
   developer.

There may be some times where the caching feature is helpful for you. One example: including test data which is too 
large to store on GitHub, but also has a slow mirror hosting it. A cache will help speed up the test since you 
 wont have to download from the slower mirror. If you this sounds like a helpful feature, you can check out the 
links below. We do not implement them for this Cookiecutter, but they can be added to your package as needed.

* [Travis-CI Caching](https://docs.travis-ci.com/user/caching/)
* [AppVeyor Caching](https://www.appveyor.com/docs/build-cache/)
 
  

### Documentation 
Make a [ReadTheDocs](https://readthedocs.org) account and turn on the git hook. Although you can manually make the 
documentation yourself through [Sphinx](http://www.sphinx-doc.org/en/master/usage/quickstart.html), you can also 
configure [ReadTheDocs](https://docs.readthedocs.io/en/latest/getting_started.html) to automatically build and 
publish the documentation for you. The initial skeleton of the documentation can be found in the `docs` folder 
of your output.

### Additional Python Settings in `setup.py`

This Cookiecutter generates the package, but there are a several package-specific Python settings you can tune to your 
package's installation needs. These are settings in the `setup.py` file which contains instructions for Python on 
how to install your package. Each of the options in the file are commented with what it does and when it should be 
used. 


## Why is Python 2.X not on the supported versions?
New projects generally should not be built with Python 2.7 support in mind, see the 
[Python 3 Statement](https://python3statement.org/). Although the final Python 2.7 release will be 
[supported through 2020](http://legacy.python.org/dev/peps/pep-0373/) and is the default on many legacy systems, Python 
3 has been released for almost a decade and projects long term usage should not be shacked by legacy methods that will 
have to be replaced in very short order as Python 2 support is retired.


## Conda and PyPI (`pip`)

Should you deploy and/or develop on Conda (with the `conda-build` tool) or PyPI (with the `pip` tool)? Good question, 
both have their own advantages and weaknesses as they both are designed to do very different things. Fortunately, 
many of the features you will need for this Cookiecutter overlap.
We will not advocate here for one or the other, nor will we cover all the differences. We can however recommend some 
additional resources where you can read and find out more at the end of this section.

We will cover the major differences you the developer will see between the two as they relate to this Cookiecutter. 

The first major difference is the dependency sources. Both [Conda](https://anaconda.org) and [PyPI](https://pypi.org/) 
have different packages which individually choose to deploy on one or the other (often both), so its important to keep 
that in mind when choosing a dependency source. 

For testing purpose, the PyPi tool, `pip`, is much faster about 
building your packages than the Conda tool, `conda-build`, will be. Depending on the number of dependencies, you may 
have conditions where `conda-build` takes 10-20 min to resolve, download, configure, and install all dependencies 
*before your tests start*, whereas `pip` would do the same in about 5 min. It is also important to note that both 
`pip` and `conda-build` are not *testing tools* in and of themselves, they are deployment and dependency resolution 
tools.  For pure testing, we include other packages like [pytest](https://pytest.org).

From a deployment perspective, it is possible to deploy your package on both platforms, although doing so is beyond 
the scope of this Cookiecutter. 

Lastly, these are optional features! You could choose to not rely on either Conda or PyPI, assuming your package 
does not need dependencies. We do highly recommend you pick one of them for dependency resolution so you (and your 
potential users) are not having to manually find and install all the dependencies you may have. To put some historical 
perspective on this, NumPy and SciPy used to ask the users to install the [BLAS](http://www.netlib.org/blas/) and 
[LAPACK](http://www.netlib.org/lapack/) libraries on their own, and 
then also make sure they were linked correctly to use in Python. These hurdles are no longer required through the 
package managers, Huzzah!

### Additional reading for Conda and PyPI

* [Author of the Python Data Science Handbook from O'Rilley's Blog on Conda Myths and Misconceptions](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/)
* [Conda's Package Management docs](https://conda.io/docs/user-guide/tasks/manage-pkgs.html)
* [`pip` User Guide](https://pip.pypa.io/en/stable/user_guide/)



## Output Skeleton

This will be the skeleton made by this `cookiecutter`, the items marked in `{{ }}` will be replaced by your choices 
upon setup.

```
.
├── LICENSE                         <- License file
├── README.md                       <- Description of project which GitHub will render
├── appveyor.yml                    <- AppVeyor config file for Windows testing (if chosen)
├── {{repo_name}}
│   ├── __init__.py                 <- Basic Python Package import file
│   ├── {{first_module_name}}.py    <- Starting packge module
│   ├── data                        <- Sample additional data (non-code) which can be packaged
│   │   ├── README.md
│   │   └── look_and_say.dat
│   ├── tests                       <- Unit test directory with sample tests
│   │   ├── __init__.py
│   │   └── test_{{repo_name}}.py
│   └── _version.py                 <- Automatic version control with Versioneer
├── devtools                        <- Deployment, packaging, and CI helpers directory 
│   ├── README.md
│   ├── conda-recipe                <- Conda build and deployment skeleton
│   │   ├── bld.bat
│   │   ├── build.sh
│   │   └── meta.yaml
│   └── travis-ci
│       └── install.sh
├── docs                            <- Documentation template folder with many settings already filled in
│   ├── Makefile
│   ├── README.md                   <- Instructions on how to build the docs
│   ├── _static
│   ├── _templates
│   ├── conf.py
│   ├── index.rst
│   └── make.bat
├── setup.cfg                       <- Near-master config file to make house INI-like settings for Coverage, Flake8, YAPF, etc.
├── setup.py                        <- Your package's setup file for installing with additional options that can be set
├── versioneer.py                   <- Automatic version control with Versioneer
├── .github                         <- GitHub hooks for user contrubtion and pull request guides
│   ├── CONTRIBUTING.md
│   └── PULL_REQUEST_TEMPLATE.md
├── .codecov.yml                    <- Codecov config to help reduce its verbosity to more reasonable levels
├── .gitignore                      <- Stock helper file telling git what file name patterns to ignore when adding 
└── .travis.yml                     <- Travis-CI config file for Linux and OSX testing
```

## Acknowledgments

This cookiecutter is developed by Levi N. Naden of Memorial Sloan Kettering Cancer Center in conjunction with 
Daniel G. A. Smith from the [Molecular Sciences Software Institute (MolSSI)](http://molssi.org/). Copyright (c) 2018.

Directory structure template based on recommendation from the 
[Chodera Lab's Software Development Guidelines](https://github.com/choderalab/software-development/blob/master/STRUCTURING_YOUR_PROJECT.md).

Elements of this repository drawn from the 
[cookiecutter-data-science](https://github.com/drivendata/cookiecutter-data-science) by Driven Data
and the [MolSSI Python Template](https://github.com/MolSSI/python_template)
