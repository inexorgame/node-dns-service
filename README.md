node-dns-service
----------------

This is a DNS service prototype for registering domains based on a UUID system.
It makes use of Amazon Web Service, specifically using

- AWS Lambda
- AWS DynamoDB
- AWS Route 53
- AWS S3
- AWS CloudFormation

# Overview

## Why a DNS service

As a common base for many flex features (such as OpenID Connect based Authentication), a flex instance needs to have two things

- a valid SSL certificate
- consequently, a domain name, to request this certificate

This requires an administrator to have knowledge of SSL technology, and to own a DNS.
To overcome this problem, we wrote the `node-dns-service`, which tries to solve these problems. It provides domain names to a requesting party, which can then use Let's Encrypt to issue a SSL certificate for themselves.

## Flow

There is two methods in the service flow

### `register`
Call `PUT` on `register`
This will register a new node to issue a domain for.
It returns both the `node` id, and a `revocationSecret`.

### `revoke`
You can call the `revoke` method by using `DELETE` on the API, passing along the `node` id (as `node` parameter) of your node and `revocationSecret` as `revocationSecret` parameter.
This will

- remove the DNS entry for your node
- disable the `node` so it can not be used any longer in the future

## Aliases 

You can add an alias for your node using our `alias.json` file on GitHub (pull request).
Once we approved your alias, a CNAME record will be added for the specified `node` id.

# Developer

## Toolchain
We use the following toolchain

- `serverless` for deployment and integration testing
- `eslint` for linting

We also have a set of basic enviroment variables, which you can find in the `serverless.yml#provider` config.


## Deploying

### Preriquites
You will need:

- a hosted zone for the domain

Everything else will be set up for you automagically.

### Get started

After you created an IAM user and a hosted zone do the following

- update the `BASE_DOMAIN`, `ALIAS_FILE` environment variables in `serverless.yml`
- update the `HOST_ZONE_ID` variable in `serverless.yml#custom`
- `npm install -g serverless` 
- set up your IAM user for serverless, see [this link](bit.ly/aws-creds-setup)
- `serverless deploy`

That's it. Serverless will tell you where the API endpoints are. Have fun!

### Price

- Lambda will serve 1 million requests for free for us, very unlikely to hit this limit (the functions run in <10 ms, with <50MB RAM)
- DynamoDB offers 10GB free storage and 1 million reads/writes per month (unlikely to hit)
- S3 provides 1 GB free storage, we need about 8 MB
- Route53 is free for 1 million standard queries per month (unlikely)
    - hosted zone costs 0.50 Dolar / month
    
=> 0.50 dolar / month
