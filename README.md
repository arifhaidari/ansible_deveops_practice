# Ansible DevOps Practices (Under Development)

following texts are draft (notes) during development. I will provide the structured one at the end

This is part of the bootcamp conducted by DataScientest.

now i want create a control node and two target node. and perform the following instructions step by step.
Create a user to be used by Ansible for connecting to target nodes. These users need to be created in all the target nodes which we want to manage.
Provide sudo access to the user.
Setup a password for Ansible user
Generate ssh key for ansible user on the Control Node.
Copy ssh ID to all the target nodes.
Check if we are able to connect to Target Nodes from Control Node.

---

step-by-step guide to set up a control node (where Ansible is installed) and two target nodes (which Ansible will manage) for practicing Ansible on your Mac.

Since you’re using macOS, you can run these steps with Docker to simulate a control node and target nodes. Alternatively, you could use virtual machines with Vagrant, but Docker is usually simpler.

---

Technologies and methods used:
HTTPD:
HTTPd is a software program, that usually runs in the background, as a process. It plays the role of server in a client-server model using HTTP and/or HTTPS network protocols. HTTPd waits for the incoming client requests and for each request it answers by replying with requested information

Playbooks:
Ansible Playbooks are lists of tasks that automatically execute for your specified inventory or groups of hosts. One or more Ansible tasks can be combined to make a play—an ordered grouping of tasks mapped to specific hosts—and tasks are executed in the order in which they are written.
