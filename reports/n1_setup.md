# Project Step By Step Report

Here’s a step-by-step guide to set up a control node (where Ansible is installed) and two target nodes (which Ansible will manage) for practicing Ansible on your Mac.

Since you’re using macOS, you can run these steps with Docker to simulate a control node and target nodes. Alternatively, you could use virtual machines with `Vagrant`, but Docker is usually simpler.

### Step 1: Set Up Control and Target Nodes

If you don't already have Docker installed, download [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop).

Once Docker is installed, create three containers: one for the control node and two as target nodes.

1. **Create a Control Node (Ansible Installed)**:

   ```bash
   docker run -dit --name control_node -v $(pwd):/ansible -w /ansible ubuntu:latest
   docker exec -it control_node bash
   ```

2. **Install Ansible on the Control Node**:
   Inside the control node container, install Ansible and `sudo`:

   ```bash
   apt update
   apt install -y ansible sudo ssh
   ```

3. **Create Target Nodes**:

   ```bash
   docker run -dit --name target_node1 ubuntu:latest
   docker run -dit --name target_node2 ubuntu:latest
   ```

4. **Set Up SSH on Target Nodes**:
   For each target node, install SSH:

   ```bash
   docker exec -it target_node1 bash
   apt update
   apt install -y sudo ssh
   exit
   ```

   Repeat for `target_node2`.

### Step 2: Create an Ansible User on Target Nodes

1. **Create the User**:
   On each target node, create a user named `ansible`:

   ```bash
   docker exec -it target_node1 bash
   useradd -m -s /bin/bash ansible
   ```

2. **Provide sudo Access**:
   Give `ansible` sudo privileges by adding it to the sudoers file.

   ```bash
   echo "ansible ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
   ```

3. **Set Up a Password for the Ansible User**:
   Set a password for `ansible`. This will be required for SSH setup.

   ```bash
   echo "ansible:your_password_here" | chpasswd
   ```

4. **Repeat for the Second Target Node**:
   Repeat steps 1-3 for `target_node2`.

### Step 3: Generate SSH Key for the Ansible User on the Control Node

1. **Switch to Control Node**:

   ```bash
   docker exec -it control_node bash
   ```

2. **Generate an SSH Key**:
   Generate an SSH key pair for secure, password-less access.
   ```bash
   ssh-keygen -t rsa -b 2048
   ```
   Save it in `/root/.ssh/id_rsa` and do not set a passphrase when prompted.

### Step 4: Copy the SSH Key to All Target Nodes

1. **Copy the Key to `target_node1`**:
   Use SSH copy to copy the key over to `ansible` on the target node.

   ```bash
   ssh-copy-id -i /root/.ssh/id_rsa.pub ansible@<target_node1_ip>
   ```

2. **Copy the Key to `target_node2`**:
   Similarly, copy the key to `target_node2`:
   ```bash
   ssh-copy-id -i /root/.ssh/id_rsa.pub ansible@<target_node2_ip>
   ```

### Step 5: Verify Connectivity from Control Node to Target Nodes

1. **Test SSH Connection**:
   From the control node, try to SSH into each target node using the `ansible` user to ensure the setup is correct:

   ```bash
   ssh ansible@<target_node1_ip>
   ssh ansible@<target_node2_ip>
   ```

2. **Check Ansible Connection**:
   Now, create an inventory file (`hosts.ini`) in the `/ansible` directory on the control node:

   ```ini
   [targets]
   target_node1 ansible_host=<target_node1_ip> ansible_user=ansible
   target_node2 ansible_host=<target_node2_ip> ansible_user=ansible
   ```

   Run a test ping to both nodes:

   ```bash
   ansible -i hosts.ini targets -m ping
   ```

If everything is set up correctly, you should see a "pong" response, confirming that the control node can connect to both target nodes using Ansible.

---

Here's a brief explanation of each command:

1. **Docker Commands**

   - **`docker run -dit --name control_node -v $(pwd):/ansible -w /ansible ubuntu:latest`**

     - `docker run`: Creates and starts a new Docker container.
     - `-d`: Runs the container in detached mode (in the background).
     - `-i`: Keeps STDIN open, allowing interaction if needed.
     - `-t`: Allocates a pseudo-TTY (terminal).
     - `--name control_node`: Assigns the name `control_node` to the container.
     - `-v $(pwd):/ansible`: Mounts the current directory (`$(pwd)`) on your Mac to the `/ansible` directory in the container, allowing you to access files from your Mac in the container.
     - `-w /ansible`: Sets `/ansible` as the working directory in the container.
     - `ubuntu:latest`: Specifies the Ubuntu image (latest version) for the container.

   - **`docker run -dit --name target_node1 ubuntu:latest`**
     - This command creates and starts another detached container named `target_node1` using the Ubuntu image, without mounting any directories or setting a working directory.

