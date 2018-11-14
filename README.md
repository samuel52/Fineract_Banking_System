# Guide to getting fineract up and running on windows

Fineract is an inclusive banking system used majorly by microfinance banks worldwide. It provides a reliable, robust, and affordable solution for entrepreneurs, financial institutions, and service providers to offer financial services to the world’s 2 billion underbanked and unbanked. Fineract is aimed at innovative mobile and cloud-based solutions, and enables digital transaction accounts for all.

# Steps On How To Set Fineract Up on Windows
# STEP 1: Tool you Need 
- JAVA DEVELOPMENT KIT >> VERSION 1.8.0_65 & above  (http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) Use this guide to set Java up on your Windows https://goo.gl/rSnYUH

- MYSQL SERVER >> Version 5.5 or 5.6 (http://dev.mysql.com/downloads/mysql/5.5.html#downloads) Use this guide to set Java up on your Windows https://goo.gl/rrZAWn

- TOMCAT >> Version 7.0.64.0 & above (http://tomcat.apache.org/download-70.cgi) Use this guide to set Java up on your Windows https://goo.gl/fZPD55

- Gradle 2.10 https://gradle.org/next-steps/?version=2.10&format=all (it downloads immediately you click)
- Git Bash (https://git-scm.com/)
- Postman or any REST Client

# STEP 2: Set Your Environment Variables 
Use this guide below to create your enviornmental varaible on Windows https://goo.gl/YHz6DY

### Checklist
1. Set JAVA_HOME Path
2. Set MySQL Path

# STEP 3: Download Fineract
```
Create a folder in your C: drive named Fineract
cd into the folder

# git clone
do git clone https://github.com/apache/fineract
cd into the cloned dir
cd into fineract-provider

#running gradle
in C:\Fineract\fineract\fineract-provider do 'gradle clean dist'
cd to C:\Fineract\fineract folder you'd find a file name 'build'
unzip the fineractplatfom_version(depending on the version)

Thats all for now
```
# STEP 4: Initailizing MySQL
- Download the HEIDISQL here http://www.heidisql.com/download.php
- Run the Heidisql installation file & setup Heidisql

- For local - Provide mysql password as mysql. 
- Click on Save button.

- Click on open button. 
- Copy the following database command and paste it into the large white box titled Query 
###### create database `mifosplatform-tenants`; create database `mifostenant-default`; use `mifosplatform-tenants`;

- Click on Run button to Execute the query
- On heidisql, click on File>>Load SQL file
- Through Load SQL file window,

      Navigate to the location where you extracted the Mifos application files.
      Navigate to the database folder, inside that location.
      Select the file mifospltaform-tenants-first-time-install.sql and click Open.
      Run the first-time-install script, by clicking Run Query button & once its successful close heidisql application.
      
 # STEP 5: Setting up Tomcat and Final Setups
 - Download tomcat-jdbc.jar file from:http://www.java2s.com/Code/JarDownload/tomcat-jdbc/tomcat-jdbc.jar.zip and paste inside the       C:\tomcat\bin directory ONLY if its not available
 
 - Generate a new keystore using java keytool :

      Open command prompt 
      Go to the path where your JAVA>>bin folder is located. 
      Run this command to generate the key: keytool -genkey -alias tomcat -keyalg RSA
      Then provide the password
      Provide input and type in 'y' for the last question to confirm. 

## Note: Once the keystore is created, it should be placed in your home directory. For Example:C:/Users/john/.keystore
 - Go to  tomcat>>conf>>open server.xml with notepad and pase the code below:
 
 ```
 <?xml version='1.0' encoding='utf-8'?>
<Server port="8005" shutdown="SHUTDOWN">
<Listener className="org.apache.catalina.core.JasperListener" />
<Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
<Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
<Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

<GlobalNamingResources>
<Resource name="UserDatabase" auth="Container"
type="org.apache.catalina.UserDatabase"
description="User database that can be updated and saved"
factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
pathname="conf/tomcat-users.xml"
/>

<Resource type="javax.sql.DataSource"
name="jdbc/mifosplatform-tenants"
factory="org.apache.tomcat.jdbc.pool.DataSourceFactory"
driverClassName="org.drizzle.jdbc.DrizzleDriver" 
url="jdbc:mysql:thin://localhost:3306/mifosplatform-tenants"
username="root"
password="mysql"
initialSize="3"
maxActive="10"
maxIdle="6"
minIdle="3"
validationQuery="SELECT 1"
testOnBorrow="true"
testOnReturn="true"
testWhileIdle="true"
timeBetweenEvictionRunsMillis="30000"
minEvictableIdleTimeMillis="60000"
logAbandoned="true"
suspectTimeout="60"
/>
</GlobalNamingResources>

<Service name="Catalina">

<Connector protocol="org.apache.coyote.http11.Http11Protocol"
port="8443" maxThreads="200" scheme="https"
secure="true" SSLEnabled="true"
keystoreFile="C:/Users/john/.keystore"
keystorePass="xyz123"
clientAuth="false" sslProtocol="TLS"
URIEncoding="UTF-8"
compression="force"
compressableMimeType="text/html,text/xml,text/plain,text/javascript,text/css"/>

<Engine name="Catalina" defaultHost="localhost">

<Realm className="org.apache.catalina.realm.LockOutRealm">
<Realm className="org.apache.catalina.realm.UserDatabaseRealm" resourceName="UserDatabase"/>
</Realm>

<Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">

<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
prefix="localhost_access_log." suffix=".log"
pattern="%h %l %u %t &quot;%r&quot; %s %b" />
</Host>
</Engine>
</Service>
</Server>


 ```
After content above is pasted in server.xml,Below mentioned are to be modified with respect to your system & settings:
    - **Key store path** should be modified with your key store path location **Example:- keystoreFile="C:/Users/john/.keystore"**
    - **Key store password** (which was provided while creating the key), should be modified **Example:- keystorePass="xyz123"**
    - MYSQL credentials should be modified
      **Example:- username="root" & password="mysql"**
      Save File
      
 - Drop Fineract inside Tomcat folder like so, **fineract-provider.war to tomcat>>webapps folder**

   Copy **"fineract-provider.war"** file from unziped folder, **C:\fineract\incubator-fineract\fineract-      provider\build\distributions\fineractplatform-17.07.01.RELEASE**
   
 - Paste it to **tomcat\webapps folder**
 - Go to "C:\tomcat\bin" folder, Run "startup.bat" by clicking it.
      **Note: You can create a shortcut  of "startup.bat" on your desktop and rename it as start mifosx.**
 if your cmd only blinks without starting do this
      - Right click on the **startup.bat** file and edit as notepad
      - add 
          set JAVA_HOME=C:\Program Files\Java\jdk1_version
          set JRE_HOME=C:\Program Files\Java\jre_version
          above @echo off
          
       - Save file
       
       
-  Open below mentioned url in internet broswer and authenticate (for the first time).
    https://localhost:8443/fineract-provider/api/v1/clients?tenantIdentifier=default
    
Finally, Fineract-platform is ready to be used. You could pass the api requests by following the api documents available in the path: C:\fineract\incubator-fineract\fineract-provider\build\distributions\fineractplatform-17.07.01.RELEASE >> api docs

At this stage you can use any REST client to consume the API.

Cheers!



