# Phase 1

### Establish Directory Hierarchy

- Make directories within ```/srv``` for service-related data:
	```
	sudo mkdir -p /srv/containers/docker /srv/data /srv/logs /srv/labs
	```
- Set the ownership to root:
	```
	sudo chown -R root:root /srv
	sudo chmod 755 /srv
	```
- Verify with: 
	```
	ls -R /srv
	ls -ld /srv /srv/*
	```


### Install Docker Engine

- Install Docker using the ```apt``` repository: https://docs.docker.com/engine/install/ubuntu/
- Here is a paste of the official instructions:
- Set up Docker's ```apt``` repository:
	```
	# Add Docker's official GPG key:
	sudo apt update
	sudo apt install ca-certificates curl
	sudo install -m 0755 -d /etc/apt/keyrings
	sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
	sudo chmod a+r /etc/apt/keyrings/docker.asc

	# Add the repository to Apt sources:
	sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
	Types: deb
	URIs: https://download.docker.com/linux/ubuntu
	Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
	Components: stable
	Signed-By: /etc/apt/keyrings/docker.asc
	EOF

	sudo apt update
	```
- Install the Docker packages:
	```
	sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
	```
- Verify that Docker is running: ```sudo systemctl status docker```
- Verify successful installation: ```sudo docker run hello-world```

### Docker Configuration

- Access the Docker daemon: ```sudo nano /etc/docker/daemon.json```
- Add the following:
	```
	{
		"data-root": "/srv/containers/docker",
		"log-driver": "json-file",
		"log-opts": {
			"max-size": "10m",
			"max-file": "3"
		}
	}
	```
- Restart Docker to apply changes: ```sudo systemctl restart docker```
- Add your user to the ```docker``` group:
	```
	sudo usermod -aG docker $USER
	newgrp docker
	```
- Now the Docker root has been moved to ```/srv/containers/docker```
- Verify: ```docker info | grep "Docker Root Dir"```

### Logging Configuration

- Access systemd journal: ```sudo nano /etc/systemd/journald.conf```
- Uncomment and set the following: ```SystemMaxUse=500M``` to limit log storage
- Restart to apply changes: ```sudo systemctl restart systemd-journald```
- Verify ```journalctl --disk-usage``` does not exceed 500M

### Enable Security Upgrades

- Reconfigure unattended upgrades:
	```
	sudo apt install -y unattended-upgrades
	sudo dpkg-reconfigure unattended-upgrades
	```
- Choose: ```Yes```
- Verify configuration: ```cat /etc/apt/apt.conf.d/50unattended-upgrades```
- Check timer: ```systemctl status unattended-upgrades``` (it should be active)