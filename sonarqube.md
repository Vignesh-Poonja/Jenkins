# SonarQube Installation and Setup

## Prerequisites
Ensure your system meets the following requirements:
- **OS**: Ubuntu 22.04 / 24.04
- **RAM**: Minimum 4GB (8GB recommended)
- **Java**: OpenJDK 17
- **Database**: PostgreSQL 14+
- **Ports**: 9000 (default for SonarQube)

## Step 1: Update System
```bash
sudo apt update && sudo apt upgrade -y
```

## Step 2: Install Required Dependencies
```bash
sudo apt install unzip wget curl -y
```

## Step 3: Install Java 17
```bash
sudo apt install openjdk-17-jdk -y
java -version  # Verify installation
```

## Step 4: Install and Configure PostgreSQL
```bash
sudo apt install postgresql postgresql-contrib -y
```

Create a database and user for SonarQube:
```bash
sudo -u postgres psql
```
Inside PostgreSQL prompt, run:
```sql
CREATE DATABASE sonarqube;
CREATE USER sonar WITH ENCRYPTED PASSWORD 'sonarpassword';
GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonar;
ALTER DATABASE sonarqube OWNER TO sonar;
\q  # Exit PostgreSQL
```

## Step 5: Download and Install SonarQube
```bash
cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
sudo unzip sonarqube-10.4.1.88267.zip
sudo mv sonarqube-10.4.1.88267 sonarqube
sudo chown -R $(whoami):$(whoami) /opt/sonarqube
```

## Step 6: Configure SonarQube
Edit the configuration file:
```bash
sudo nano /opt/sonarqube/conf/sonar.properties
```
Modify the following lines:
```
sonar.jdbc.username=sonar
sonar.jdbc.password=sonarpassword
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
sonar.web.port=9000
```
Save and exit (`CTRL+X`, then `Y` and `Enter`).

## Step 7: Create a Systemd Service for SonarQube
```bash
sudo nano /etc/systemd/system/sonarqube.service
```
Paste the following content:
```
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
User=$(whoami)
Group=$(whoami)
Restart=always

[Install]
WantedBy=multi-user.target
```
Save and exit.

## Step 8: Start SonarQube Service
```bash
sudo systemctl daemon-reload
sudo systemctl enable sonarqube
sudo systemctl start sonarqube
sudo systemctl status sonarqube
```

## Step 9: Access SonarQube
Open a browser and go to:
```
http://<server-ip>:9000
```
Default login credentials:
- **Username**: admin
- **Password**: admin

## Step 10: Check Logs if Issues Occur
```bash
cat /opt/sonarqube/logs/sonar.log | grep ERROR
cat /opt/sonarqube/logs/web.log | grep ERROR
cat /opt/sonarqube/logs/es.log | grep ERROR
```

---
🎉 **SonarQube is now installed and running!**

