# Thingworx 8.4

## Requirements
1. **OS/Platform**: 
    1. Windows Server 2016/2012 R2/ 2008 R2 SP1
    2. Red Hat Enterprise Linux (RHEL) 7.5
    3. Ubuntu 14.04 LTS / 16.04 LTS / 18.04 LTS
2. **Java JDK**: Java SE Development Kit 8, Update 92 , 1.8.0_92- b14 (64-bit)
    > OpenJDK not supported
3. **Tomcat** : 
    1. 8.5.42 (64- bit)
    2. 9.0.21 (64- bit)
2. **Database**:    
    1. PostgreSQL (9.4.5, 9.5.11,9.6, 10)
    2. DataStaxEnterpriseEdition (4.6.3, 5)
    3. Microsoft SQL Server (2016)
    4. AzureSQL (Azure SQL Logical ServerV12)
    5. InfluxDB (1.6.3)  `Not supported for use with ThingWorx Flow.`

## Setting up Ubuntu in VM
1. Download latest version of Ubuntu from respective website. [Dwonload (1.9 GiB)](http://releases.ubuntu.com/18.04/ubuntu-18.04.3-desktop-amd64.iso)
2. Start Oracle VM VirtualBox. (In ITC can be installed from Software Center)
3. Install UBUNTU to the VM. Can follow [this](https://www.wikihow.com/Install-Ubuntu-on-VirtualBox) guide.

## Download and Setup Oracle JDK

> `ntp` is installed for time sunchronization whereas authbind 
```
$sudo apt-get update
$sudo apt-get install ntp #Network Time Protocol (NTP) settings for time synchronization
$sudo apt-get install authbind #AUTHBIND properties to allow Tomcat to bind to ports below 1024
```

Download JAVA JDK from Oracle's official website. OpenJDK is not supported.

```
$wget -c --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz
$tar -xf jdk-8u131-linux-x64.tar.gz
sudo mkdir -p /usr/lib/jvm
sudo mv jdk1.8.0_131/ /usr/lib/jvm/
```

```
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk1.8.0_131/bin/java" 1
sudo update-alternatives --install "/usr/bin/keytool" "keytool" "/usr/lib/jvm/jdk1.8.0_131/bin/keytool" 1

sudo chmod a+x /usr/bin/java
sudo chmod a+x /usr/bin/keytool

sudo chown -R root:root /usr/lib/jvm/jdk1.8.0_131/

sudo update-alternatives --config java
sudo update-alternatives --config keytool
```
> Check installed Java version `$java -version`

### Download and Setup Apache Tomcat

```
$wget http://mirrors.estointernet.in/apache/tomcat/tomcat-8/v8.5.46/bin/apache-tomcat-8.5.46.tar.gz
tar -xf apache-tomcat-8.5.46.tar.gz
sudo mkdir -p /usr/share/tomcat8.5
sudo mv apache-tomcat-8.5.46 /usr/share/tomcat8.5/8.5
```

```
sudo addgroup --system tomcat8.5 --quiet -force-badname
sudo adduser --system --home /usr/share/tomcat8.5/ --no-create-home --ingroup tomcat8.5 --disabled-password --force-badname --shell /bin/false tomcat8.5

sudo chown -R tomcat8.5:tomcat8.5 /usr/share/tomcat8.5
```

```
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_131
export CATALINA_HOME=/usr/share/tomcat8.5/8.5
```
```
cd $CATALINA_HOME
sudo chown -Rh tomcat8.5:tomcat8.5 bin/ lib/ webapps/
sudo chmod 775 bin/ lib/ webapps/
sudo chown -Rh root:tomcat8.5 conf/
sudo chmod -R 650 conf/
sudo chown -R tomcat8.5:adm logs/ temp/ work/
sudo chmod 760 logs/ temp/ work/
```

## Setting up SSL
sudo $JAVA_HOME/bin/keytool -genkey -alias tomcat8.5 -keyalg RSA -keystore $CATALINA_HOME/conf/.keystore
> Keystore password: thingworxptc

sudo chown root:tomcat8.5 $CATALINA_HOME/conf/.keystore
sudo chmod 640 $CATALINA_HOME/conf/.keystore

## Uncomment the Manager element in context.xml to prevent sessions from persisting across restarts:
<Manager pathname="" />

sudo nano conf/context.xml

## Comment in server.xml
<!--
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
--> 
sudo nano $CATALINA_HOME/conf/server.xml
and add the code below
<Connector port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
keystoreFile="${user.home}/8.5/conf/.keystore" keystorePass="thingworxptc" 
clientAuth="false" sslProtocol="TLS" />

### If you receive an error that the directory doesn’t exist, use the following
commands to ensure port 443 works:
sudo touch /etc/authbind/byport/443
sudo chmod 700 /etc/authbind/byport/443
sudo chown tomcat8.5:tomcat8.5 /etc/authbind/byport/443

## Create a tomcat user

sudo nano $CATALINA_HOME/conf/tomcat-users.xml
Add the following line,

<user username="bikash" password="password" roles="manager"/>

## Determine uid of tomcat8.5 user:
$ id -u tomcat8.5
Using this number, create an ID file in /etc/authbind/byuid/:
Note
Change the <uid> to the number that was returned in the previous step.
$ sudo touch /etc/authbind/byuid/<uid>

sudo vi /etc/authbind/byuid/<uid>
28. Edit the file from the step above and paste in the following:
0.0.0.0/0:1,1023

Change owner and access permissions of /etc/authbind/byuid/
<uid>:
$ sudo chown tomcat8.5:tomcat8.5 /etc/authbind/byuid/<uid>
$ sudo chmod 700 /etc/authbind/byuid/<uid>
30. Modify $CATALINA_HOME/bin/startup.sh to always use authbind:
sudo vi $CATALINA_HOME/bin/startup.sh
Comment the following in the file:
#exec "$PRGDIR"/"$EXECUTABLE" start "$@"
  
exec authbind --deep "$PRGDIR"/"$EXECUTABLE" start "$@"

## 
sudo touch /etc/init.d/tomcat8.5
33. Edit the file and enter the following contents:
$ sudo vi /etc/init.d/tomcat8.5

CATALINA_HOME=/usr/share/tomcat8.5/8.5
case $1 in
start)
/bin/su -p -s /bin/sh tomcat8.5 $CATALINA_HOME/bin/startup.sh
;;
stop)
/bin/su -p -s /bin/sh tomcat8.5 $CATALINA_HOME/bin/shutdown.sh
;;
restart)
/bin/su -p -s /bin/sh tomcat8.5 $CATALINA_HOME/bin/shutdown.sh
/bin/su -p -s /bin/sh tomcat8.5 $CATALINA_HOME/bin/startup.sh
;;
esac
exit 0

