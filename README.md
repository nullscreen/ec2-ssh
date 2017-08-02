# EC2 SSH

SSH to an EC2 instance by `Name` tag.

## Installation

First install the [AWS CLI][aws-cli]. Then copy or symlink `essh` somewhere on
your `PATH`. See `essh -h` for usage information.

[aws-cli]: https://aws.amazon.com/cli/

## Introduction

```sh
essh [OPTIONS] [SEARCH] [--] [SSH_OPT...]
```

To search for an instance by name, type a portion of the name as the first
argument. The results will be listed in alphabetical order by name.

```sh
$ essh web
1) app-production-web    ip-10-0-0-1.ec2.internal
2) app-production-web    ip-10-0-0-2.ec2.internal
3) web-bastion           ec2-216-3-128-12.compute-1.amazonaws.com
>
```

Then enter the number you want to ssh to. If there is only one result, it will
be selected automatically. EC2 SSH will automatically generate the ssh command
and run it.

```sh
# If you selected 3 above, essh will automatically run
ssh ec2-216-3-128-12.compute-1.amazonaws.com
```

You can also run `essh` with no parameters and it will list all instances.

```sh
$ essh
1) app-production-web    ip-10-0-0-1.ec2.internal
2) app-production-web    ip-10-0-0-2.ec2.internal
3) app-worker            ip-10-0-0-4.ec2.internal
4) web-bastion           ec2-216-3-128-12.compute-1.amazonaws.com
>
```

## AWS Authentication

`-p profile` Specify an AWS CLI profile

EC2 SSH supports the same authentication types as the AWS CLI. This includes the
`/.aws/credentials` file, environment variables, and instance profiles. If you
have multiple CLI profiles configured, you can specify one with the `-p` flag.

```sh
$ essh -p prod web
```

Multiple profiles are allowed if you want to list instances from multiple
accounts.

```sh
$ essh -p prod -p dev web
```

## AWS Regions

- `-r region` Specify an AWS region
- `-R` Search all regions

If you don't specify a region, the default AWS CLI region will be used. To
override the default, use the `-r` flag. Multiple regions can be specified and
the results will be combined. Optionally, use `-R` to search all regions.

```sh
$ essh -r us-east-1 -r us-west-2 web
```

## DNS Types

- `-d` Use private DNS name
- `-D` Use public DNS name

If an instance has a public DNS name, it will be preferred over the private DNS
name. If you need to override that default, use the `-d` flag. This forces all
DNS to private. You might need to do that for instances that have a public IP,
but don't allow SSH from the internet.

Note that you'll need to set up your `~/.ssh/config` file to allow SSHing
through a bastion host if you need to access an instance with only private DNS.

## List Operations

- `-c number` Choose a number instead of showing choices
- `-l` Force showing the list even for only 1 option

## SSH Options

- `-u user` Specify the ssh login user
- `--` Any options after this will be passed to the SSH command

Usually you will want to configure your username in the `~/.ssh/config` file,
but if you need override that, use the `-u` to set your username.

```sh
$ ssh -u ec2-user web
```

To pass other options to SSH, specify them after the search parameter or after
`--`.

```sh
# The -- is only required if you don't pass a search parameter.
$ ssh web -i ~/.ssh/id_rsa
$ ssh -- -i ~/.ssh/id_rsa
```

## License

Copyright 2017 Warner Bros

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
