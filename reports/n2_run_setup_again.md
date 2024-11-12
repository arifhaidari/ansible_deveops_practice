## if you are coming again to run after few days after you terminated the services then follow the following instructions

This issue occurs because Docker containers do not persist their state after being stopped and restarted. Each time a container is stopped, any services you started manually (like SSH) are terminated. When you restart your Docker containers, they return to their original state, meaning the SSH service isn’t running on your target nodes anymore.

### Here’s how to fix the issue

1. **Start the SSH Service on the Target Nodes Again**:
   Since the containers were restarted, you need to manually start the SSH service in each target node.

   - First, start a shell session on each target node (replace `target_node1` and `target_node2` as appropriate):

     ```bash
     docker exec -it target_node1 bash
     ```

   - Inside the target node, start the SSH service:

     ```bash
     service ssh start
     ```

   - Repeat these steps for any other target nodes (e.g., `target_node2`).

2. **Verify IP Addresses**:
   After a restart, Docker often assigns new IP addresses to containers. This means the IP addresses you used previously might have changed.

   - Check the current IP addresses of `target_node1` and `target_node2`:

     ```bash
     docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target_node1
     docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target_node2
     ```

   - Update your `hosts.ini` file with the new IP addresses if they have changed.

3. **Retry the SSH Key Copy**:
   Now, you can try the `ssh-copy-id` command again from the control node. This will copy the SSH key to the `ansible` user on the target nodes.

   ```bash
   ssh-copy-id -i /root/.ssh/id_rsa.pub ansible@<updated_target_node1_ip>
   ssh-copy-id -i /root/.ssh/id_rsa.pub ansible@<updated_target_node2_ip>
   ```

4. **Verify SSH Connection**:
   After copying the SSH key, test the connection to ensure you can SSH into each target node without a password:

   ```bash
   ssh ansible@<updated_target_node1_ip>
   ssh ansible@<updated_target_node2_ip>
   ```

### Optional: Automate SSH on Startup

If you’re regularly stopping and starting your containers, consider automating SSH startup by adding a startup command when creating the Docker container or setting up a persistent container setup with `docker-compose`. Alternatively, you could create a simple script that performs these steps for convenience.

To automate the SSH service startup and simplify the container setup for persistence, we can use a docker-compose file. This way, each time you start the containers with docker-compose up, they will run SSH automatically, and you won’t have to start it manually every time
