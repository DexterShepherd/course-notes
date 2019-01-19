# Front end master "AWS for Front end engineers" course

## Introduction

### Problem
- All requests had to go through chicago
  - Rails server running there
- All front end deploys have to go through the rails deploy process

### Solution
- Host Frontend on S3 and CloudFront
- Host Backend whereever

### What are we going to cover
- How to get single page app
  - hosted on AWS
  - distributed globally
  - secured with SSL
  - automatically deployed with CI/CD
  - dynamically responding to requests

### Not going to cover
- Servers
- Serverless
  - See lambda course

## AWS

### Free tier

- Free Forever
  - cloud watch logs
  - lamda - 1mil a month
- Free for  one year
  - cloud front  50gb


### Monitoring
- billing dashboard
  - prefs
    - recieve free tier usage alerts
  - create budget
    - cost

### Security
- IAM
  - _it is a bad idea to use the root account for anything_
  - subaccount structure
    - create a subaccount that replaces root
      - same idea as unix user roles
    - principle of least access
      - only give account the permissions it needs

- secure root account
  - services
    - IAM
  - turn on MFA 

- create IAM user
  - name ( admin )
  - add policies ( attach directly )
    - admin access
    - download access csv
  - set up MFA on admin user as well 

## Storage and hosting with s3

### Intro
- _Can use diff buckets for staging, dev, prod_
- infinitely scaleable
- can be very small or very large

- Features
  - lifecycle management
  - versioning
  - encryption
  - security

- tiers
  - standard
  - infrequent access
  - reduced redundancy
  - glacier

- s3 is basically a key value store, not a real folder structure
- Data consistency model
  - new objects are immidiate and up to date
  - updates are not immidiate

### Registering a domain
- route 53 
  - domain registrations
    - register domain 
    - add to cart
    - continue
    - domain privacy

### Policies
- terms
  - principle == _who_ can do a thing
  - action == _what_ cant hey do
  - resource == to _which_ things can they do it

### what are we going to do
- setup a bucket
- add policy
- configure for static hosting
- upload a react app from command line


### setting up a bucket
- s3
  - create bucket
  - unique name ( must match domain if hosting )
  - keep defaults
  - tags
    - key/value
    - labelling
  - don't keep all versions
  - permissions
    - default
  - review
  - create

- turn on static website hosting
  - use to host website
  - default files
    - index.html
    - error.html
      - bummer with client side routing
  - permissions
    - bucket policy
      - generator
      - json

- areas of improvement
  - bad url
  - manual upload
  - hosted in virginia
  - routing is not great


## The AWS CLI

### Intro to CLI
- install aws cli
- `aws configure`
  - default region [ us-east-1 ]
  - ALWAYS CHOOSE us-east-1 so you have access to global stuff
- supports different profiles

### Deploy to s3 through CLI
- `aws s3 ls [bucket]`
- `aws [service] [action] [item]`
- copy dir to s3
  - `aws s3 cp dist/ [s3://(bucket)] --recursive`

## DNS with route 53

### Intro to route 53
- route 53 is
  - scaleable dns service
  - health check stuff
  - location based server routing

### Setting up DNS
- route 53
- domains
- manage dns
- go to record set
  - create new record set
    - leave blank
    - A-record
    - alias
      - target
        - bucket
    - routing policy
      - simple

### Creating an SSL Cert
- cert manager
- get started
- request pup cert
- domain names
  - website.com
  - www.website.com
- DNS validation or email validation
  - DNS Easier if you are on amazon for everything
- open dropdown
  - create record for route 53

## Routing with s3 and Route 53   

### Client side routing and s3
- s3
  - static website hosting
    - define 404 as index.html 

### Setting up www subdomain
- s3
  - static website hosting
    - redirect requests
    - create new bucket `www.[website-name].com`
    - redirect from www to root
- route 53
  - registered domains
    - website
      - go to record sets
      - create record
      - www.bucket
      - alias to www bucket

## CloudFront
### Creating a cloud front distribution
- Globally distributing your website
- __Eventual__ Consistency

- agenda
  - create a new cloud front distribution
  - point it to our static website on s3
  - add our domain names
  - set up gzipping for assets
  - set default root object

### Re-routing DNS
- create distribution
  - web
  - domain should be full url of s3 bucket ( not the auto complete bucket )
  - redirect to https
  - only allow GET and HEAD
  - Ignore all headers
  - leave stuff on NONE where possible
  - compress objects automatically
  - alternate domain names
  - custom SSL
  - default root object ( index.html )
- Route 53
  - change alias target for cloudfront distribution

### Cloud Front overview
- speeds everything up
- http/2 out of the box
- cloud front will cache s3 assets so less requests
- headers
  - ignored to improve caching
  - some custom ones that might be cool
    - device
    - region

### Error pages with CloudFront
- cloud front distribution
  - error pages
    - custom error response
      - 404 | 403
      - index.html
      - return 200

### Cache Invalidations
- cloud front
  - invalidations
    - each time we deploy
  - invalidate everything
    - *

## Setting up build pipeline
### Introduction to CI/CD with Travis CI
- could use anything ( jenkins / travis / something else )

### setting up a travis config file
- travis dashboard
  - find repo
  - turn on
  - config yaml
    - steve kinney travis gist
  - .travis.yml
  - set up env variables on travis
    - encrypt aws access and secret

### create iam user and policy for travis
- IAM
  - create policy
    - s3
      - write
        - to site bucket
    - cloud front
      - create invalidation
  - create user
    - programmatic access
    - add ci policy
    - download user csv
- add ci user keys to travis dashboard

### Pushing a change with CI



