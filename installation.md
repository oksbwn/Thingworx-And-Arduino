# Thingworx 8.4

##Requirements
1. OS/Platform: Windows Server 2016/2012 R2/ 2008 R2 SP1, Red Hat Enterprise Linux (RHEL) 7.5, Ubuntu 14.04 LTS / 16.04 LTS / 18.04 LTS
2. Java JDK: Java SE Development Kit 8, Update 92 , 1.8.0_92- b14 (64-bit), OpenJDK not supported
3. Tomcat : 8.5.42(64- bit), 9.0.21 (64- bit)
2. Database:    PostgreSQL (9.4.5, 9.5.11,9.6, 10)
                DataStaxEnterpriseEdition (4.6.3, 5)
                Microsoft SQL Server (2016)
                AzureSQL (Azure SQL Logical ServerV12)
                InfluxDB (1.6.3) #Not supported for use with ThingWorx Flow.

## Setting up Ubuntu in VM
1. Download latest version of Ubuntu from respective website. [Dwonload (1.9 GiB)](http://releases.ubuntu.com/18.04/ubuntu-18.04.3-desktop-amd64.iso)
2. Start Oracle VM VirtualBox. (In ITC can be installed from Software Center)
3. Install UBUNTU to the VM. Can follow [this](https://www.wikihow.com/Install-Ubuntu-on-VirtualBox) guide.

## 
sudo apt-get update
sudo apt-get install ntp #Network Time Protocol (NTP) settings for time synchronization
sudo apt-get install authbind #AUTHBIND properties to allow Tomcat to bind to ports below 1024
tar -xf jdk-8uxxx-linux-x64.tar.gz

https://support.ptc.com/help/thingworx_hc/thingworx_8_hc/en/index.html#page/ThingWorx%2FHelp%2FGetting_Started%2FInstallingandUpgrading%2FInstallation%2Finstall_java_and_apache_tomcat__ubuntu_.html%23
https://www.ptc.com/support/-/media/52CCDE621448456ABB31E6C727E0C1B8.pdf?sc_lang=en
