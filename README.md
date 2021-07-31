These playbooks are for orchestrating the creation and use of cloud-init VM templates with Proxmox VE.

- `setup-template.yml`: this ansible playbook handles all of the steps to create a cloud-init template from a freshly installed OS. 
- `create-vm.yml`: this ansible playbook handles all of the steps needed to create and push a custom cloud-init configuration to the PVE node, clone a template VM, and apply the cloud-init configuration.

## setup-template

This playbook assumes that it will be running with root priviledges. The playbook will install several packages (including cloud-init), set the neccessarry cloud-init config files, and then push a "finishing script" to the host. The finishing script simply runs `cloud-init clean`, locks the root account, and shuts down the virtual machine. This is particularly useful if you do not want any users pre-loaded in the template VM (i.e. you only want users added later via cloud-init) but still want the root account to be locked. After running the playbook, you will login as root, remove any other users (if neccessarry) and run the script. Then the machine will be ready to use with the next part.

## create-vm

This playbook will work to clone any template VM that is configured with cloud-init, not just one created above. See the `templates/` folder for an example cloud-init user-data template. Currently the playbook only pushes a user-data cloud-init file, but the playbook can be easily expanded to include network and metadata files, if needed.

The playbook assumes that the same credentials that can be used to login over SSH to the PVE node can also be used for the REST interface utilized by the proxmox_kvm module. If this is not the case, you should be able to update the playbook accordingly.

The below command represents a "one-touch" command to create a VM from a template on the PVE node. The PVE connection details are in the inventory and vm specific details are in the my_vars.yml file.

``` sh
ansible-playbook -i inventory.yml --ask-vault-pass -e '@my_vars.yml' create-vm.yml
```
