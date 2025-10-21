# javascript-sonarqube-analysis-for-angular-calculater-app

JavaScript Project SonarQube Analysis – Step-by-Step Guide
1️⃣ Launch an EC2 Instance
Launch an AWS EC2 t3.medium instance (2 vCPU, 4 GB RAM) with Ubuntu 22.04 LTS.
Configure Security Group:
Port 22 → for SSH
Port 9000 → for SonarQube Web UI
Key Pair → download .pem file for SSH access.
2️⃣ Connect to EC2
ssh -i "your-key.pem" ubuntu@<EC2_PUBLIC_IP>
3️⃣ Install Java (OpenJDK 17 recommended for SonarQube 9)
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
4️⃣ Install and Configure SonarQube
Download and extract SonarQube 9:
cd /opt
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.65466.zip
unzip sonarqube-9.9.0.65466.zip
mv sonarqube-9.9.0.65466 sonarqube
sudo chown -R ubuntu:ubuntu sonarqube
Configure system limits:
sudo sysctl -w vm.max_map_count=262144
echo 'vm.max_map_count=262144' | sudo tee -a /etc/sysctl.conf
ulimit -n 65536
ulimit -u 4096
5️⃣ Start SonarQube
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
./sonar.sh status
Wait a few seconds and check:
http://<EC2_PUBLIC_IP>:9000
6️⃣ Create a SonarQube Token
Log in to SonarQube web UI (default admin/admin).
Go to My Account → Security → Generate Token
Copy the token (example: squ_4d4e0a30afdd8fb63881c9a0ddbba7422692ce94).
7️⃣ Install Node.js & NPM
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
node -v
npm -v
8️⃣ Install SonarScanner CLI
cd /opt
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.8.0.2856-linux.zip
unzip sonar-scanner-cli-4.8.0.2856-linux.zip
mv sonar-scanner-4.8.0.2856-linux sonar-scanner
export PATH=$PATH:/opt/sonar-scanner/bin
Optional: add to .bashrc for permanent path:
echo 'export PATH=$PATH:/opt/sonar-scanner/bin' >> ~/.bashrc
source ~/.bashrc
9️⃣ Prepare JavaScript Project
Navigate to your project directory:
cd ~/AngularCalculator
Create sonar-project.properties in project root:
# Unique project key
sonar.projectKey=AngularCalculator
sonar.projectName=Angular Calculator

# Project files
sonar.sources=.

# SonarQube server
sonar.host.url=http://<EC2_PUBLIC_IP>:9000
sonar.login=<your_sonar_token>
🔟 Run SonarScanner
sonar-scanner
Expected output:
Analysis runs for JavaScript, TypeScript, CSS, HTML, etc.
Reports are uploaded to SonarQube.
Final line should show:
INFO: ANALYSIS SUCCESSFUL, you can find the results at: http://<EC2_PUBLIC_IP>:9000/dashboard?id=AngularCalculator
1️⃣1️⃣ View Results on SonarQube Web UI
Open browser → http://<EC2_PUBLIC_IP>:9000
Log in with your credentials or token.
Use the search bar → type AngularCalculator.
Open the project dashboard.
Review:
Bugs, Vulnerabilities, Code Smells
Test Coverage (if configured)
Duplication
Code Analysis History
Drill down on issues → click file or line-level details.
1️⃣2️⃣ Optional: CI/CD Integration
You can configure GitHub Actions / Jenkins / GitLab CI to run sonar-scanner automatically on each commit.
✅ Notes
JavaScript/TypeScript projects: SonarScanner will detect .js, .ts, .css, .html, etc.
Always check Quality Gate in the dashboard to enforce standards.
SonarScanner uses the token for authentication, do not share it publicly.
For additional configuration, edit sonar-project.properties.
