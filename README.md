mapr-aws-bootstrap
========

This role uses the ec2 module via a local action to set up a multi-node MapR cluster with one or more non-cluster nodes. The service layout attempts to follow MapR best practices as they become available.

This role will only create the nodes - it will not apply any configuration or install any packages. 

Requirements
------------

1. AWS credentials (account ID and secret key). If you don't know what this means, read this: http://docs.aws.amazon.com/general/latest/gr/getting-aws-sec-creds.html
2. We will provision nodes in a VPC. A VPC must exist, with at least one subnet. You'll need the subnet ID.
3. You will need a base image. This has been tested with the CentOS 6.5 AMI in us-east-1 having ID `ami-8997afe0`.
4. You will need your SSH keypair already generated and available in AWS. You will want to have the PEM file local as well.
5. You will need one or more security groups. The cluster that results from the install_cluster play uses a security group that allows ports 22 and 8443 as well as all traffic originating from the VPC subnet.
6. It might be a good idea to configure your SSH client (assuming you use a *nix like system) so that it automatically uses your AWS private key when connecting to AWS public IPs. Try something like the below, substituting paths as necessary.
```
    Host *.amazonaws.com
    User root
    IdentityFile "~/.ssh/my_keypair.pem"
```


Role Variables
--------------

The following need to be modified from these (personal) defaults. Odds are excellent these will not work at all for you, so you need to supply your own. Others may creep in over time, so be sure to check all the variables.

ec2_keypair: 'vgonzalez_keypair'

ec2_security_group: 'sg-d152d7b4'

ec2_region: 'us-east-1'

ec2_zone: 'us-east-1b'

ec2_image: 'ami-8997afe0'

vpc_subnet: 'subnet-9bfae2ef'


Turn Off - (on-demand instances only)
------------

To set the cluster into a `stopped` state:

```
$ ansible-playbook --extra-vars="ec2_region=<region>" -i playbooks/cluster.hosts playbooks/aws_turnoff.yml
```

Note that you cannot stop spot instances.

Turn On 
------------

To set the cluster into a `running` state:

```
$ ansible-playbook --extra-vars="ec2_region=<region>" -i playbooks/cluster.hosts playbooks/aws_turnon.yml
```

Teardown
------------

When you're finished with the cluster and wish to destroy it, the `aws-teardown.yml` playbook should be used.

This playbook will fail unless the `i_am_sure` variable is set to `True`. It's also necessary to specify the ec2_region in this command:

```
$ ansible-playbook --extra-vars "i_am_sure=True" --extra-vars="ec2_region=us-east-1" -i playbooks/cluster.hosts playbooks/aws_teardown.yml
```

Instance Type Notes
-----------

m1.xlarge with Amazon Linux will have /dev/xvdf mounted. Need to unmount and remove the mount from fstab. The MFS format step will fail otherwise. You can do this with the following ansible invocation:


```
ansible all -i inventory.py -su ec2-user -m mount -a "src=/dev/xvdf state=absent name=/media/ephemeral0 fstype=ext3"
```

Dependencies
------------

python module boto (python AWS library)

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
