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

==

step-by-step guide to set up a control node (where Ansible is installed) and two target nodes (which Ansible will manage) for practicing Ansible on your Mac.

Since you’re using macOS, you can run these steps with Docker to simulate a control node and target nodes. Alternatively, you could use virtual machines with Vagrant, but Docker is usually simpler.
