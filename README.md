
## Editing host file:


The following variables are used to collect information about host server.
#### - general variable about host server
- ansible_ssh_user: host server user name
- ansible_ssh_pass: password of host server
- ansible_sudo_pass: sudo password of host server

- server_type: snaplogic or kafka, this will triger the right code block to execute
    
- store_directory: the new directory will be created on host server to store files (cer, key, jks, ...)

- old_certificate_path: old certificate path, will be replaced with new certificate. Based on the tutorial, it is stored at /etc/snaplogic/jcc-serverkeys.jks (for snaplogic) or /var/ssl/private/kafka.itg.keystore.jks (for Kafka ITG Colo1 and Colo2 brokers and C3)

- common_name: common name of the server
- alias_name: or friendly name of the certificate

#### - for SnapLogic server only
- storekey_pass_path: certificate password file path - certificate will be created by using this password. Based on the tutorial, it is stored at /etc/snaplogic/jcc-serverkeys.pass
- old_trustca_path: old TrustCA path, will be updated with the new certificate
- production: set to True if working with actual server. This will put the server into maintenance mode and switch out the old certificate

#### - for Kafka server only
- storekey_pass: password for storekey, enter manually, this pass will be stored on Vault
- restart_server: set to True if the server needs to restart

#### - for phase 1 and Vault path
- local_store_directory: location to store or temporary store new created files (scr, key)
- delete_local_file: set to True if you want to delete local files after creation. Those files are still being stored on Vault
- ansible_become_pass: password of localhost sudo
- VAULT_ADDR: default at https://vault.docker.hpecorp.net:443, stores information at HPE HashiCorp Vault, user doesn't have to change unless want to store secrets at a different vault:
- vault_directory: the directory where secret will be store on Vault
- vault_token: token access to Vault (generated from Github)
- HPE_Private_Root_vault: directory where HPE Private CA and Root CA is stored



## Tutorial to run
Enter all of the fields in the host inventory (hosts.yml)

#### Phase 1:
phase_1.yml contains all important tasks pre signing
    ```ansible-playbook phase_1.yml -i hosts.yml```

#### Intermediate phase:
- Copy the csr from the Vault store directory (vault_directory)
    
- Obtain a certificate with that csr. Remember to add SAN when obtaining the new certificate.

- Copy and paste the certificate back into Vault store directory. Replace 'place_holder' in ca.cer (for Snaplogic) or in kafka-itg.its.hpecorp.net.pem (for Kafka)
    - At this step, please copy and paste the content so that there is no empty line at the beginning. For ending, have an extra line (\n) after '-----END CERTIFICATE-----'

#### Phase 2:
phase_2.yml contains all important tasks post signing
    ```ansible-playbook phase_2.yml -i hosts.yml```
    
Manual tutorial can be found here:
[Kafka](https://github.hpe.com/IntegrationFabric/MPaaS-Platform/wiki/hoh-kafka-certificate-renewal)
[SnapLogic](https://github.hpe.com/SnapLogic/admin/wiki/Update-Server-Certificates)
    
