---
draft: true
date: 2022-09-17
draft: true
authors:
  - william.crum
categories:
  - Software
links:
  - Cloud.gov: https://cloud.gov
  - Cloud Foundry: https://www.cloudfoundry.org
  - Flask: https://flask.palletsprojects.com
---
# Building a Full Stack Application on Cloud.gov

Navigating software development in production environments can be tricky. It can be even more so since we are trying to develop solutions in the DoD. Applications need to be secure and compliant. Where can a developer test and build applications without using a commercial platform and without funding? Cloud.gov is an amazing PaaS that allows developers to test their tools in a federal service.

!!! quote "Cloud.gov"
    Cloud.gov is a secure and compliant Platform as a Service (PaaS). cloud.gov helps federal agencies deliver the services the public deserves in a faster, more user-centered way.
    
<!-- more -->

# What Cloud.gov offers

Any federal employee with a .mil or .gov address can utilize their sandbox environments. They have Cloud Foundry as their primary cloud computing platform. If you are familiar with Tanzu, Firebase, Heroku its similar alternatives. At the time of writing this they have allowed the Marine Corps unlimited app instances, users, private domains; 160 unique routes, 160 service instances and 16 GB of memory. Not too shabby for a free tier.

Some of the services they offer are S3 buckets, AWS ALB (Application Load Balancer), RDS (Relational Database Service), Elasticsearch, Elasticache and an IDP (identity provider).

Today we will be utilizing cloud.gov IDP, RDS to build a basic full stack Python Application.

## Getting Started

### Connecting to Cloud.gov using CF

1. Install the Cloud Foundry CLI: [Windows](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html), [Mac OS X](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html#pkg-mac), or [Linux](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html#pkg-linux).
2. Enter `cf login -a api.fr.cloud.gov --sso`
3. It’ll say One Time Code ( Get one at https://login.fr.cloud.gov/passcode ) – visit this login link in your browser. If you use a cloud.gov account, you may need to log in using your email address, password, and multi-factor authentication token. (EPA, FDIC, GSA, and NSF: use your agency button.) [^1](%5Bhttps://cloud.gov/docs/getting-started/setup/%5D&#40;https://cloud.gov/docs/getting-started/setup/&#41;)

## Cloud.gov Setup

This project covers documenting the process of setting up a python + flask project within the Cloud.gov space.

We will be using cloud.gov Identity Provider (IDP) and an Amazon Webservice Relational Database Service (AWS RDS).

### Setup

Our primary application will be called `template-flask` it will be using `template-flask-uaa` which is connected to cloud.gov IDP and `template-flask-db` which is the RDS.

To set up the IDP you will either go through the User Interface (UI) or by running the following commmand:

```
cf create-service cloud-gov-identity-provider oauth-client my-uaa-client
```

Similar command to create the database.

```
cf create-service aws-rds small-mysql my-db
```

> Running this command will detach from the process, creating the database will take a decent amount of time.

Database information including username, password and JDBC (Java Database Connectivity) with be within the environmental variables. Accessed with `os.environ`.

### Obtaining Credentials

To get the nessecary credentials for utilizes cloud.gov UAA you need to create the service key which will be provided to your application. This can be done with the below command:

```
cf create-service-key \
    demo-uaa \
    my-service-key \
    -c '{"redirect_uri": ["https://demo-app.app.cloud.gov/auth/callback", "https://demo-app.app.cloud.gov/auth/logout"]}'
cf service-key demo-app my-service-key
```

> The redirect\_uri will be the redirect uris which the user will be sent to **after** the authenticated login, whatever url you wish to intercept the information will be that url. It will also include the `/logout` url which properly disconnects the user.

### Credentials

Reading the documentation provided by cloud.gov you need to ensure your application has credentials which match the request. To do so I had to manually set environmental variables which then are accessed within the application. This ensures the information is not passed. You can do this through the GUI or the below command:

```
cf service-key demo-app my-service-key
{
  "credentials": {
    "client_id": "12345678-1234-1234-1234-12345672890",
    "client_secret": "1234567890qwertyuiopasdfghjkl"
  }
}
cf set-env demo-app client_id 12345678-1234-1234-1234-12345672890
cf set-env demo-app client_secret 1234567890qwertyuiopasdfghjkl
```

### Setting up the Application Manifest

A manifest is a file that describes the way the application deploys in Cloud Foundry.

```yaml
---
version: 1
applications:
- name: demo-app
  memory: 512MB
  disk_quota: 512MB
  random-route: true
  buildpack: python_buildpack
  command: gunicorn main:app -b :8080
  env:
    APP_SETTINGS: Production
  services:
    - name: demo-uaa
    - name: demo-db
```

#### The Manifest Explained

What you see here is a overview of our deployed application. Under line `services` you will see all of the binded application we built. The `buildpack` is a basic container that has the majority of tools and software you would need to deploy an application. Since we are using python I chose `python_buildpack`. Since we are utilizing the `python_buildpack` it will look for a `requirements.txt` at the base of your repository. It will install all of your depencies for you at runtime. The `command` is the command that starts your program. Often called an entrypoint this is what starts your beautiful code. Settings `memory`, `disk_quote` arent too important. Since we are a free tier I have found it to be the highest amount usable. `random-route` generates a random URL for you to use. See cloud.gov docs [https://cloud.gov/docs/services/external-domain-service/](https://cloud.gov/docs/services/external-domain-service/).

#### Deploying Your App

Once you have your database, authentication, and application. Running `cf push` will deploy your code to cloud.gov\! Now we just gotta write the code.

## The Coding Part

I will be skipping general parts and keep to focusing around cloud.gov integration.

### Connecting to the Database

Even though we binded our application, still means we gotta code in the connection.

```python
class Config(object):
    TEMPLATES_AUTO_RELOAD = True
    APP_DIRECTORY = os.getcwd() + "/app"
    TESTING = False

class Production(Config):
    PORT = 8080
    CLIENT_ID = LOCAL_ENV.get("client_id")
    CLIENT_SECRET = LOCAL_ENV.get("client_secret")
    VCAP_APPLICATION = LOCAL_ENV.get("VCAP_APPLICATION")
    VCAP_SERVICES = LOCAL_ENV.get("VCAP_SERVICES")

    if VCAP_SERVICES:
        DATABASE_HOST = VCAP_SERVICES["aws-rds"][0]["credentials"]["host"]
        DATABASE_USERNAME = VCAP_SERVICES["aws-rds"][0]["credentials"]["username"]
        DATABASE_PASSWORD = VCAP_SERVICES["aws-rds"][0]["credentials"]["password"]
        DATABASE_NAME = VCAP_SERVICES["aws-rds"][0]["credentials"]["db_name"]
        DATABASE_URI = f"mysql+pymysql://{DATABASE_USERNAME}:{DATABASE_PASSWORD}@{DATABASE_HOST}:3306/{DATABASE_NAME}"

    REDIRECT_URI = "https://demo-app.app.cloud.gov/auth/callback"
    LOGOUT_REDIRECT_URI = "https://demo-app.app.cloud.gov/"
    UAA_AUTHORIZE_URI = "https://login.fr.cloud.gov/oauth/authorize"
    UAA_LOGOUT_URI = "https://login.fr.cloud.gov/logout"
    UAA_TOKEN_URI = "https://uaa.fr.cloud.gov/oauth/token"
```

Once binded the application container service connections will be found within a JSON object called `VCAP_SERVICES`. Cloud Foundry allows admins to look through the environmental variables in the dashboard. Highly recommend searching through that.