2. **Linux User and Privilege Commands**

   - **`useradd -m -s /bin/bash ansible`**

     - `useradd`: Adds a new user to the system.
     - `-m`: Creates a home directory for the user.
     - `-s /bin/bash`: Sets `/bin/bash` as the default shell for the user.
     - `ansible`: The username being created.

   - **`echo "ansible ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers`**

     - `echo "ansible ALL=(ALL) NOPASSWD: ALL"`: This command generates a string that grants the `ansible` user permission to run any command as root (sudo) without needing a password.
     - `>> /etc/sudoers`: Appends this string to the `/etc/sudoers` file, which manages sudo permissions for users.

   - **`echo "ansible:your_password_here" | chpasswd`**
     - `echo "ansible:your_password_here"`: Generates a string with the username `ansible` and the specified password.
     - `| chpasswd`: Sends the generated string to `chpasswd`, a command that sets the password for the specified user (`ansible` in this case).

These commands collectively set up a Docker environment with a control node and target nodes, configure the `ansible` user, grant it sudo privileges, and set a password for it. This setup prepares the environment for Ansible to manage the target nodes.

---

To copy the SSH key from the control node to each target node, you first need to know the IP addresses of the target nodes. Here’s how to expand on this process:

### Step 1: Get the IP Addresses of Target Nodes

You can retrieve the IP addresses of `target_node1` and `target_node2` by using Docker commands. In your terminal (on your Mac), run:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target_node1
```

This command will return the IP address of `target_node1`. Similarly, get the IP address of `target_node2`:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target_node2
```

Note these IP addresses, as you’ll need them for the SSH setup.

### Step 2: Copy the SSH Key from the Control Node to Each Target Node

1. **Switch to the Control Node**:
   In your terminal, access the `control_node` container:

   ```bash
   docker exec -it control_node bash
   ```

2. **Generate an SSH Key (if you haven’t already)**:
   If you haven’t generated an SSH key for the `root` user in the `control_node`, you can do it now:

   ```bash
   ssh-keygen -t rsa -b 2048
   ```

   - When prompted, save the key in `/root/.ssh/id_rsa` and don’t set a passphrase (just press Enter).

3. **Copy the SSH Key to the Target Nodes**:
   To allow passwordless SSH from the control node to each target node, copy the public SSH key to the `ansible` user on each target node.

   You can use the `ssh-copy-id` command to make this process easier. Run the following command from the `control_node` terminal, replacing `<target_node1_ip>` with the actual IP address of `target_node1`:

   ```bash
   ssh-copy-id -i /root/.ssh/id_rsa.pub ansible@<target_node1_ip>
   ```

   - When prompted for the `ansible` user's password (which you set up earlier on `target_node1`), enter it.
   - This will add the `control_node`'s public key to the `~/.ssh/authorized_keys` file on `target_node1` for the `ansible` user, enabling passwordless SSH login.

   Repeat this step for `target_node2`:

   ```bash
   ssh-copy-id -i /root/.ssh/id_rsa.pub ansible@<target_node2_ip>
   ```

4. **Verify SSH Access**:
   To confirm that the setup is correct, try connecting to each target node from the `control_node` without a password:

   ```bash
   ssh ansible@<target_node1_ip>
   ```

   If you’re able to connect without a password, the setup was successful. Run the same command to test connectivity with `target_node2`:

   ```bash
   ssh ansible@<target_node2_ip>
   ```

Now, your control node should be able to access both target nodes using SSH, and Ansible can connect to manage them.

---

The "Connection refused" error typically means that the SSH service isn't running on the target node. In Docker containers, SSH isn’t usually started automatically, so you’ll need to start it manually. Here’s how to resolve this:

### Step 1: Start the SSH Service on Each Target Node

1. Open a terminal on your Mac and start the SSH service on `target_node1`:

   ```bash
   docker exec -it target_node1 bash
   ```

2. Inside `target_node1`, install and start the SSH server if it’s not already running. (Ubuntu containers usually don’t have SSH enabled by default):

   ```bash
   apt update
   apt install -y openssh-server
   service ssh start
   ```

   Repeat these steps for `target_node2`:

   ```bash
   docker exec -it target_node2 bash
   apt update
   apt install -y openssh-server
   service ssh start
   ```

