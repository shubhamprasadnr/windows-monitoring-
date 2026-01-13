


# Monitoring of  Windows Server  
  


---

## 🎯 **Objective**

The objective of this document is to **monitor windows server  performance metrics** on a Windows Server using **Prometheus** and **Grafana**.
It includes complete setup instructions for:
- Configuring SQL Server Agent If not Enabled 
- Installing and running Windows Exporter  
- Visualizing MSSQL metrics in Grafana dashboards  

---

##  **Requirements**

Before starting, ensure the following prerequisites are met:

| Component | Description |
|------------|-------------|
|  **Windows Server** | Host machine have instaled Windows Exporter |
|  **Prometheus** | Metrics collection tool (installed on Ubuntu) |
|  **Grafana** | Visualization tool (installed on Ubuntu) |
|  **Windows Exporter** | Exposes system and MSSQL metrics |
|  **Open Ports** | `9182` (Windows Exporter), `9090` (Prometheus), `3000` (Grafana) |

---

##  **Install Prometheus Windows Exporter**

Prometheus Windows Exporter helps expose system and MSSQL metrics for monitoring.

### **Steps:**

1. **Download Windows Exporter**  
   Visit the [official releases page](https://github.com/prometheus-community/windows_exporter/releases) to download the latest version of **Windows Exporter**.
   
   **Note:If Windows Exporter is not installed under C:\Program Files\windows_exporter, it may not run as a Windows service reliably.**

2. **Download the Binary File**  
   Select and download the binary file:

   
3. **Open Command Prompt as Administrator**  
- Press **Start → Search “cmd” → Right-click → Run as Administrator**.

4. **Run the Exporter Manually**  
Execute the following command to start the Windows Exporter and fetch SQL Server statistics:

```bash
"C:\Program Files\windows_exporter\windows_exporter-0.31.3-amd64.exe" --collectors.enabled="cpu,logical_disk,net,os,service,system,textfile,"
```

5.**Verify the Metrics Endpoint**
 - Press Open your browser and navigate to:
[http://`<PrivateIp>`:9182/metrics](http://`<PrivateIp>`:9182/metrics)

---

##  Run Windows Exporter Automatically After System Restart
 To ensure the Windows Exporter service starts automatically after a system reboot, follow these steps:

**open cmd**
- Run below command to create a service for window exporter 
``` bash 
sc create windows_exporter ^
  binPath= "\"C:\Program Files\windows_exporter\windows_exporter.exe\" --telemetry.addr=:9182 --collectors.enabled=cpu,cs,logical_disk,os,system,textfile" ^
  start= auto

```
**OR** 

**Open Registry Editor**

- Press Press Start → Search “regedit” → Open Registry Editor

2. Navigate to the Following Path:
**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\windows_exporter**
Modify the ImagePath Value,Double-click on ImagePath.

3. Replace the existing value with the following (adjust if your installation path differs):
```bash
"C:\Program Files\windows_exporter\windows_exporter-0.31.3-amd64.exe" --collectors.enabled="cpu,logical_disk,net,os,service,system,textfile,mssql"
```
<img width="1366" height="768" alt="Screenshot (13)" src="https://github.com/user-attachments/assets/8726918c-d1fb-48eb-9659-baa839d93c0e" />

4. Press **`Windows + R`** to open the Run dialog.  
5. Type **`services.msc`** and press **Enter** to open the **Services** window.

6.Double-click the **window exporter** service to open its **Properties** window.

7.In the **Startup type** dropdown menu, select **Automatic**, 
 You can also choose **Automatic (Delayed Start)** if you want other services to start first. 

<img width="1366" height="768" alt="Screenshot (19)" src="https://github.com/user-attachments/assets/7f07fe79-c919-4113-b113-34ccb40f2b06" />


---

##  MSSQL Monitoring Metrics 

| **Category**                     | **Metric Description**                                                                                                                | **Purpose / Insight**                                                                                           |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Networking**                | **Network Traffic & Hourly Usage**                                                                                                    | Tracks incoming and outgoing traffic to monitor network throughput and identify congestion or latency.   |
| **Storage**                   | **Used Space & Available Space**                                                                                                      | Monitors disk utilization and available storage capacity to prevent database outages due to full disks.  |

| **Memory**              | **Overall Memory Usage (Committed & Free)**                                                                                           | Monitors SQL Server memory allocation and helps identify memory pressure or leaks.                             |
| **CPU**                | **CPU Usage & CPU Load per Process**                                                                                                  | Evaluates CPU consumption by SQL Server and related services to identify overutilization or idle patterns.      |
| **General Statistics** | **Database Health, Deadlocks, Network, Storage, Logins/Logouts, Connection Reset, Lock Waits, TempDB Free Space, Active Temp Tables** | Offers a high-level operational view of server performance and stability, covering all major resource areas.    |

##  Observability Dashboard

You can download the MSSQL Grafana Dashboard JSON from the link below:

[Download MSSQL Grafana Dashboard JSON](https://github.com/ot-client/o11y-k8s-setup-template/blob/108-mssql-poc-documentation/grafana/grafana_dashboard/Opstree/Database/mssql.json)

---
<img width="1342" height="570" alt="Screenshot 2025-11-14 145715" src="https://github.com/user-attachments/assets/8bdc5f26-4971-416d-9ba8-a419530bb570" />
