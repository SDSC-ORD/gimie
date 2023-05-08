# Gimie

[![PyPI version](https://badge.fury.io/py/gimie.svg)](https://badge.fury.io/py/gimie) [![Python Poetry Test](https://github.com/SDSC-ORD/gimie/actions/workflows/poetry-pytest.yml/badge.svg)](https://github.com/SDSC-ORD/gimie/actions/workflows/poetry-pytest.yml)

Gimie (GIt Meta Information Extractor) is a python library and command line tool to extract structured metadata from git repositories.


## Context
Scientific code repositories contain valuable metadata which can be used to enrich existing catalogues, platforms or databases. This tool aims to easily extract structured metadata from a generic git repositories. The following sources of information are used:

* [x] Github API
* [x] Gitlab API
* [x] Git index
* [ ] License text
* [ ] Free text in README
* [ ] Renku project metadata

----------------------------------------------------------------------

Using Gimie: easy peasy, it's a 3 step process. 

## STEP 1: Installation

To install the stable version on PyPI:

```shell
pip install gimie
```

To install the dev version from github:

```shell
pip install git+https://github.com/SDSC-ORD/gimie.git@main#egg=gimie
```

Gimie is also available as a docker container hosted on the [Github container registry](https://github.com/SDSC-ORD/gimie/pkgs/container/gimie):

```shell
docker pull ghcr.io/sdsc-ord/gimie:latest

# The access token can be provided as an environment variable
docker run -e ACCESS_TOKEN=$ACCESS_TOKEN ghcr.io/sdsc-ord/gimie:latest gimie data <repo>
```

## STEP 2 : Set your credentials

In order to avoid rate limits with the github api, you need to provide your github
username and a github token with the `read:org` scope. 

### A. Create access tokens

New to access tokens? Or don't know how to get your Github / Gitlab token ? 

Have no fear, see
[here for Github tokens](https://docs.github.com/en/enterprise-server@3.4/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) and [here for Gitlab tokens](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html).
(Note: tokens are as precious as passwords! Treat them as such.)

### B. Set your access tokens via the Terminal

Gimie will use your access tokens to gather information for you. If you want info about a Github repo, Gimie needs your Github token; if you want info about a Gitlab Project then Gimie needs your Gitlab token.

Add your tokens one by one in your terminal:
your Github token:
```bash
export GITHUB_TOKEN=
```
and/or your Gitlab token:
```bash
export GITLAB_TOKEN=
```

## STEP 3: GIMIE info ! Run Gimie

### As a command line tool

```shell
gimie data https://github.com/numpy/numpy
```
(want a Gitlab project instead? Just replace the URL in the command line) 

### As a python library

```python
from gimie.project import Project
proj = Project("https://github.com/numpy/numpy)

# To retrieve the rdflib.Graph object
g = proj.to_graph()

# To retrieve the serialized graph
proj.serialize(format='ttl')
```

Or to extract only from a specific source:
```python
from gimie.sources.github import GithubExtractor
gh = GithubExtractor('https://github.com/SDSC-ORD/gimie')
gh.extract()

# To retrieve the rdflib.Graph object
g = gh.to_graph()

# To retrieve the serialized graph
gh.serialize(format='ttl')
```
[For a GitLab project, replace `gimie.sources.github` by `gimie.sources.gitlab`, `GithubExtractor` by `GitlabExtractor`, as well as the URL to the GitLab project.]

## Outputs

The default output is JSON-ld, a JSON serialization of the [RDF](https://en.wikipedia.org/wiki/Resource_Description_Framework) data model. We follow the schema recommended by [codemeta](https://codemeta.github.io/).
Supported formats are json-ld, turtle and n-triples.

### Limitations

* Currently, gimie will only the first 100 contributors of a repository (in arbitrary order), and for each users, at most 100 affiliations.
* If a Github repository is owned by an organization, all "mentionable users" are reported as contributors. This will include all members of the organization in addition to contributors.

----------------------------------------------------------------------

## Contributing

All contributions are welcome. New functions and classes should have associated tests and docstrings following the [numpy style guide](https://numpydoc.readthedocs.io/en/latest/format.html).

The code formatting standard we use is [black](https://github.com/psf/black), with `--line-length=79` to follow [PEP8](https://peps.python.org/pep-0008/) recommendations. We use [pytest](https://docs.pytest.org/en/7.2.x/) as our testing framework. This project uses [pyproject.toml](https://pip.pypa.io/en/stable/reference/build-system/pyproject-toml/) to define package information, requirements and tooling configuration.

### For development:

activate a conda or virtual environment with Python 3.8 or higher

```shell
git clone https://github.com/SDSC-ORD/gimie && cd gimie
make install
```

run tests:

```shell
make test
```

run checks:

```shell
make check
```
for an easier use Github/Gitlab APIs, place your access tokens in the `.env` file: (and don't worry, the `.gitignore` will ignore them when you push to GitHub) 

```
cp .env.dist .env
```

## Releases and Publishing on Pypi

Releases are done via github release

- a release will trigger a github workflow to publish the package on Pypi
- Make sure to update to a new version in `pyproject.toml` before making the release
- It is possible to test the publishing on Pypi.test by running a manual workflow: go to github actions and run the Workflow: 'Publish on Pypi Test'
