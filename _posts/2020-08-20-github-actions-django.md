---
layout: post
title: Setting up GitHub Actions for a Django project
date: 2020-10-05 11:00:00
description: Working with databases is tricky...
---

I've been hoping to use my current work on the [Mood Music](https://ninadicara.co.uk/projects/mood-music/) project to test out GitHub Actions for continuous integration (CI). There are a few extra steps needed for the Django set up because of databases etc so this is a little guide to save my future self the pain of figuring it all out again.


## Why bother?

I'm new enough to programming that I haven't previously used CircleCI or Travis, but have been told that Actions is a straight-forward option for ongoing testing (so, good for first-timers?). In general though,  CI is useful for...

1. Automated regular testing of my code - this also means checking for [regressions](https://en.wikipedia.org/wiki/Regression_testing) that I might have accidentally introduced by changing something. 

2. Testing my code on an OS that I am not developing on, but likely to deploy to. For reference, I am developing on macOS but likely to deploy to a linux virtual machine. 

3. The satisfying ticks of passed tests :D 



### For reference...

For this example I am using:  
Testing framework: **pytest**  
Database backend: **postgres**  
Django framework: [This cookie-cutter](https://github.com/pydanny/cookiecutter-django) (worth noting as it has a slightly different folder structure to classic Django setups)  


So, your tests run locally. What next?


## 1. Create the config file

Create the file ```.github/workflows/django.yml``` or get the template [from here](https://github.com/actions/starter-workflows/blob/9ddf3f1e08711afd130319041f24a61aed6212e9/ci/django.yml). 



*Q. Does it matter if it's on a branch rather than committed straight to my main branch?*  
A. Nope! As soon as the file is on GitHub (by pushing your code, or creating it directly on the repo) the commands will be used as far as I can tell. 

## 2. Decide when you want the actions to run

You use the `on:` keyword to decide which actions will trigger the test suite to run. 

I wanted it to run every time I push to any feature branch, or create a pull request to `develop` or `main`.  This can get much more complicated, so [see the GitHub docs](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow) for all the possibilities. 

```yaml
# .github/workflows/django.yml

on:
  push:
    branches:
    - 'feature/**' # Runs workflow everyone time code is pushed to repo
    paths-ignore:
    - 'docs/**' # Will not run for anything pushed to docs
  pull_request:
    branches:
    # Also runs on any pull request to main or develop branches 
    - main 
    - develop
```


## 3. Set up the os and database 

I started from GitHub's default YAML file for Django, but ended up having to make quite a lot of changes to get it to use the database properly, and include my environment variables etc. 

Assuming you have a database then the `job` section will need a name (here it's called `runner-job`). You will need to tell it which system you want to run it on (I've chosen `ubuntu-latest`), and the strategy for running it which allows you to test on multiple versions of a language. Here I'm just sticking with Python 3.8, but I could have had `[3.7, 3.8]` and run the tests on two Python versions. 

That gave this: 

```yaml
jobs:

  runner-job:

    runs-on: ubuntu-latest
    strategy:
      max-parallel: 4
      matrix:
        python-version: 3.8
```

Next I had to deviate from the original template by including a `services` section which is where you set up the database. Figuring out how to do this was [HUGELY helped by this blog post](https://hacksoft.io/github-actions-in-action-setting-up-django-and-postgres/), which was one of few resources I could find that explained this step. There's also some instructions on [creating PostgreSQL service containers in the GitHub Docs](https://docs.github.com/en/actions/configuring-and-managing-workflows/creating-postgresql-service-containers) and [an exmaple of it being used in a workflow](https://github.com/actions/example-services/blob/master/.github/workflows/postgres-service.yml). Even with all that help, this took some time to get right. 

```yaml
    services:
      postgres: 
        image: postgres:12.3
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: github_actions
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - '5432:5432'
```
It's important to remember that the username, password, name and port need to match what you have specified in your Django project's settings file. My set up has a local settings file where I can define credentials for a testing database for exactly this reason. If you're having problems it's very likely to be related to the specifications in your settings file. 

## 4. Define the steps needed to run the tests

The last step is to actually define the `steps` needed to run your tests...

{% raw %}
```yaml
    steps:
    - uses: actions/checkout@v2

    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}

    - name: Install Dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements/local.txt

    - name: Database Migrations
      run: python manage.py migrate

    - name: Run Tests
      run: |
        pytest
```
{% endraw %}


You'll see that each step is separated out and given a name. I chose these names, you can change them to whatever you like and nothing will go wrong. GitHub will use the names in the CI summary to show progress through each stage. 

First we need to tell it to checkout the repository, which there is already a handy shortcut written for ([actions/checkout@v2](https://github.com/actions/checkout)); this command has lots of other options like checking out multiple repos etc if you need them. In the first `Set up Python` block we're executing this action using the settings we defined above in `strategy`. 

In `Install Dependencies` we are now setting up the environment. GitHub is basically providing a blank environment, so as usual we have to tell it to install all of our requirements. Similarly, we have to then run our database migrations for the first time in `Database Migrations`.

Lastly (finally??), now that our environment is all set up and running we get to run the test command. The appropriate command for my project is `pytest`, but you can also use a `python manage.py ....` command here.


## 5. Add environment variables like API keys and secrets

Now, the stages in Step 4 won't work on their own just yet, because I'm missing some crucial environment variables. At the top of the yaml file you can define a top level section called `env` that holds some generic environmental variables that your postgres server needs in the steps defined above.

```yaml
env:
  POSTGRES_HOST: localhost
  POSTGRES_PORT: 5432
  POSTGRES_USER: postgres
  POSTGRES_PASSWORD: postgres
```

Lastly, I also have some API keys written into my code by calling environment variables, for example `os.environ.get("TWILIO_AUTH_TOKEN")`. We obviously don't want to hard code these anywhere, but they are needed to run the tests. Thankfully GitHub is able to store secrets related to your repository which you can call in the testing workflow. To add your environment variables to the repository [follow these instructions](https://docs.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets#creating-encrypted-secrets-for-a-repository). Note to self... remember not to enclose them in quotation marks when you save them in GitHub 🙄. *N.B. You need admin access to the repository to do this*

Now, all we need to do is add them to the `env` list, remembering to give them the same names as they have in your code. 

{% raw %}
```yaml
env:
  # Postgres variables
  POSTGRES_HOST: localhost
  POSTGRES_PORT: 5432
  POSTGRES_USER: postgres
  POSTGRES_PASSWORD: postgres
  # API keys
  SPOTIFY_CLIENT_ID: ${{ secrets.SPOTIFY_CLIENT_ID }}
  SPOTIFY_CLIENT_SECRET: ${{ secrets.SPOTIFY_SECRET_ID }}
  TWILIO_AUTH_TOKEN: ${{ secrets.TWILIO_AUTH_TOKEN }}
```
{% endraw %}

Now these environment variables are accessible to GitHub actions. 


## Done! 

My final full workflow is pasted below in case it's of use. This took me quite a bit of time to work out, but I'm pleased I did! I now have regular (completley automated) health checks on my code and know very quickly when something has broken. Hopefully it'll be quicker to work out how to this for my next project now too 😄.

{% raw %}
```yaml
name: Testing

env:
  # Postgres variables
  POSTGRES_HOST: localhost
  POSTGRES_PORT: 5432
  POSTGRES_USER: postgres
  POSTGRES_PASSWORD: postgres
  #API keys
  SPOTIFY_CLIENT_ID: ${{ secrets.SPOTIFY_CLIENT_ID }}
  SPOTIFY_CLIENT_SECRET: ${{ secrets.SPOTIFY_SECRET_ID }}
  TWILIO_AUTH_TOKEN: ${{ secrets.TWILIO_AUTH_TOKEN }}

on:
  push:
    branches:
    - 'feature/**' # Runs workflow everyone time code is pushed to repo
    paths-ignore:
    - 'docs/**' # Will not run for anything pushed to docs
  pull_request:
    branches:
    # Also runs on any pull request to main or develop branches 
    - main 
    - develop

jobs:

  runner-job:

    runs-on: ubuntu-latest
    strategy:
      max-parallel: 4
      matrix:
        python-version: 3.8

    services:
      postgres: 
        image: postgres:12.3
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: github_actions
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - '5432:5432'

    steps:
    - uses: actions/checkout@v2

    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}

    - name: Install Dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements/local.txt

    - name: Database Migrations
      run: python manage.py migrate

    - name: Run Tests
      run: |
        pytest
```
{% endraw %}