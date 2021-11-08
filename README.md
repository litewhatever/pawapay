# pawapay
This repository contains home assignment solution.

Following tools are used:
- Vagrant
- Virtualbox
- Ansible

## Running home assignment/lab
1. Install vagrant and virtualbox
2. Clone the repository
3. Create ansible vault password file to decrypt secrets:
```
echo "pass" > vault_pass_local.txt
```
4. Execute `vagrant up`
5. Import CA certiticate to browser truststore to avoid warnings
6. Services should be at:

| Service | URL |
| --- | --- |
| kibana | http://localhost:5601 |

## Notes
- Instances run in GUI mode because Virtualbox has [issues](https://www.virtualbox.org/ticket/20636) on macos monterey. It is possible to disable gui mode in Vagrantfile with `gui_mode` variable.
