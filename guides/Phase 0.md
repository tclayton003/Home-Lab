# Phase 0

### System Notes:

- OS: Ubuntu Server 24.04.3 LTS
- Hardware: Raspberry Pi 5

### First Boot:

- Ensure the OS is up to date:
	```
	sudo apt update && sudo apt full-upgrade -y
	sudo reboot
	```
- Harden SSH:
	```
	sudo nano /etc/ssh/sshd_config
	```
- Find the following lines, uncomment, and disable:
	```
	PasswordAuthentication no
	PermitRootLogin no
	```
- Add SSH public key to server if it's not added already (I ran this from my Windows PC where the public key is stored):
	```
	type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh user@hostname "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
	```
- Restart SSH (try to connect in another terminal before ending session):
	```
	sudo systemctl restart ssh
	```

### Basic Configuration

- Install core utilities:
	```
	sudo apt install -y curl \ wget \ git \ vim \ htop \ ca-certificates \ gnupg \ lsb-release \ unzip
	```
- Disable extra services (personal preference, I want to disable Wi-Fi and Bluetooth):
	```
	sudo nano /boot/firmware/config.txt
	```
- Add the following lines under ```# Config settings specific to arm64```:
	```
	dtoverlay=disable-wifi
	dtoverlay=disable-bt
	```
- Reboot to enact changes
	```
	sudo reboot
	```
- Configure firewall:
	```
	sudo apt install -y ufw
	```
- Set default policy:
	```
	sudo ufw default deny incoming
	sudo ufw default allow outgoing
	```
- Allow SSH:
	```
	sudo ufw allow ssh
	sudo ufw enable
	sudo ufw status verbose
	```
- Check system status:
	```
	ss -tulpn
	```
- Note the services that are running and their ports. If everything looks good, then Phase 0 is complete.