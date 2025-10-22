# **JavaScript Project SonarQube Analysis – Step-by-Step Guide**

## **1. Launch an EC2 Instance**

* Launch an **AWS EC2 t3.medium instance** (2 vCPU, 4 GB RAM) with Ubuntu 22.04 LTS.
* Configure Security Group:

  * **Port 22** → SSH access
  * **Port 9000** → SonarQube Web UI access
* Create and download a **key pair** (`.pem` file) for SSH access.

---

## **2. Connect to EC2**

```bash
ssh -i "your-key.pem" ubuntu@<EC2_PUBLIC_IP>
```
<img width="1235" height="884" alt="Image" src="https://github.com/user-attachments/assets/48b2825c-1759-453b-ae3f-84236ae86883" />

---

## **3. Install Java (OpenJDK 17 for SonarQube 9)**

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
```

<img width="922" height="254" alt="Image" src="https://github.com/user-attachments/assets/e983cb1b-4131-4a97-b827-8842a990610f" />

---

## **4. Install and Configure SonarQube**

1. Download and extract SonarQube 9:

```bash
cd /opt
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.65466.zip
unzip sonarqube-9.9.0.65466.zip
mv sonarqube-9.9.0.65466 sonarqube
sudo chown -R ubuntu:ubuntu sonarqube
```
<img width="1800" height="275" alt="Image" src="https://github.com/user-attachments/assets/c108b967-ef9a-41e9-a0a2-f9583e152d84" />

2. Configure system limits:

```bash
sudo sysctl -w vm.max_map_count=262144
echo 'vm.max_map_count=262144' | sudo tee -a /etc/sysctl.conf
ulimit -n 65536
ulimit -u 4096
```

---

## **5. Start SonarQube**

```bash
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
./sonar.sh status
```

Then visit: [http://<EC2_PUBLIC_IP>:9000](http://<EC2_PUBLIC_IP>:9000) 

<img width="1148" height="245" alt="Image" src="https://github.com/user-attachments/assets/7a5dcc75-52e5-48fb-a124-6eb2dcf9e7bc" />

---

## **6. Create a SonarQube Token**

1. Log in to SonarQube web UI (**admin/admin** by default).
2. Go to **My Account → Security → Generate Token**.
3. Copy the token (e.g., `squ_4d4e0a30afdd8fb63881c9a0ddbba7422692ce94`).

<img width="1333" height="407" alt="Screenshot 2025-10-21 at 4 56 25 PM" src="https://github.com/user-attachments/assets/e4aebee3-66b8-43cc-a956-e92c7bc314df" />


---

## **7. Install Node.js & NPM**

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
node -v
npm -v
```

<img width="1800" height="392" alt="Image" src="https://github.com/user-attachments/assets/c7577488-a1c8-4e92-bf0a-3d4a57cbba56" />

<img width="1634" height="491" alt="Image" src="https://github.com/user-attachments/assets/f854aae0-2ca3-4862-a47d-bf61222edb98" />


---

## **8. Install SonarScanner CLI**

```bash
cd /opt
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.8.0.2856-linux.zip
unzip sonar-scanner-cli-4.8.0.2856-linux.zip
mv sonar-scanner-4.8.0.2856-linux sonar-scanner
export PATH=$PATH:/opt/sonar-scanner/bin
```

<img width="1800" height="329" alt="Image" src="https://github.com/user-attachments/assets/99baf93b-747e-49ff-ab76-7b8e39ffa008" />
<img width="1800" height="266" alt="Image" src="https://github.com/user-attachments/assets/8b51dc95-879e-4ece-9035-c4533693cfa2" />

Make the path permanent:

```bash
echo 'export PATH=$PATH:/opt/sonar-scanner/bin' >> ~/.bashrc
source ~/.bashrc
```

---

## **9. Prepare JavaScript Project**

Navigate to your project directory:

```bash
cd ~/AngularCalculator
```

Create a `sonar-project.properties` file:

```properties
sonar.projectKey=AngularCalculator
sonar.projectName=Angular Calculator
sonar.sources=.
sonar.host.url=http://<EC2_PUBLIC_IP>:9000
sonar.login=<your_sonar_token>
```

<img width="1215" height="173" alt="Image" src="https://github.com/user-attachments/assets/a676cbac-6aa3-4097-9024-8487001c43d2" />

---

## **10. Run SonarScanner**

```bash
sonar-scanner
```

**Expected Output:**

* SonarScanner analyzes JavaScript, TypeScript, CSS, and HTML files.
* Uploads the report to SonarQube.
* Displays a message like:

```
INFO: ANALYSIS SUCCESSFUL, you can find the results at: http://<EC2_PUBLIC_IP>:9000/dashboard?id=AngularCalculator
```

<img width="1800" height="1169" alt="Image" src="https://github.com/user-attachments/assets/87fe3e28-fd1d-46f2-b311-e02daa2c7049" />  

---

## **11. View Results on SonarQube Web UI**

1. Open `http://<EC2_PUBLIC_IP>:9000`.
2. Log in using credentials or token.
3. Search for **AngularCalculator**.
4. Explore the dashboard:

   * Bugs, Vulnerabilities, Code Smells
   * Test Coverage (if configured)
   * Duplications and Code Complexity
5. Click files or lines for detailed issue insights.


<img width="1800" height="1169" alt="Image" src="https://github.com/user-attachments/assets/452f8a6a-1478-4227-b6e8-40285bee6d26" />

---

## **Notes**

* For JavaScript/TypeScript, SonarScanner automatically detects `.js`, `.ts`, `.css`, `.html` files.
* Review the **Quality Gate** in SonarQube to enforce project standards.
* Keep your **token secure**.
* Customize `sonar-project.properties` for coverage reports or advanced configurations.

---

This guide provides a complete setup for analyzing JavaScript or TypeScript projects in SonarQube on an AWS EC2 instance.
