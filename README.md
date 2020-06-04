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

Fill in the non-root sudo users username, apt pages in ```vars/default.yml```

## Set up a Droplet on DigitalOcean

Set up a Droplet (a virtual private server) on Digital Ocean. Make sure to copy the IP address

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

## Check the ```initial_server_setup.yml``` playbook for syntax errors

```
ansible-playbook -i hosts --syntax-check initial_server_setup.yml
```

## Run the ansible playbook to complete the initial server setup

```
ansible-playbook -i hosts -u root initial_server_setup.yml
```

## Try to log into the server with the non-root sudo user

Use SSH to log into the server as the non-root sudo user

```
ssh peter@234.XXX.23.XX
```
