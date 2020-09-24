# Heroku Multi Requirements file buildpack

_tl;dr_ -- The idea here is that you have a single git repository, but multiple Heroku apps. In other words, you want to share a single git repository to power multiple Heroku apps. So, for each app you need this buildpack, and for each app, you need to set a config variable named `REQUIREMENTS_FILE` to the location where the requirements.txt is for that app. As an example:

```
$ heroku create -a example-1
$ heroku create -a example-2
$ heroku buildpacks:add -a example-1 https://github.com/tspecht/heroku-buildpack-multi-requirements.git
$ heroku buildpacks:add -a example-2 https://github.com/tspecht/heroku-buildpack-multi-requirements.git
$ heroku config:set -a example-1 REQUIREMENTS_FILE=requirements.txt
$ heroku config:set -a example-2 REQUIREMENTS_FILE=backend/requirements.txt
$ git push https://git.heroku.com/example-1.git HEAD:master
$ git push https://git.heroku.com/example-2.git HEAD:master
```

_Make sure this Buildpack is listed **before** the official Python buildpack as otherwise the proper `requirements.txt` file will not be in place by the time Heroku installs your dependencies!_

When example-1 builds, it'll copy `requirements.txt` into `/app/requirements.txt`, and when example-2 builds, it'll copy `backend/requirements.txt` to `/app/requirements.txt`.

## Pipelines

Only **builds** will set the proper requirements.txt. If you use [Heroku Pipelines](https://devcenter.heroku.com/articles/pipelines), then promoting a slug downstream will not trigger a build, and therefore will not look at the environment variable and act accordingly. Make sure that the proper requirements.txt is referenced all the way upstream to the first stage that builds.
