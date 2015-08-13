# Flysystem Adapter for Runabove Object Storage.

[![Author](http://img.shields.io/badge/author-@tdutrion-blue.svg?style=flat-square)](https://twitter.com/tdutrion)
[![Build Status](https://img.shields.io/travis/engineor/flysystem-runabove/master.svg?style=flat-square)](https://travis-ci.org/engineor/flysystem-runabove)
[![Coverage Status](https://coveralls.io/repos/engineor/flysystem-runabove/badge.svg?branch=master&service=github&style=flat-square)](https://coveralls.io/github/engineor/flysystem-runabove?branch=master)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)
[![Packagist Version](https://img.shields.io/packagist/v/engineor/flysystem-runabove.svg?style=flat-square)](https://packagist.org/packages/engineor/flysystem-runabove)
[![Total Downloads](https://img.shields.io/packagist/dt/engineor/flysystem-runabove.svg?style=flat-square)](https://packagist.org/packages/engineor/flysystem-runabove)


## Installation

```bash
composer require engineor/flysystem-runabove
```

## Usage

See configuration section for credential details.

```php
use Engineor\Flysystem\Runabove;
use Engineor\Flysystem\RunaboveAdapter as Adapter;
use League\Flysystem\Filesystem;

$client = new Runabove([
   'username'  => ':username',
   'password'  => ':password',
   'tenantId'  => ':tenantId',
]);

$store = $client->objectStoreService('swift', Runabove::REGION_EUROPE);
$container = $store->getContainer('flysystem');

$filesystem = new Filesystem(new Adapter($container));
```

Alternatively:

```php
use Engineor\Flysystem\Runabove;
use Engineor\Flysystem\RunaboveAdapter as Adapter;
use League\Flysystem\Filesystem;

$options = [
    'username'  => ':username',
    'password'  => ':password',
    'tenantId'  => ':tenantId',
    'container' => 'flysystem',
    'region'    => Runabove::REGION_EUROPE,
];

$client = new Runabove($options);

$filesystem = new Filesystem(new Adapter($client->getContainer()));
```

## Configuration

To get the required credentials, you need to authenticate and download the credentials file from the Runabove Horizon portal:

[https://cloud.runabove.com/horizon/project/access_and_security/api_access/openrc/](https://cloud.runabove.com/horizon/project/access_and_security/api_access/openrc/)

You will then receive a file similar to the following content:

```bash
#!/bin/bash

# With the addition of Keystone, to use an openstack cloud you should
# authenticate against keystone, which returns a **Token** and **Service
# Catalog**.  The catalog contains the endpoint for all services the
# user/tenant has access to - including nova, glance, keystone, swift.
#
# *NOTE*: Using the 2.0 *auth api* does not mean that compute api is 2.0.  We
# will use the 1.1 *compute api*
export OS_AUTH_URL=https://auth.runabove.io/v2.0

# With the addition of Keystone we have standardized on the term **tenant**
# as the entity that owns the resources.
export OS_TENANT_ID=********************************
export OS_TENANT_NAME="**********"

# In addition to the owning entity (tenant), openstack stores the entity
# performing the action as the **user**.
export OS_USERNAME="********@********.***"

# With Keystone you pass the keystone password.
echo "Please enter your OpenStack Password: "
read -sr OS_PASSWORD_INPUT
export OS_PASSWORD=$OS_PASSWORD_INPUT
```

You can retrieve your `username` and `tenantId` from the file using the following table:

| PHP settings | Credential file variable |
| ------------ | ------------------------ |
| `username`   | `OS_USERNAME`            |
| `tenantId`   | `OS_TENANT_ID`           |

Your password is the one you are using to login on Runabove, associated to the `username` email address.

For the region, 3 of them are currently available (`SBG-1`, `BHS-1` and `HZ1`). As `HZ1` is currently for specific VPS instances only, this library only contains constants for `REGION_EUROPE` (`SGB-1`) and `REGION_US` (`BHS-1`).

Finally, the container ID is the name of the object storage container you want to target.