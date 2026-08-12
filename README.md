
# jenkins-installation:

#!/bin/bash

# Jenkins Installation - Ubuntu 24.04
# Java 21 required

# 1. Check Java
java -version

# 2. Add Jenkins repository key
sudo mkdir -p /etc/apt/keyrings

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

# 3. Add Jenkins repository
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | \
sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# 4. Update packages
sudo apt update

# 5. Install Jenkins
sudo apt install -y jenkins

# 6. Configure Jenkins port
sudo sed -i 's/^HTTP_PORT=.*/HTTP_PORT=8081/' /etc/default/jenkins

# 7. Configure Jenkins to listen on all interfaces
sudo mkdir -p /etc/systemd/system/jenkins.service.d

sudo tee /etc/systemd/system/jenkins.service.d/override.conf > /dev/null <<'EOF'
[Service]
ExecStart=
ExecStart=/usr/bin/jenkins --httpPort=8081 --httpListenAddress=0.0.0.0
EOF

# 8. Reload systemd
sudo systemctl daemon-reload

# 9. Enable and start Jenkins
sudo systemctl enable --now jenkins

# 10. Check Jenkins status
sudo systemctl status jenkins --no-pager

# 11. Check Jenkins port
sudo ss -lntp | grep ':8081'

# 12. Display initial admin password
echo "Jenkins Initial Admin Password:"
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
