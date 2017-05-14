puppet-automation
=================

Provision Puppet master for future puppet work

puppet-master
-------------

Currently split into two parts, stack and deploy.

*puppet-master-stack*

Provision and configure a puppet environment in aws. A single ec2 instance is provisioned and
assigned to a elb.

```bash
make puppet-master-stack env=<environment-name> profile=<aws-profile> region=<aws-region>
```

This will create a `puppet-<env>-master` ec2 instance (if none already exist), create elb
instance and register ec2 hosts to the elb in the `<aws-profile>` account within the
`<aws-region>` region.

*puppet-master-deploy*

Configure a puppet master host with required packages and libraries, deploy latest configuration
and ensure service is running correctly.

```bash
make puppet-master-deploy env=<environment-name> profile=<aws-profile> region=<aws-region>
```

This will update any hosts in the `<aws-profile>` account within the `<aws-region>` region
with the following tags:

- Service :: puppet
- Role :: master
- Environment :: <environment-name>

edit-secrets
------------

Within this repository you will find `./vars/secrets.yml`, an ansible-vault encrypted file.

*The password for this file has not been included in this repository.*

*vault structure*

```yml
ssh_keys:
  orchestration:
    public:
      <profile>: <public-ssh-key>
    private:
      <profile>: <private-ssh-key>

dyn_ssh_orchestration_key: "{{ssh_keys['orchestration']['private'][profile]}}"
```

The purpose of this file is to manage and maintain your ssh keys. You can set keys based
on the `aws-profile` you're using or via some other lookup method.

`dyn_ssh_orchestration_key` will be used to create an `ec2-key` - you will need the related
public key to connect to provisioned hosts.

questions
---------

*How do I unlock the vault? Where are the secrets and keys?*

See the `edit-secrets` section above.

*Are there any defaults when running playbooks via the Makefile?*

When actioning playbooks via the Makefile, defaults are available for profile (`dev`) and 
region (`us-west-2`).

*What is wrong with `meta_ssh_all_list`?*

`meta_` values should be discovered and not pre-defined. This should either be hard-coded to
your own IP list or discovered via a lookup task/role.
