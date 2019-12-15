# gcp-app-base

This is a base project to start backend app using GCP App Engine (GAE) offering.

## App Engine Environment 

There are 2 types of App Engine envoriment.
Generally, for fast prototyping, one should start with standard environment.

For detail comparison and weighting between the two, refer to this:
https://cloud.google.com/appengine/docs/the-appengine-environments
https://cloud.google.com/appengine/docs/flexible/go/flexible-for-standard-users

## Project Structure

This project contains 2 sample app (exactly the same) that are deployed as different service endpoints in GAE. One to standard and one to flexible.

### Standard

For standard deployment, to build and deployment with submodules:
```shell
unset GOPATH
```
ref: https://github.com/golang/appengine/issues/199

### Flexible

For flex deployment, using `runtime: go` with go submodules resulted in error during image build (builder does not seem to have git in $PATH).

So `runtime` is set to `custom` to use Dockerfile to customize the builder and deploy only the built app binary.

## Deployment

These are the project creation and deployment sequence (assuming all commands go well, the gcloud provides pretty good clues for troubleshoting if anything goes wrong along the way) 

Refer to pre-requisites for App Engine deployment here:

https://cloud.google.com/appengine/docs/flexible/go/quickstart
https://cloud.google.com/appengine/docs/flexible/go/using-go-libraries


```shell
gcloud config set project <PROJECT_ID>

gcloud app create 

gcloud app deploy standard/app.yaml flex/app.yaml --version=<VERSION> --stop-previous-version

Once deployed, the services are available at:
https://standard-dot-<PROJECT_ID>.appspot.com
https://flex-dot-<PROJECT_ID>.appspot.com

## To avoid incuring charge, stop the services after experimenting
gcloud app versions stop <VERSION>

```

## Note on Stopping GAE standard service

* The serving instance on GAE Standard cannot be stopped with the command above, with this error
```
ERROR: (gcloud.app.versions.stop) INVALID_ARGUMENT: serving status cannot be changed for Automatic Scaling versions
```
  * One way is to go to console.google.com/appengine/ setting to `disable application`.
  * Another way is to deploy a dummy service with static file - thus making the version stoppable.
  ```shell
  gcloud app deploy standard-dummy/app.yaml --stop-previous-version
  ## Afterward the specific version of the services can be deleted by
  gcloud app services delete standard --version=v2
  ```