3. **(Optional) Ensure SSH Starts Automatically**:
   If you restart these containers, you’ll need to start the SSH service again. To automate this, you can add the following line to `/etc/rc.local` in each target node (if it exists), or create an entry in Docker for persistent services.

### Step 2: Retry Copying the SSH Key

Now that SSH is running, go back to the `control_node` and retry the `ssh-copy-id` command:

```bash
ssh-copy-id -i /root/.ssh/id_rsa.pub ansible@<target_node1_ip>
```

If successful, repeat for `target_node2`. This should allow you to connect to the target nodes without a password.

---

To run Ansible commands, you need an **inventory file** (usually named `hosts.ini`) that lists the target nodes (or "hosts") Ansible will manage. Here’s a detailed step-by-step guide for creating this file, including how to find the Ansible directory in your setup and what this file accomplishes.

### Purpose of the Inventory File (`hosts.ini`)

The inventory file tells Ansible which hosts (target nodes) it can connect to and manage. Each entry in this file includes details about a target node, such as its IP address, SSH user, and any other necessary settings.

With this file, you’ll be able to check if Ansible can connect to the target nodes by running basic commands or "pinging" them.

### Step-by-Step Instructions

1. **Navigate to the Ansible Directory on the Control Node**:
   In your setup, you have the control node with a directory named `/ansible` where you mounted the current working directory from your Mac. This means any files you create here can be accessed from both your Mac and the control node.

   To navigate to the `ansible` directory:

   - First, access the control node container from your Mac terminal:

     ```bash
     docker exec -it control_node bash
     ```

   - Once inside, navigate to the `/ansible` directory (where you mounted your Mac’s current directory):

     ```bash
     cd /ansible
     ```

2. **Create the Inventory File (`hosts.ini`)**:
   In the `/ansible` directory on the control node, you’ll now create an inventory file named `hosts.ini`.

   - To create and open this file in a basic text editor (e.g., `nano` or `vim`):

     ```bash
     nano hosts.ini
     ```

   - If `nano` isn’t installed, you can use `vim` instead:

     ```bash
     vim hosts.ini
     ```

3. **Add Target Node Information to the Inventory File**:
   In the `hosts.ini` file, add the following content, adjusting the IP addresses to match the IPs of your `target_node1` and `target_node2`.

   ```ini
   [targets]
   target_node1 ansible_host=<target_node1_ip> ansible_user=ansible
   target_node2 ansible_host=<target_node2_ip> ansible_user=ansible
   ```

   - **`[targets]`**: This is a group name. You can name it whatever you like; it's used to organize hosts.
   - **`target_node1` and `target_node2`**: These are labels for each target node.
   - **`ansible_host=<target_node_ip>`**: Replace `<target_node1_ip>` and `<target_node2_ip>` with the IP addresses you found earlier for each target node.
   - **`ansible_user=ansible`**: Specifies the `ansible` user for connecting to the target nodes.

   After adding this information, save and close the file (in `nano`, press `CTRL+X`, then `Y` to save and `Enter` to confirm).

4. **Check Ansible Connection to the Target Nodes**:
   Now, you can test if Ansible can connect to the target nodes listed in the inventory file.

   - In the `/ansible` directory, run the following command to "ping" the target nodes:

     ```bash
     ansible -i hosts.ini targets -m ping
     ```

     - `-i hosts.ini`: Specifies the inventory file you just created.
     - `targets`: Refers to the `[targets]` group in your inventory file.
     - `-m ping`: Uses the `ping` module, which checks if Ansible can connect to each node.

   - If everything is set up correctly, you should see a response similar to:

     ```plaintext
     target_node1 | SUCCESS => {
         "changed": false,
         "ping": "pong"
     }
     target_node2 | SUCCESS => {
         "changed": false,
         "ping": "pong"
     }
     ```

     This output confirms that Ansible can successfully connect to each target node using the SSH key you copied earlier.

### Summary of Steps

1. **Enter the Control Node**: `docker exec -it control_node bash`
2. **Navigate to `/ansible`**: `cd /ansible`
3. **Create `hosts.ini`**: Use `nano hosts.ini` and add target node details.
4. **Ping the Nodes**: Run `ansible -i hosts.ini targets -m ping` to verify the connection.

Once this is successful, your Ansible control node is ready to manage and automate tasks on the target nodes!
