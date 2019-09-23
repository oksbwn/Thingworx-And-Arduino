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

## Setup Java and Tomcat in Ubuntu

`$sudo apt-get update`
`$sudo apt-get install ntp #Network Time Protocol (NTP) settings for time synchronization`
`$sudo apt-get install authbind #AUTHBIND properties to allow Tomcat to bind to ports below 1024`

Download JAVA JDK from Oracle's official website. OpenJDK is not supported.
`$wget -c --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz`

Unzip downloaded .tar.gz file 

`$tar -xf jdk-8u131-linux-x64.tar.gz`

sudo mkdir -p /usr/lib/jvm
sudo mv jdk1.8.0_131/ /usr/lib/jvm/
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.8.0_131/bin/java" 1
sudo update-alternatives --install "/usr/bin/keytool" "keytool" "/usr/lib/jvm/jdk1.8.0_131/bin/keytool" 1


sudo chmod a+x /usr/bin/java
sudo chmod a+x /usr/bin/keytool

sudo chown -R root:root /usr/lib/jvm/jdk1.8.0_131/

sudo update-alternatives --config java
sudo update-alternatives --config keytool

Check installed Java version,
`$java -version`

### Install tomcat

Download,
`$wget http://mirrors.estointernet.in/apache/tomcat/tomcat-8/v8.5.46/bin/apache-tomcat-8.5.46.tar.gz`


https://support.ptc.com/help/thingworx_hc/thingworx_8_hc/en/index.html#page/ThingWorx%2FHelp%2FGetting_Started%2FInstallingandUpgrading%2FInstallation%2Finstall_java_and_apache_tomcat__ubuntu_.html%23
https://www.ptc.com/support/-/media/52CCDE621448456ABB31E6C727E0C1B8.pdf?sc_lang=en
