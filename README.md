# pawapay
This repository contains home assignment solution.

Following tools are used:
- Vagrant
- Virtualbox
- Ansible

## Running home assignment/lab
1. Clone this repository
2. Install vagrant and virtualbox
3. Install ansible and python dependencies (`pip3 install -r requirements.txt`)
4. Execute `vagrant up`
4. Import [CA certiticate](/files/ca.crt) to browser truststore for avoiding warnings
5. Services should be at:

| Service | URL |
| --- | --- |
| kibana | https://localhost:5601 |

## Notes
- Instances run in GUI mode because Virtualbox has [issues](https://www.virtualbox.org/ticket/20636) on macos monterey. It is possible to disable gui mode in Vagrantfile with `gui_mode` variable
- CA key-pair has been generated with openssl:
```
cd files/
CA_NAME=pawapay-assignment-ca
openssl genrsa -out ${CA_NAME}.key 4096
openssl req -x509 -new -nodes -key ${CA_NAME}.key -subj "/CN=${CA_NAME}" -days 3650 -reqexts v3_req -extensions v3_ca -out ${CA_NAME}.crt
```
- logstash role has been forked from geerlingguy.logstash to add TLS support for elasticsearch output
