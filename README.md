# Siem-Lab<p align="center">
<img src="./assets/wazuh_logo.svg" height="70%" width="70%" alt="Sentinel logo"/>
</p>


<h1> Wazuh home lab </h1>
<p>This project is just a readme containing the implementation of my own SIEM tool inside my home lab to monitor my infrastructure and also gain exposure to using a SIEM tool. I opted for Wazuh as my tool because of the cost, it is open source and also it is able to store data for a longer period of time. I looked at other tools like SumoLogic but the free tier has a 7 days retention period. <br />
</p>

<p>While other tools like SumoLogic, Splunk, Sentinel are few of the most used SIEM tools by companies, they do tend to come with a high price for personal use.</p>


<h2>Environments and Technologies Used</h2>

- Linode (now part of Akamai) as the cloud provider for my server where Wazuh is configured
- Ubuntu 22.04 LTS as the server hosting Wazuh
- Docker for running the Wazuh instance
- Different machines to pull the logs from (Personal machines and VM's)
- Jira & Confluence


<h2>Operating Systems Used </h2>

- Server: Ubuntu
- Machines that are pulled into Wazuh: MacOS/Linux/Windows 


<h2> Lab Description </h2>


<p>
I started this lab as a learning oportunity and also to play with log ingestion from personal built programs or small scripts and see how that can be achieved.
While looking for implementing this there was a very nice video, which I recommend you check, from NetworkChuck. You can follow the following link to access it: <a href=https://www.youtube.com/watch?v=3CaG2GI1kn0>Wazuh video from NetworkChuck</a>
</p>
<br>
                                                                                    
<p>
<img src="./assets/Linode_config.png"80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the video Chuck mentions that he got it up and running using a 2GB Ram container. That is doable, but while using it you do see an increased latency and if you are like me you might not like waiting. While waiting might be something you can go over, one thing that I definetly did not like was that after configuring a setting, which I will show a bit down the road, to notify me in real time about a change in a specific file, with the 2GB RAM config it was no longer realtime. It took about 30 seconds to send you the notification.
<br>
As such I opted to bump the server from a 2GB to a 4GB machine.
</p>
                                                                                     

<h3>Config change</h3>

***

<h4>Default password change</h4>

The default instalation of Wazuh will come with a simple password and it is not really a good thing to keep while also having my SIEM accesible over the internet at this time. To fix this I followed their documentation to change the password. To keep track of all the changes I set myself a Jira instance and a Conflunece instance. Bellow I am adding a picture of the created ticket.

Steps done during this change:

* Made a backup of the linode machine
* Turned of the container
* Followed the steps in the documentation: [Password Change](https://documentation.wazuh.com/current/deployment-options/docker/wazuh-container.html#change-the-password-of-wazuh-users)
* Turned the docker container back online
* Tested the configuration - If you are signed in before doing the changes you will get an error due to browser cookie
* Closed Jira ticket as complete


<p>
<img src="./assets/update_config_password.png"80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<h4>Upgrade of the Linode machine</h4>
<p>
This change was quite simple as most of it is done by Linode itself. To acomplish this again I created a Jira ticket for traking and then followed Linode documentation on resizing the machine from 2GB to 4GB option. Once completed I could confirm that the system runs much better now and the ram utilization is down to 45%. Notifications are also comming in real-time as expected.
</p>
<br />
                                                                                        
 <p>
<img src="./assets/change_ticket.png" width="80%" alt="Disk Sanitization Steps"/>
</p>


<h2>Wazuh dashboard</h2>                                                                                     
                                                                                        
<p>
<img src="./assets/wazuh_connected_systems.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
After configuring the system with a new password, and also linking the server to a subdomain linked to my own domain, I started installing the agents and connect several machines to the system.

Main machines that I always want to monitor:

1. Siem Server
2. Personal Windows machine
3. Mac Laptop

<br>
With those machines configured I went ahead and enabled the vulnerability scanner for Wazuh. To achieve this I used the following documentation and links:

1. [Wazuh Vuln detection](https://documentation.wazuh.com/current/user-manual/capabilities/vulnerability-detection/how-it-works.html)
2. [Wazuh config and running scans](https://documentation.wazuh.com/current/user-manual/capabilities/vulnerability-detection/configuring-scans.html)

As the Siem server is hosted on Ubuntu 22.04 I had to add `<os>jammy</os>` to the `ossec.conf` file so that the system does a vulnerability scan on those systems as well. 
To turn the vuln scanner `on` you need to either use the GUI or edit the config inside the docker container. I find that the GUI is simpler :). 

The end configuration looks like this:

```
 <vulnerability-detector>
    <enabled>yes</enabled>
    <interval>5m</interval>
    <min_full_scan_interval>6h</min_full_scan_interval>
    <run_on_start>yes</run_on_start>

    <!-- Ubuntu OS vulnerabilities -->
    <provider name="canonical">
      <enabled>yes</enabled>
      <os>jammy</os>
      <os>trusty</os>
      <os>xenial</os>
      <os>bionic</os>
      <os>focal</os>
      <update_interval>1h</update_interval>
    </provider>

    <!-- Debian OS vulnerabilities -->
    <provider name="debian">
      <enabled>no</enabled>
      <os>stretch</os>
      <os>buster</os>
      <os>bullseye</os>
      <update_interval>1h</update_interval>
    </provider>

    <!-- RedHat OS vulnerabilities -->
    <provider name="redhat">
      <enabled>no</enabled>
      <os>5</os>
      <os>6</os>
      <os>7</os>
      <os>8</os>
      <update_interval>1h</update_interval>
    </provider>

    <!-- Amazon Linux OS vulnerabilities -->
    <provider name="alas">
      <enabled>no</enabled>
      <os>amazon-linux</os>
      <os>amazon-linux-2</os>
      <update_interval>1h</update_interval>
    </provider>

    <!-- Arch OS vulnerabilities -->
    <provider name="arch">
      <enabled>no</enabled>
      <update_interval>1h</update_interval>
    </provider>

    <!-- Windows OS vulnerabilities -->
    <provider name="msu">
      <enabled>yes</enabled>
      <update_interval>1h</update_interval>
    </provider>

    <!-- Aggregate vulnerabilities -->
    <provider name="nvd">
      <enabled>yes</enabled>
      <update_interval>1h</update_interval>
    </provider>

  </vulnerability-detector>
```


## Vulnerabilities found

With the vuln scanner turned on, the scanner was able to find vulnerabilties and that made me happy that it was able to find some things to fix on my own machine. It allows me to learn a few more stuff this way.

Bellow I will add all the findings and keep updating this as new ones appear:

1. [03/09/2023 PersonalMachine Vulnerabilities found](./Vulnerabilities%20report/personalMachine_03_09_2023_vuln.md)
2. [siem_docker Vulnerabilities found](#)


</p>                 


 Thank you for viewing. 
</p>
<br />
