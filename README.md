# puppet-agent-build

This repository is designed to automate the process of building puppet-agent from the Puppetlabs puppet-agent repository.

## Requirements

 * ansible
 * rvm

## How To

The prerequisite to using this is that you have a host with SSH access. With that assumption, for an EL7 host with hostname "el7.example.com". First create an ansible inventory file with this host in it:

`build_inventory`
```
[build_host]
el7.example.com ansible_user=root el_version=7 el_arch=x86_64

[localhost]
localhost ansible_connection=local
```

NOTE: You may also need to supply information such as the SSH key to use or SSH username and password to connect to the box.

Now that we have our box and inventory configured, we can prep the box with necessary build libraries:

```
yum install ansible
git clone https://github.com/ehelms/puppet-agent-build
cd puppet-agent-build

ansible-playbook -i build_inventory prep_box.yaml
```

Lastly, we run the build playbook that creates and exports the SRPM to the local machine.

```
ansible-playbook -i build_inventory build.yaml
```

## SRPM_Foundry

To request a Beaker machine you'll need a valid kerberos ticket (from `kinit`) and the beaker cli already set up.

```
# in the puppet-agent-build dir
$ bundle exec pry -r./lib/puppet_agent_build.rb
pry> foundry = PuppetAgentBuild::Foundry.new({'RedHatEnterpriseLinux7' => [:x86_64]})
# => <new object>
pry> foundry.prep_for_ansible
# when finished run ansible commands in another shell
pry> foundry.shutdown # releases the beaker machines
```
