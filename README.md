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
5. Following services are available:

| Service | URL | Notes |
| --- | --- |
| kibana | https://localhost:5601 | |
| grafana | https://localhost:3000 | Use admin/secret for login |

## Notes
- Instances run in GUI mode because Virtualbox has [issues](https://www.virtualbox.org/ticket/20636) on macos monterey. It is possible to disable gui mode in Vagrantfile with `gui_mode` variable
- CA key-pair has been generated with openssl:
```
cd files/
CA_NAME=pawapay-assignment-ca
openssl genrsa -out ${CA_NAME}.key 4096
openssl req -x509 -new -nodes -key ${CA_NAME}.key -subj "/CN=${CA_NAME}" -days 3650 -reqexts v3_req -extensions v3_ca -out ${CA_NAME}.crt
```
- Logstash role has been forked from geerlingguy.logstash to add TLS support for elasticsearch output
- Logstash default configuration collects `/var/log/syslog` from logstash instance. Data is being pushed to a index which name is not exactly right (metadata tag is not filled as expected). It is still possible to run queries against this index e.g `GET /%%{[@metadata][beat]}-2021.11.08/_search` query. This should be fixed.

## Backup and recovery

### Elasticsearch

1. Register/configure snapshot repository
2. Use snapshot API to create snapshot
3. Use snapshot lifecycle management to create periodic snapshots

### Kibana and logstash

1. Destroy and recreate instances
2. Deploy with ansible

### Prometheus
1. Enable admin API
2. Make a POST request against admin API which creates a snapshot on prometheus instance
3. Copy the file to a safe location

OR (depending if dataloss is acceptable)

1. Destroy and recreate instance
2. Deploy with ansible
