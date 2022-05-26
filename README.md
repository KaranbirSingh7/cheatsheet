# cheatsheet
CheatSheet for day-to-day tasks


### Python

Start python server in oneline: 
```sh
python3 -m http.server 8080
```

### Bash

Query website/endpoint every 10 secs:
```sh
while true; do curl -s https://google.com/; sleep 10;  done;
```


### OpenSSL


Check expiry of a cert on remote domain:
```bash
openssl s_client -connect google.com:443 -servername google.com 2> /dev/null | openssl x509 -dates -noout

# check SANs + expiry as well
openssl s_client -connect google.com:443 -servername google.com 2> /dev/null | openssl x509 -text -noout | grep "DNS\|Before\|After"
```


Verify if public certificate and private key matches:
```bash
openssl x509 -in domain.public.crt -noout -modulus  | openssl md5
openssl rsa -in domain.private.key -noout -modulus  | openssl md5
```

Decode a pkcs12 cert bundle file
```sh
openssl pkcs12 -info -in INFILE.p12 -nodes
```

### Bosh

Get manifest of current deployment:
```bash
bosh manifest -d $deployment > /tmp/$deployment.yaml
```

List running/processing tasks
```bash
bosh tasks
```

Debug bosh task
```bash
bosh task 12345 --debug
```

Cancel running/queued task
```bash
bosh cancel-task 12345
```

Check if cloud CPI is good with bosh (in-sync)
```bash
bosh cck
```


Restart VM processes only, NOT a complete reboot
```bash
bosh -d $deployment restart UUID/cell_UUID
```


Get Memory Usage info for deployment VMs:
```bash
bosh -d $DEPLOYMENT vms --vitals --json | jq ".Tables[0].Rows[] | { memory_usage: .memory_usage, instance: .instance }"
```

### CredHub

Find a secret:
```bash
credhub find -n /p-bosh/concourse/
```

Get a secret:
```bash
credhub get -n /p-bosh/concourse/myapp_password
```

Get specific key on a secret:
```bash
credhub get -n /p-bosh/concourse/credhub_ca -k certificate
```

Import secret from YML:
```bash
credhub import --file secrets.yml
```

Export Secret to YML:
```bash
credhub export --file secrets-$(date +%F).yml
```

Delete a secret: 
```bash
credhub delete -n /p-bosh/concourse/myapp_password
```

### Knife (Chef)

Show/list cookbooks from current run_list

```bash
knife node show `hostname -s`
```

Add new cookbook to run_list
```bash
knife node run_list add `hostname -s` recipe[linux_artifactory@X.X.X]
```

Remove cookbook from existing run_list
```bash
knife node run_list remove `hostname -s` recipe[linux_artifactory@X.X.X]
```

#### Knife Databag (Chef)
Create new key for encrypting data bags 
```sh
openssl rand -base64 512 | tr -d '\r\n' > /tmp/databag_secret
```

Create new data bag (encrypted)

```bash
knife data bag create database database_secrets --secret-file ./databag_secret -z
```

Edit existing data bag (encrypted)

```bash
knife data bag edit database database_secrets --secret-file ./databag_secret -z
```

Show contents of data bag (encrypted)

```bash
knife data bag show database database_secrets --secret-file ./databag_secret -F json -z
```

Create new data bag from json file (encrypted)

```bash
knife data bag from file database ./database/database_secrets.json --secret-file ./databag_secret -z
```

### chef-client (Chef)

Run chef-client in debug mode

```bash
chef-client -l debug
```

### npm

Run npm install with verbose logging
```sh
npm install nodemon --loglevel verbose
```


### JFrog Artifactory 

Monitor logs when artifactory starts up:
```sh
tail -f /var/opt/jfrog/artifactory/log/console.log
```

Uninstall artifactory coompletely and delete all data from disk:
```sh
apt-get remove jfrog-artifactory-pro -y
rm -rf /opt/jfrog/artifactory
rm -rf /var/opt/jfrog/artifactory
```
