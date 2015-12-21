mapr-aws-bootstrap
========

This role uses the ec2 module via a local action to create some number of ec2 instances.

Requirements
------------

1. AWS credentials (account ID and secret key). If you don't know what this means, read this: http://docs.aws.amazon.com/general/latest/gr/getting-aws-sec-creds.html
2. We will provision nodes in a VPC. A VPC must exist, with at least one subnet. You'll need the subnet ID.
3. You will need an image in the region of your choice.
4. You will need your SSH keypair already generated and available in AWS. You will want to have the PEM file local as well.
5. You will need one or more security groups.
6. It might be a good idea to configure your SSH client (assuming you use a *nix like system) so that it automatically uses your AWS private key. When connecting to AWS public hostnames, you can try something like the below, substituting paths as necessary. If you use a VPN to connect to AWS and you use the default DNS, you can substitute `*.ec2.internal` for `*.amazonaws.com`.
```
    Host *.amazonaws.com
    User root
    IdentityFile "~/.ssh/my_keypair.pem"
```


Role Variables
--------------

Configuration is pulled in from a dictionary. Please look at `defaults/main.yml` for an example. Copy that file to `vars/main.yml`, and edit it. In particular, make sure you update your AWS keys, keypair and security groups. The rest have reasonable (for me) defaults.

Dependencies
------------

python module boto (python AWS library) installed locally.

Example Playbook
-------------------------

```
- hosts: localhost
  roles:
    - mapr-aws-bootstrap
```

License
-------

MIT

Author Information
------------------

Vince Gonzalez
