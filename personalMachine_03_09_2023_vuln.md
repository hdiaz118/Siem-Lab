# Windows vuln scanner results


Once I enabled vuln scanner I was presently surpsied to see that the scan now functions and it has actually found problems on my windows system. 

As such bellow I am presenting what was found and how I went ahead and remediated each one that was required or thought to be needed. If I thought one to not require fix I will detail the reason bellow.


As we can see from the picture bellow it did find quite a few of them:

<p>
<img src="../assets/03_09_2023/1.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Having a glance at them it seems that checking the **critical** and **high** would be the best way to start as they pose the biggest threat to the system.

## Critical vulnerabilities found

### Wireshark 4.0.1

<p>
<img src="../assets/03_09_2023/2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

- As seen in my picture above, my version was 4.0.1. Expanding the description we can find all that we can see visiting the online sources. 
- This is a vulnerability linked to not having the application to the latest version. I find that this would be a common issue in companies as well where some computers might have old and not updated software
	- It is a good practice to keep your software up to date and remove software that you do not use. Something that I have failed to do so here
	- [CVE-2023-0667](https://nvd.nist.gov/vuln/detail/CVE-2023-0667)
	- Checking the vulnerability it seems that versions bellow 4.0.5 are susceptible to a heap-based buffer overflow. That is not good

**Remediation:**
- What I have went with for now is updating the software. Might uninstall it as I am not currently using it later on and move it into one of my VM's where I will connect the agents as well.
- I find that this to be an easy solution and a fast remediation

### Python 3.10.0

<p>
<img src="../assets/03_09_2023/3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

- Version installed on my machine: 3.10.5150.0
- Checking the information is seems that the version affected is up to 3.10.9 but not including that one.
- Description of the CVE: The Keccak XKCP SHA-3 reference implementation before fdc6fef has an integer overflow and resultant buffer overflow that allows attackers to execute arbitrary code or eliminate expected cryptographic properties. This occurs in the sponge function interface.
- [CVE-2022-37454](https://nvd.nist.gov/vuln/detail/CVE-2022-37454)

**Remediation:**
- To fix this there are two steps. One is to uninstall the old version and install the most recent version which is 3.11.5
- I opted to uninstall for the sole reason I mainly use it inside of WSL so it should be running only there. For now


## High Vulnerabilities found

Well here will be a long road ahead as we have 15 it seems. 
What looks to be interesting it seems that most of them appear to be linked to the Python and Wireshark ones all providing different CVE's. Ok. Those should be fixed. Letting the scanner to confirm once it does a new check. One thing that I have found while looking at the exported information it seems that there is a vulnerability that affected Python up to 3.11.4. Latest version of python is 3.11.5.

<p>
<img src="../assets/03_09_2023/4.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Taking those out of the equation it leaves me 1. 

### VLC 3.0.17.4

<p>
<img src="../assets/03_09_2023/5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

- Again this looks to be a vulnerability linked to the fact that I have not updated my software. Bad thing to do. 
- Description seems to be pointing at a possible RCE maybe? Interesting
- Description of the CVE: An integer overflow in the VNC module in VideoLAN VLC Media Player through 3.0.17.4 allows attackers, by tricking a user into opening a crafted playlist or connecting to a rogue VNC server, to crash VLC or execute code under some conditions.
- Looks to be affecting the versions up to and including 3.0.17.4 and was published in December of last year. Well seems I was very lazy in updating my software.

**Remediation:**
- I opted with updating as I am using this software from time to time.
- VLC is now updated to 3.0.18

## Medium Vulnerabilities found

- 24 linked to both Python and Wireshark.
- Should be fixed in the next scan. 
<p>
<img src="../assets/03_09_2023/6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>



## Remediation conclusion

Once the issues found have been fixed, we can confirm that at the next scan nothing new was found. Which was very good.

<p>
<img src="../assets/03_09_2023/7.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>