# ansible-jupyterhub-digitalocean

A repo of Ansible playbooks to install JupyterHub on a Digital Ocean Server using Ansible. Can only be used on WSL, MacOS or Linux. Does not work on Windows 10.

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

## Set up a Droplet on DigitalOcean

Create a Droplet (a virtual private server) on Digital Ocean. Make sure to copy the IP address.

## Create ```hosts``` file

```
cp hosts_example hosts
```

Add the Digital Ocean Droplet IP address into the ```hosts``` file.

## Ping the DO server

Make sure you can connect to the Digital Ocean server:

```
ansible -i hosts --list-hosts all
# verify the IP address of the server

ansible -i hosts -m ping all
# pong
```

If this is the first time communicating with the server, it will ask you to verify the host key. Answer ```yes```.

If this doesn't work. Try to log into the server with SSH. May need to remove a known host ```ssh-keygen -f "/home/peter/.ssh/known_hosts" -R "XX.XXXX.XX"```

## Check the ```initial_server_setup.yml``` playbook for syntax errors

```
ansible-playbook -i hosts --syntax-check initial_server_setup.yml
```

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

## Run the jupyterhub_install.yml playbook

```
ansible-playbook -i hosts jupyterhub_install.yml
```

## Log into server and try to activate the jupyterhubenv and type a Python command

```
ssh peter@XXX.XX.XXX.X
conda activate jupyterhubenv   # might have to type $ conda init && source .bashrc
python
>>> import numpy
>>> numpy.__version__
>>> exit()
```

## Link up domain name to server

The next step requires a domain name is hooked up to the server. Once the hookup is made, it can take some time for the DNS records to propigate.

```
ansible-playbook -i hosts nginx_and_ssl.yml
```

## View the running installation

browse to your domain name

## Google OAuth
