# How-to Setup AEM As A Service on Linux (CentOS and Ubuntu)
Details on how-to setup AEM as a Service on Linux. Used CentOS 7 and Ubuntu 17.04 as an example.

## Pre-requisites
1. AEM Installed on your server. Copy the path of the install (e.g: /mnt/crx)
2. Java (atleast JRE) installed (To test if java is installed and the version, run this command `java -version`) 
3. Start AEM (e.g `java -jar cq-quickstart-author-p4502.jar`) once. This will generate all the necessary folders, especially **/mnt/crx/crx-quickstart/bin** that is required by the scripts.
4. Create a user who will have access to the service. (e.g: aem)

## Step-by-step guide
1. You will need root access
2. Create user aem and add to sudoers group
   * `adduser aem`   
   * `usermod -aG sudo aem`   
   * Test the user 
     * `su - aem`   
     * `sudo ls -la /root` [This is accessible by root only]
2. Create these 2 files (Get the contents of the files based on the OS. Under /centos or /ubutnu in this project)
   * aem
   * aem.service
3. Open `aem` script file and update the below
   * AEM_ROOT (e.g: `/mnt/crx` is the root, where `/mnt/crx/crx-quickstart` is the full path)
   * AEM_USER (e.g: `aem`) 
4. SCP these files to the server
   * Copy `aem` to `/usr/bin`
     * Example: From terminal on your desktop `$ scp <filename> user@1.1.1.1:/usr/bin/aem`
   * Copy `aem.service` to `/etc/system.d/system` (if Ubuntu - `/etc/systemd/system`)
     * Example: From terminal on your desktop `$ scp <filename> user@1.1.1.1:/etc/system.d/system/aem.system`
5. SSH to your server
   * `ssh user@1.1.1.1`
6. Give permissions to the files
   * `sudo chmod u+rwx /usr/bin/aem`
   * `sudo chmod u+rw /etc/system.d/system/aem.service` (if Ubuntu - `/etc/systemd/system/aem.service`)
7. Update 
   * `cd /etc/system.d/system` (if Ubuntu - `/etc/systemd/system`)
   * `sudo systemctl enable aem.service`
8. You can restart the server or run the below commands to start AEM. Make sure you run **Pre-requisite Step 2** before running this command.

## Commands to START, RESTART and STOP AEM
### Centos
* Start AEM - `sudo service aem start`
* Restart AEM - `sudo service aem restart`
* Stop AEM - `sudo service aem stop`
* Check Status of the service `sudo service aem status`

### Ubuntu
* Start AEM - `sudo systemctl start aem`
* Restart AEM - `sudo systemctl restart aem`
* Stop AEM - `sudo systemctl stop aem`
* Check Status of AEM `sudo systemctl status aem`

## Test if AEM is running
There are several ways we can test if AEM is running with the above commands
1. Run the command `ps -ef | grep java` , you should see something like this
   * `root      12958      1  5 20:39 ?        00:02:41 java -server -Xmx1024m -XX:MaxPermSize=256M -Djava.awt.headless=true -Dsling.run.modes=author,crx3,crx3tar -Djava.locale.providers=CLDR,JRE,SPI -jar crx-quickstart/app/cq-quickstart-6.4.0-standalone-quickstart.jar start -c crx-quickstart -i launchpad -p 4502 -Dsling.properties=conf/sling.properties
`
2. Check if port 4502 is being used `sudo lsof -i -P -n | grep LISTEN` 

3. Test AEM in another command/terminal window using cUrl as `curl -I http://localhost:4502`

4. Test AEM in browser `http://<vm-ip-address>:4502` 

## Troubleshooting
### Installing Oracle Java 8 Manually in Ubuntu 18x
1. Download the `tar.gz` file "https://download.oracle.com/otn/java/jdk/8u231-b11/5b13a193868b4bf28bcb45c792fce896/jdk-8u 231-linux-x64.tar.gz" to your local machine. Downloading to your linux VM maybe not possible, as the above url needs a login.
2. Copy the file to the server using SCP
3. Make a directory: `mkdir /opt/java`
4. Install and copy: `sudo tar -zxf jdk-8u231-linux-x64.tar.gz -C /opt/java/`
5. Update your environment to allow Java for all users. Run this command `sudo update-alternatives --install /usr/bin/java java /opt/java/jdk1.8.0_231/bin/java 1`
6. Add path to ".bashrc". Open to edit `sudo nano ~/.bashrc`
   - Add these lines to the end of the file:
     
     `export JAVA_HOME=/opt/java/jdk1.8.0_231`
     
     `export PATH=${PATH}:${JAVA_HOME}/bin`

### Others:
1. If the command shows `active` and still AEM does not load (On a browser `http://localhost:4502`) then check the AEM logs. Path for the log file: `/<aem-folder>/crx-quickstart/logs/stdout.log` 

## Notes
1. The example above was tested on CentOS 7 and Ubuntu 17x, 18x
2. AEM 6.3 version was used. Although the above process should work for AEM 6.x
