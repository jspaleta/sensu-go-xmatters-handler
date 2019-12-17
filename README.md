
[![Bonsai Asset Badge](https://img.shields.io/badge/sensu-go-xmatters-handler-Download%20Me-brightgreen.svg?colorB=89C967&logo=sensu)](https://bonsai.sensu.io/assets/toozej/sensu-go-xmatters-handler) [![TravisCI Build Status](https://travis-ci.org/toozej/sensu-go-xmatters-handler.svg?branch=master)
](https://travis-ci.org/toozej/sensu-go-xmatters-handler)

# Sensu Go xMatters Plugin

Table of Contents

- [Overview](#overview)
- [Usage examples](#usage-examples)
- [Configuration](#configuration)
  - [Asset registration](#asset-registration)
  - [Asset configuration](#asset-configuration)
  - [Resource configuration](#resource-configuration)
- [Functionality](#functionality)
- [Installation from source and contributing](#installation-from-source-and-contributing)

## Overview
Sensu Go handler to send paging events to xMatters such that on-call engineers receive notifications of issues in their infrastructure. 

This plugin is based almost entirely off [sensu-hangouts-chat-handler](https://github.com/betorvs/sensu-hangouts-chat-handler)

## Usage Examples
### Help

```
Usage:
  sensu-xmatters-handler [flags]

Flags:
  -h, --help                     help for sensu-xmatters-handler
  -w, --webhook string           The Webhook URL, use default from XMATTERS_WEBHOOK env var
  -a, --withAnnotations string   The xMatters handler will parse check and entity annotations with these values. Use XMATTERS_ANNOTATIONS env var with commas, like: documentation,playbook
```

## Configuration

### Asset Registration

Assets are the best way to make use of this plugin. If you're not using an asset, please consider doing so! If you're using sensuctl 5.13 or later, you can use the following command to add the asset: 

`sensuctl asset add toozej/sensu-go-xmatters-handler:0.1`

If you're using an earlier version of sensuctl, you can find the asset on the [Bonsai Asset Index](https://bonsai.sensu.io/assets/toozej/sensu-go-xmatters-handler).

### Asset configuration

Example asset manifest:

```yml
---
type: Asset
api_version: core/v2
metadata:
  name: sensu-go-xmatters-handler
spec:
  url: https://CHANGEME
  sha512: CHANGEME
```

### Resource configuration

Example resource definition:

```yml
---
api_version: core/v2
type: Handler
metadata:
  name: sensu-go-xmatters-handler
  namespace: default
spec:
  type: pipe
  command: 'sensu-go-xmatters-handler -w "${XMATTERS_WEBHOOK}"'
  timeout: 0
  filters:
    - is_incident
    - not_silenced
  env_vars:
    - XMATTERS_WEBHOOK=https://companyname.na5.xmatters.com/api/integration/1/functions/aaa-bbb-ccc/triggers?apiKey=xxx-yyy-zzz
  runtime_assets:
    - sensu-go-xmatters-handler
```

## Functionality

Requires the env_var "XMATTERS_WEBHOOK" to be set to a valid inbound xMatters generic webhook URL like "https://companyname.na5.xmatters.com/api/integration/1/functions/aaa-bbb-ccc/triggers?apiKey=xxx-yyy-zzz"

Optionally supply an env_var "XMATTERS_ANNOTATIONS" to be set to a string containing Sensu Go check and/or entity annotations like "documentation"

This repo supplies four example event files: a warning event and resolved event pair, and a warning event and resolved event pair including additional annotations. To see what the JSON object POSTed to the configured xMatters webhook endpoint looks like for each of these events, you can run the below Bash one-liner:

```bash
for file in `ls ./example-*.json`; do cat ${file} | /usr/local/bin/sensu-go-xmatters-handler -w https://example.com -d; done
```


## Installation from source and contributing

The preferred way of installing and deploying this plugin is to use it as an [asset][2]. If you would like to compile and install the plugin from source or contribute to it, download the latest version of the sensu-go-xmatters-handler from [releases][1] or create an executable script from this source.

From the local path of the sensu-go-xmatters-handler repository:

```
go build -o /usr/local/bin/sensu-go-xmatters-handler main.go
```

For more information about contributing to this plugin, see https://github.com/sensu/sensu-go/blob/master/CONTRIBUTING.md

[1]: https://github.com/toozej/sensu-go-xmatters-handler/releases
[2]: #asset-registration
