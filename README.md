# ansible-jupyterhub-digitalocean

A repo of Ansible playbooks to install JupyterHub on a Digital Ocean Server using Ansible. Can only be used on WSL, MacOS or Linux. Does not work on Windows 10.

## Create DO server and connect domain name to it

First create the DigitalOcean server, add domain to DigitalOcean DNS. Link domain name to server (the DNS hookup takes the longest). Note the IP address of the server.

```
ns1.digitalocean.com
ns2.digitalocean.com
ns3.digitalocean.com
```

## Install Ansible Locally

```
python -m venv venv
source venv/bin/activate
python -m pip install -r requirements.txt
ansible --verison
# ansible 2.9.9
```

## Make sure there is a local SSH key

```
cat ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub
# if not present, run $ ssh-keygen -b 4096
```

## Modify ```vars/default.yml```

Fill in the non-root sudo users username, apt packages, etc. in ```vars/default.yml```

```
cp vars/default_example.yml vars/default.yml
```

## Create ```hosts``` file

```
cp hosts_example hosts
```

Add the Digital Ocean Droplet IP address into the ```hosts``` file.

```
ansible -i hosts --list-hosts all
# verify the IP address of the server
```

## Ping the DO server

Make sure you can connect to the Digital Ocean server:

```
ansible -i hosts -m ping all
# pong
```

If this is the first time communicating with the server, it will ask you to verify the host key. Answer ```yes```.

If this doesn't work. Try to log into the server with SSH. May need to remove a known host ```ssh-keygen -f "/home/peter/.ssh/known_hosts" -R "XX.XXXX.XX"``

## Run the ansible playbook to complete the initial server setup

```
ansible-playbook -i hosts initial_server_setup.yml
```

## Try to log into the server with the non-root sudo user

Use SSH to log into the server as the non-root sudo user

```
ssh peter@234.XXX.23.XX
```

## Run the miniconda_install.yml playbook

```
ansible-playbook -i hosts miniconda_install.yml
```

## Log into the server and see if conda works

```
ssh peter@XXX.XXXX.XXXX
conda --version
# conda 4.8.2
```

May have to run ```conda init``` and ```source ~/.bashrc```

## Run the jupyterhub_install.yml playbook

```
ansible-playbook -i hosts jupyterhub_install.yml
```

## Log into server and try to activate the jupyterhubenv and type a Python command

```
ssh root@XXX.XX.XXX.X
conda activate jupyterhubenv   # might have to type $ conda init && source .bashrc
python
>>> import numpy
>>> numpy.__version__
>>> exit()
cd /etc/jupyterhub 
jupyterhub          # need to be root (b/c of key files)
# ctrl-c to exit
```

## Link up domain name to server

The next step requires a domain name is hooked up to the server. Once the hookup is made, it can take some time for the DNS records to propigate. Can look at https://www.whatsmydns.net/ to see if changeover is compete.

## Get the SSL Cert

```
ansible-playbook -i hosts ssl.yml
```

## Nginx installation, configuration

```
ansible-playbook -i hosts nginx_install.yml
```

## View the running installation

Browse to your domain name

Could create a new user on the server over SSH and use the new user's name and password to log into Jupyterhub

## Google OAuth

Log into the Google Developer Console and hook up the domain as a known site. 

 - API & Services --> Domain Verification --> Add Domain. Your domain should be listed. (can take a bit)
 - Credentials --> + Create Credentials --> OAuth Client ID
   - Application Type: Web application
   - Authorized JavaScript origins: https://mydomain.org
   - Authorized redirect URIs: https://mydomain.org/hub/oauth_callback
   - Create
 - Save keys as json using the download button to the right of the keys
 - move the key file into the ```vars``` directory and save as ```client_secret.json```


Then run the ```google_oauth.yml``` playbook

```
ansible-playbook -i hosts google_oauth.yml
```

You should now be able to log into your your JupyterHub server with google usernames and passwords
