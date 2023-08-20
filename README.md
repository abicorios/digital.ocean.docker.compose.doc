# digital.ocean.docker.compose.doc
Installing Docker and Docker Compose on a Digital Ocean Droplet

## Initial Setup

1. Create a free domain at https://nic.ua/en/domains/.pp.ua.
2. In Digital Ocean, set up a minimal Ubuntu droplet and add your public SSH key following the guide at https://docs.digitalocean.com/products/droplets/how-to/create/.
3. Link the domain to the droplet using the instructions at https://docs.digitalocean.com/products/networking/dns/how-to/add-domains/.

## Digital Ocean Droplet Configuration

1. Log in as the root user via SSH to the droplet and perform a system upgrade:

   ```bash
   sudo fallocate -l 3G /swapfile
   sudo chmod 600 /swapfile
   sudo mkswap /swapfile
   sudo swapon /swapfile
   sudo cp /etc/fstab /etc/fstab.bak
   echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
   sudo sysctl vm.swappiness=10
   sudo sysctl vm.vfs_cache_pressure=50
   echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
   echo 'vm.vfs_cache_pressure=50' | sudo tee -a /etc/sysctl.conf
   apt update
   apt upgrade
   reboot
   ```

2. Log in to the droplet again via SSH and continue the setup:

   ```bash
   adduser yar
   usermod -aG sudo yar
   ufw app list # Check available OpenSSH
   ufw allow OpenSSH
   ufw enable
   rsync --archive --chown yar:yar ~/.ssh /home/yar
   exit
   ```

3. Log in to the droplet once more using the new user account and proceed with the setup:

   ```bash
   sudo apt install apt-transport-https ca-certificates software-properties-common
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt update
   apt-cache policy docker-ce # Check available repository
   sudo apt install docker-ce
   sudo systemctl status docker
   sudo usermod -aG docker ${USER}
   exit
   ```

4. Log in to the droplet again with the new user account and proceed with the final setup:

   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   docker-compose --version
   ```

5. If you encounter issues cloning a private GitHub repository, generate an SSH key using the command `ssh-keygen -t ed25519 -C "your_email@example.com"` and add the public key to your private GitHub repository settings.

## References
- Tutorial: [How to Install and Set Up Laravel with Docker Compose on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-set-up-laravel-with-docker-compose-on-ubuntu-20-04)
