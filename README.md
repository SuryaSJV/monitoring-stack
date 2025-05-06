✅ Project: DevOps Monitoring Sandbox on Azure Ubuntu VM:
--------------------------------------------------------------
📦 Tools:
---------

1.Azure Ubuntu VM

install Azure CLI 

![Image](https://github.com/user-attachments/assets/63e1ddbc-c727-404e-a441-68cf698c23ea)

2.Shell Scripts (for provisioning)

3.Prometheus

4.Node Exporter

5.Grafana

6.Alertmanager


🔧 Step 1: Provision Azure Ubuntu VM:
--------------------------------------
🖥️ Create an Ubuntu VM on Azure:

![Image](https://github.com/user-attachments/assets/481970d2-2853-43b1-8b65-575769d7478e)


-Region: Your closest Azure region

-Size: Standard B1s or above

-Inbound ports: Allow SSH (22), HTTP (80), and custom ports (9090, 9100, 3000, 9093)


🔐 Step 2: SSH into VM:
------------------------
```
ssh azureuser@<vm_public_ip>
```

📁 Step 3: Prepare Folder Structure:
-------------------------------------

=> Monitoring-stack
       |
       |-prometheus 
       |
       |-alertmanager 
       |
       |-grafana
       | 
       |-node_exporter


📜 Step 4: Install Required Packages:
---------------------------------------
```
sudo apt update && sudo apt install -y wget curl tar unzip
```
then install the required packages 

![Image](https://github.com/user-attachments/assets/bd1b4f9b-b240-4725-87d7-62d9a3a06610)


📦 Step 5: Install Prometheus:
-------------------------------
📥 Download and extract:

```
cd ~/monitoring-stack/prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.51.1/prometheus-2.51.1.linux-amd64.tar.gz
tar -xvf prometheus-2.51.1.linux-amd64.tar.gz
mv prometheus-2.51.1.linux-amd64 prometheus
```

🛠️ Create Prometheus config file:

vim ~/monitoring-stack/prometheus/prometheus/prometheus.yml

```
global:
  scrape_interval: 15s
  evaluation_interval: 15s

alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - "20.106.202.149:9093"

rule_files:
  - "alert_rules.yml"

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["20.106.202.149:9090"]

  - job_name: "node_exporter"
    static_configs:
      - targets: ["20.106.202.149:9100"]

  - job_name: "alertmanager"
    static_configs:
      - targets: ["20.106.202.149:9093"]

```

🟢 Run Prometheus:

```
cd prometheus/prometheus
./prometheus --config.file=prometheus.yml
```
![Image](https://github.com/user-attachments/assets/a7bf8f81-2a6b-47e2-84db-83415c8839b1)

then visit here
```
Visit http://<vm-ip>:9090

```
![Image](https://github.com/user-attachments/assets/8af8ae44-5007-4c5d-8813-64e0cd29c72a)

🌐 Step 6: Install Node Exporter:
----------------------------------
```
cd ~/monitoring-stack/node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
tar -xvf node_exporter-1.8.1.linux-amd64.tar.gz
mv node_exporter-1.8.1.linux-amd64 node_exporter
cd node_exporter
./node_exporter
```
![Image](https://github.com/user-attachments/assets/720f01d3-0619-49d4-8646-b672b88110fd)

Visit 
```
http://<vm-ip>:9100/metrics
```
 — you should see plain text metrics.

![Image](https://github.com/user-attachments/assets/386980cc-82a6-4ed9-899d-abab16e60be8)

📊 Step 7: Install Grafana:
----------------------------
```
cd ~/monitoring-stack/grafana
wget https://dl.grafana.com/oss/release/grafana_10.4.2_amd64.deb
--->Run the following command to automatically install missing dependencies:
      ```
      sudo apt-get install -f
      ```
sudo dpkg -i grafana_10.4.2_amd64.deb
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

🟢 Access Grafana at:
```
http://<vm-ip>:3000
```


steps to follow:

✅ Step 1: Log into Grafana:

Go to http://<your-vm-ip>:3000

Use credentials:

Username: admin

Password: admin

You’ll be prompted to change the password → set a new one or skip.

✅ Step 2: Add Prometheus as a Data Source
In Grafana’s left sidebar, click the gear icon ⚙️ (Configuration).

Click “Data Sources” → then click “Add data source”.

Choose Prometheus.

In the URL field, enter:

http://localhost:9090
Scroll down and click “Save & test”.

You should see “Data source is working” if successful.

✅ Step 3: Import Dashboard ID 1860 (Node Exporter Full)
In the left sidebar, click the “+” icon → Import.

In the Import via grafana.com field, enter:

"  1860  "
Then click “Load”.

Set:

Name: (leave default or rename)

Prometheus data source: Select the Prometheus source you just added.

Click Import.



🚨 Step 8: Configure Alertmanager:
-----------------------------------
📥 Download:

```
cd ~/monitoring-stack/alertmanager
wget https://github.com/prometheus/alertmanager/releases/download/v0.27.0/alertmanager-0.27.0.linux-amd64.tar.gz
tar -xvf alertmanager-0.27.0.linux-amd64.tar.gz
mv alertmanager-0.27.0.linux-amd64 alertmanager
```

📜 Create alertmanager.yml:
```
vi alertmanager/alertmanager/alertmanager.yml
```

```
global:
  resolve_timeout: 1m

route:
  receiver: 'email-alert'

receivers:
  - name: 'email-alert'
    email_configs:
      - to: "suryasjv139@gmail.com"  # Recipient email address
        from: "suryasjv139@gmail.com"  # Sender email address (your Gmail)
        smarthost: "smtp.gmail.com:587"
        auth_username: "suryasjv139@gmail.com"  # Your Gmail address
        auth_password: "aecz uplp wwsm xvqa"  # The 16-character App Password
        auth_identity: "suryasjv139@gmail.com"
        require_tls: true

```
NOTE:
-----
Explanation of the Changes:
-to: Set to your recipient email address suryasjv139@gmail.com.

--from: Set to your Gmail address suryasjv139@gmail.com.

-auth_username: Set to your Gmail address suryasjv139@gmail.com.

-auth_password: The 16-character App Password you generated (aecz uplp wwsm xvqa).

-smarthost: Gmail SMTP server (smtp.gmail.com) on port 587.

-require_tls: Ensures that the connection is encrypted.


💡 For Gmail, you must use an App Password (not your regular Gmail password). You must also have 2FA enabled.

To use Gmail for email alerts in Alertmanager, you need to follow these steps:

1. Enable Two-Factor Authentication (2FA) in Gmail:
----------------------------------------------------
Go to your Google Account: Google Account.

Click on Security in the left-hand sidebar.

Under Signing in to Google, enable 2-Step Verification and follow the steps to complete the setup.

2. Generate an App Password:
-----------------------------
Once 2FA is enabled, you need to generate an App Password that Alertmanager can use to authenticate with Gmail:

Go to the App Passwords page: App Passwords.

Select Mail as the app and Other (for custom name) for the device.

Click Generate, and a 16-character password will appear.

Copy this password. You will use this in place of your regular Gmail password in Alertmanager.

=>Then
"Configure Alertmanager's alertmanager.yml File as above"

🟢 Run Alertmanager:

```
cd alertmanager/alertmanager
./alertmanager --config.file=alertmanager.yml
```
![Image](https://github.com/user-attachments/assets/f39ef876-855e-4b7f-ace0-0c4fd0bba13b)

![Image](https://github.com/user-attachments/assets/8bf06599-3f67-405e-bbd2-ad9434e34a87)


Access:
```
 http://<vm-ip>:9093
```
![Image](https://github.com/user-attachments/assets/190d5806-d1a0-4985-85f5-7c138c4f4624)


⚠️ Step 9: Setup Alert Rules in Prometheus:
---------------------------------------------
```
vi ~/monitoring-stack/prometheus/prometheus/alert_rules.yml
```

```
groups:
  - name: node-alerts
    rules:
      - alert: HighCPUUsage
        expr: 100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[2m])) * 100) > 75
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "High CPU Usage on {{ $labels.instance }}"
          description: "CPU usage is above 75%"

      - alert: HighDiskUsage
        expr: (node_filesystem_size_bytes{mountpoint="/"} - node_filesystem_free_bytes{mountpoint="/"}) / node_filesystem_size_bytes{mountpoint="/"} > 0.8
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "Disk Usage above 80%"
          description: "Instance {{ $labels.instance }} has disk usage above 80%"
```

📦 Reference the alert rules in prometheus.yml:
------------------------------------------------
```
rule_files:
  - "alert_rules.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - - "20.106.202.149:9093"
```

Restart Prometheus:
-------------------
```
pkill prometheus
./prometheus --config.file=prometheus.yml
```

✅ Output: Alerts will be triggered based on your rules and sent to Alertmanager.




To display all the required things in Grafana Dashboard make sure 
visit 
```
http://20.106.202.149:9090/targets
```
you should see the Targets as 

```
alertmanager (1/1 up)
node_exporter (1/1 up)
prometheus (1/1 up)
```
As like below:

![Image](https://github.com/user-attachments/assets/099ea40b-812c-46c5-963f-fd24ec95d328)

Then only you can see all the  CPU / MEM / DISK  in the dashboard successfully if any of the Promotheus, Node Exporter, Alert Manager are gone down we cant get the detailes of the
CPU / MEM / DISK properly like we might see them as N/A (not available).



you should see "node_exporter"server as:
----------------------------------------

![Image](https://github.com/user-attachments/assets/386980cc-82a6-4ed9-899d-abab16e60be8)

![Image](https://github.com/user-attachments/assets/23a7b16e-91ae-4ebe-9906-48ab70160784)


And "Prometheus" server as:
---------------------------

![Image](https://github.com/user-attachments/assets/a7bf8f81-2a6b-47e2-84db-83415c8839b1)


alert-manager as:
-----------------

![Image](https://github.com/user-attachments/assets/190d5806-d1a0-4985-85f5-7c138c4f4624)

![Image](https://github.com/user-attachments/assets/a235a4f4-b9d9-444e-966a-c3f3bebb55d1)



Finally visible representation in "Grafana" server would be appear as below:
-----------------------------------------------------------------------------

![Image](https://github.com/user-attachments/assets/d195a658-413c-4a84-8513-53116a798b34)