# howto-update-a-dockerfile-with-dependabot
A Tutorial on how to have dependabot update your Dockerfile

This repository has a very simple php project that uses a basic dockerfile:

    FROM: php:8.0.0

The code itself is a simple hello world style php script, that only prints the current php version:

    <?
    echo phpversion();
    ?>

Running this php code within the specified Dockerfile is easy with github actions. The file `.github/workflows/php.yml` looks like this:

    name: PHP CI
    on:
    push:
        branches: [ main ]
    pull_request:
        branches: [ main ]
    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Build docker images
        run: docker build -t local - < Dockerfile
        - name: Run tests
        run: docker run -t -v $PWD:/srv -w/srv local php hello.php

It runs the CI workflow on the `main` branch and on pull requests. It builds the docker image in the first step and runs the php script in the second step.

The next thing to be done is to go to the "Insights" tab in your github repository, then select "Dependency Graph" and then go to "Dependabot":

"Insights" -> "Dependency Graph" -> "Dependabot"

And create the following `.github/dependabot.yml` file:

    version: 2
    updates:
     - package-ecosystem: "docker"
       directory: "/"
       schedule:
         interval: "daily"

This should prompt dependabot to create a pull request that proposes the correct update to your `Dockerfile`.