## 
sudo chmod 755 /etc/init.d/tomcat8.5
$ sudo ln -s /etc/init.d/tomcat8.5 /etc/rc1.d/K99tomcat
$ sudo ln -s /etc/init.d/tomcat8.5 /etc/rc2.d/S99tomcat

--Pending--
## Set up Tomcat as a service to start on boot. First, build JSVC:
sudo apt-get install gcc
$ cd /usr/share/tomcat8.5/8.5/bin/
$ sudo tar xvfz commons-daemon-native.tar.gz
$ cd commons-daemon-*-native-src/unix
$ sudo ./configure --with-java=$JAVA_HOME
$ sudo apt-get install make

sudo make
$ sudo cp jsvc ../..
37. Create the Tomcat service file:
sudo touch /etc/systemd/system/tomcat8.5.service
38. Open /etc/systemd/system/tomcat8.5.service in a text editor
(as root):
sudo vi /etc/systemd/system/tomcat8.5.service
39. Paste the following in the Tomcat service file:
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target
[Service]
Type=forking
PIDFile=/var/run/tomcat.pid
Environment=CATALINA_PID=/var/run/tomcat.pid
Environment=JAVA_HOME=/usr/lib/jvm/jdk1.8.0_xxx
Environment=CATALINA_HOME=/usr/share/tomcat8.5/8.5.xx
Environment=CATALINA_BASE=/usr/share/tomcat8.5/8.5.xx
Environment=CATALINA_OPTS=
ExecStart=/usr/share/tomcat8.5/8.5.xx/bin/jsvc \
-Dcatalina.home=${CATALINA_HOME} \
-Dcatalina.base=${CATALINA_BASE} \
-Djava.awt.headless=true -Djava.net.
preferIPv4Stack=true -Dserver -Dd64 -XX:+UseNUMA \
-XX:+UseG1GC -Dfile.encoding=UTF-8 \
-Djava.library.path=${CATALINA_BASE}/webapps/
Thingworx/WEB-INF/extensions \
-cp ${CATALINA_HOME}/bin/commons-daemon.jar:
${CATALINA_HOME}/bin/bootstrap.jar:${CATALINA_HOME}/bin/tomcat-juli.jar \
-user tomcat8.5 \
-java-home ${JAVA_HOME} \
-pidfile /var/run/tomcat.pid \
-errfile ${CATALINA_HOME}/logs/catalina.out \
-outfile ${CATALINA_HOME}/logs/catalina.out \
$CATALINA_OPTS \
org.apache.catalina.startup.Bootstrap
[Install]
WantedBy=multi-user.target

## Create a new file in the tomcat /bin file named setenv.sh:
cd $CATALINA_HOME/bin
sudo touch setenv.sh
sudo vi setenv.sh





https://support.ptc.com/help/thingworx_hc/thingworx_8_hc/en/index.html#page/ThingWorx%2FHelp%2FGetting_Started%2FInstallingandUpgrading%2FInstallation%2Finstall_java_and_apache_tomcat__ubuntu_.html%23
https://www.ptc.com/support/-/media/52CCDE621448456ABB31E6C727E0C1B8.pdf?sc_lang=en
