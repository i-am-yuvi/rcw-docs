---
title: Swarm Based Deployment
---

<head>
  <title>Environment Setup</title>
  <meta
    name="description"
    content="Here we'll deploy our pipeline in the Docker Container"
  />
</head>

Here we'll deploy our pipeline in the Docker Container using compose.

## Pre-requisites:
- Jenkins Server for running pipelines
- Hashicorp Vault for storing secrets
- Docker Swarm (This could be your localhost as well)
- Private Docker Registry
- Ansible for deploying swarm nodes

## Setting up the Pipeline
- ### **Clone the repository**
```
git clone https://github.com/SamagraX-RCW/devops.git
```


- ### **Run the scripts to install Docker, docker-compose & Ansible** 
```
chmod +x setup.sh
./setup.sh
```
<!-- - Get your SSL key from CA(Certified Authority) and paste it inside the ssl certificate(docker-registry.crt) -->

- ### **Now run the compose file to deploy Jenkins, registry & vault** 
```
docker compose up -d
```

- ### **Configure Vault token**

  - **Configure the script(set_env.sh) if you want to change the registry** username/password

  ![registry credentials image](../assets/registry_cred.png)

  ***Note: This will store the registry username & password inside the vault***

  - **Save the inital root token & unseal keys **Securely** for future use-cases**

  - *Note:This script will mount the file containing the Registry Username & Password inside the Jenkins container for pushing to registry*

  - **Now run the Script**
    ```
    chmod +x ./scripts/set_env.sh
    ./scripts/set_env.sh
    ```

- ### **Configure Jenkins Credentials for Private Registry**
  **Update the Credentials in Jenkins:** 

  Dashboard > RCW > deploy-staging > Credentials > docker-server > Update with **https://nginx-reverse-proxy:80 -u <registry_username> -p <registry_password>**, use port 443 if you are using SSL



- ### **SSL Configuration for Nginx**(Optional)
  - **Copy the SSL certificates and paste it inside the *nginx_config/ssl* folder**

  - **Now run the script**
    ```
    chmod +x ./scripts/set_up_ssl.sh
    ./scripts/set_up_ssl.sh
    ```
  
  - ***This script will store the ssl certs content inside the Vault as KV(key value) and keep as environment variable inside the Nginx container***

- ### **Deploy Swarm and other Services**
  - **Copy the hostname and paste in inside the inventory/hosts folder**

  - **Copy this command the paste this inside the script section inside the jenkins job**

  ```
  ansible-playbook -i ./ansible_workspace_dir/inventory/hosts ./ansible_workspace_dir/main.yml
  ```

  - **Now Jenkins will run ansible playbook after the build has been successful**



## Adding Ansible Roles for Services

- ### **Run the Script**

```
chmod +x /scripts/roles.sh
./scripts/roles.sh
```

- **Give the name of the role**

    eg. monitoring


- **Now give the variables for that role**

    eg. no. of replicas : 1/2/3