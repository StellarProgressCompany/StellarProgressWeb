To install Docker and Docker Compose on Debian, follow these steps:

### Installing Docker

1. **Update the apt package index**:
   ```bash
   sudo apt update
   ```

2. **Install required packages** to allow apt to use a repository over HTTPS:
   ```bash
   sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release
   ```

3. **Add Docker’s official GPG key**:
   ```bash
   curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. **Set up the stable repository**:
   ```bash
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. **Update the apt package index again**:
   ```bash
   sudo apt update
   ```

6. **Install the latest version of Docker Engine and containerd**:
   ```bash
   sudo apt install docker-ce docker-ce-cli containerd.io
   ```

7. **Verify that Docker is installed correctly** by running the hello-world image:
   ```bash
   sudo docker run hello-world
   ```

### Installing Docker Compose

1. **Download the current stable release of Docker Compose**:
   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep -Po '"tag_name": "\K.*?(?=")')/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

2. **Apply executable permissions to the binary**:
   ```bash
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. **Verify that Docker Compose is installed correctly**:
   ```bash
   docker-compose --version
   ```

### Optional: Manage Docker as a non-root user

1. **Create the docker group**:
   ```bash
   sudo groupadd docker
   ```

2. **Add your user to the docker group**:
   ```bash
   sudo usermod -aG docker $USER
   ```

3. **Log out and log back in** so that your group membership is re-evaluated. Alternatively, you can use the following command to activate the changes to groups:
   ```bash
   newgrp docker
   ```

4. **Verify that you can run docker commands without sudo**:
   ```bash
   docker run hello-world
   ```

These steps should set up Docker and Docker Compose on your Debian system.
