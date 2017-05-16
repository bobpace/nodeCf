## Overview
Simple package to help with Cloudformation deployments

### Goals
* Make it easy to deploy multi-stack Cloudformation templates
* Encourage multi-environment deployments
* Promote use of 'native' Cloudformation templates (i.e., with minimal pre-processing)


### Installation
Requires:
* nodejs v7.10.0 or later
* npm

Add the github link as a npm dependency to your local repo. Snippet from package.json:
```
"dependencies": {
    "nodecf": "git+https://github.com/jaymell/nodeCf.git#v.9.0"
  }
```

### Usage
```
node_modules/.bin/nodeCf <ENVIRONMENT> [ ACTION ] [ -r <REGION> ] [ -p <PROFILE> ] [ -s,--stacks <STACK NAMES> ]
```

Run deployment against specified ENVIRONMENT. 

ACTION default to 'deploy': choices are 'deploy', 'delete', and 'validate'
REGION specifies the desired AWS Region. Currently defaults to 'us-east-1'
PROFILE specifies an optional name for an AWS profile to assume when running the job
STACK NAME corresponds to the name of your Cloudformation templates

### Template Files
Cloudformation templates must be stored under ./templates in either json or yaml format.

### Config Files
Config files must be written in yaml and by default are looked for in ./config -- e.g., './config/Dev.yml'

Required config files:
* ./config/global.yml -- global (i.e., not environment-specific) variables
* ./config/\<ENVRIONMENT\>.yml -- environment-specific variables
* ./config/stacks.yml -- specifies the variables that get passed to Cloudformation as parameters

Required variables:
* account
* environmeent
* application
* infraBucket

Example stacks.yml:
```
---
stacks:
- name: network
  parameters:
  - VpcIPRange: "{{VpcIPRange}}"
  - PrivateSubnet0: "{{PrivateSubnet0}}"
  - PrivateSubnet1: "{{PrivateSubnet1}}"
- name: rds
  parameters:
  - NetworkStack: "{{environment}}-{{application}}-network"
  - PrivateSubnet0: "{{PrivateSubnet0}}"
  - PrivateSubnet1: "{{PrivateSubnet1}}"
```

Example env.yml:
```
---
account: "{{accounts.production}}"
infraBucket: myUniqueBucketname # required -- nodeCf will attempt to create it if it doesn't exist
VpcIPRange: 10.0.0.0/8
PrivateSubnet0Cidr: 10.0.0.0/24
PrivateSubnet1Cidr: 10.0.1.0/24
```

Example global.yml:
```
---
application: MyApplication
accounts:
  production: <MY AWS Account number -- e.g., 123456789012>
```

### TO DO
* Print progress of deployments
* Use change sets
* Make it easy to set stack update policies
* Delete files from s3 after deployments?
* Add more unit tests
* Fix bugs
* Add example project
