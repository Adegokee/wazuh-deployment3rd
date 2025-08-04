# Deploy Wazuh Docker in single node configuration

This deployment is defined in the `docker-compose.yml` file with one Wazuh manager containers, one Wazuh indexer containers, and one Wazuh dashboard container. It can be deployed by following these steps: 

1) Increase max_map_count on your host (Linux). This command must be run with root permissions:
```
$ sysctl -w vm.max_map_count=262144
```
2) Run the certificate creation script:
```
$ docker-compose -f generate-indexer-certs.yml run --rm generator
```
3) Start the environment with docker-compose:

- In the foregroud:
```
$ docker-compose up
```
- In the background:
```
$ docker-compose up -d
```

The environment takes about 1 minute to get up (depending on your Docker host) for the first time since Wazuh Indexer must be started for the first time and the indexes and index patterns must be generated.


## Yo need to  generate certs for filebeat on your home directory 
1) # Generate private key for Root CA
openssl genrsa -out root-ca.key 4096

# Generate Root CA certificate
openssl req -x509 -new -nodes -key root-ca.key -sha256 -days 3650 -out root-ca.pem -subj "/C=US/ST=State/L=City/O=Organization/CN=RootCA"

# Generate private key for Filebeat
openssl genrsa -out filebeat.key 2048

# Generate CSR for Filebeat
openssl req -new -key filebeat.key -out filebeat.csr -subj "/C=US/ST=State/L=City/O=Organization/CN=filebeat"

# Create Filebeat certificate signed by Root CA
openssl x509 -req -in filebeat.csr -CA root-ca.pem -CAkey root-ca.key -CAcreateserial -out filebeat.pem -days 365 -sha256