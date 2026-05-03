# Upload Vulnerabilities
**Attacker IP:** 10.65.124.177 
<br>**Target IP:** 10.65.136.187 

**Start Date and time:** 4/26/2026  10:10am 
<br>**End Date and Time:**  4/26/2026 12:25pm 

**Tools Used:** gobuster, Burpsuite, Wappalyser 

## OVERWRITING EXISTING FILES 
I navigated to overwrite.uploadvulns.thm and found a simple upload form. After inspecting the page source I confirmed there was no filename validation on the form. I saved a local image as mountains.jpg — the same filename as an existing file already stored on the server — and uploaded it. The server accepted it without checking for filename conflicts and replaced the original file. The success message confirmed the overwrite and returned the flag.

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV2.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV3.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV4.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV5.png)

## REMOTE CODE EXECUTION 
I ran dirb against shell.uploadvulns.thm and discovered /resources/ as a listable directory. I created webshell.php containing a single PHP line that accepts a URL parameter called cmd and executes it as a system command on the server. Since the server had no file type restrictions, it accepted the PHP file without issue. I confirmed the upload by browsing to /resources/ and seeing the file listed there. I triggered it through the browser using ?cmd=id;whoami;ls, which returned the server's user (www-data) and directory contents, confirming remote code execution. I then started a Netcat listener on port 1234, sent a reverse bash shell payload through the webshell URL, and caught an interactive shell on the target. From there I ran cat /var/www/flag.txt to read the flag.

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV6.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV7.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV8.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV9.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV10.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV11.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV12.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV13.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV14.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV15.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV16.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV17.png)

## BYPASSING CLIENT SIDE FILTERING 
The target at java.uploadvulns.thm had a JavaScript filter (client-side-filter.js) that blocked non-image file uploads in the browser before they reached the server. I set up Burp Suite as a proxy via FoxyProxy, turned intercept on, and refreshed the page. When the server's HTML response came through Burp, I located the script tag loading the filter and deleted it before forwarding the modified response to the browser. The page loaded without the filter ever executing. I ran dirb to find the upload directory (/images/), uploaded webshell.php without any restriction, confirmed it in the directory listing, and triggered a reverse shell the same way as before, catching it on my Netcat listener.

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV18.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV19.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV20.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV21.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV22.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV23.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV24.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV25.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV26.png)

## BYPASSING SERVER SIDE FILTERING: FILE EXTENSIONS 
At annex.uploadvulns.thm the server was blocking .php and .phtml extensions server-side. I ran dirb and found /privacy/ as the upload directory. Uploading webshell.php directly returned "File type is invalid." I created copies of the webshell with alternative PHP extensions and tried them one by one — .phar was rejected but .php5 was accepted. The server also randomized the filename on upload, prepending the date and time, so the file was saved as 2026-04-26-17-01-33-webshell.php5. I found this by browsing /privacy/, then used the full randomized filename in the reverse shell trigger URL and caught the shell on my Netcat listener.

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV27.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV28.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV29.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV30.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV31.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV32.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV33.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV34.png)

## BYPASSING SERVER – SIDE FILTERING: MAGIC NUMBERS 
At magic.uploadvulns.thm the server validated file type by reading the first bytes of the file (magic numbers) rather than checking the extension. Attempting a regular upload returned "GIF files only please", telling me exactly what magic number I needed to spoof. I edited webshell.php in nano and added GIF89a as the very first line — this is the ASCII magic number that identifies GIF image files. I verified the spoof worked by running the file command, which returned "GIF image data" confirming the OS now identified it as a GIF. I uploaded it and the server accepted it. Since directory indexing was disabled on /graphics/, I couldn't browse the directory — I accessed the shell directly via its URI and triggered the reverse shell, catching it on my Netcat listener and reading the flag from /var/www/.

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/UV1.png)
 
## Threat Intel (MITRE Mapping)
| Tactic | Technique ID | Technique Name | How It Was Used |
|--------|-------------|----------------|-----------------|
| Initial Access | T1190 | Exploit Public-Facing Application | All 5 attacks exploited insecure file upload functionality on a public-facing web application to gain unauthorized server access |
| Execution | T1059.004 | Command and Scripting Interpreter: Unix Shell | Reverse bash shell established via webshell gave interactive command execution on the target as www-data |
| Execution | T1059.005 | Command and Scripting Interpreter: PHP | PHP webshell used to execute arbitrary OS commands via HTTP GET requests using the ?cmd= parameter |
| Persistence | T1505.003 | Server Software Component: Web Shell | Uploaded webshell.php acted as a persistent backdoor, accessible across requests and sessions |
| Defense Evasion | T1036.008 | Masquerading: Masquerade File Type | Prepended GIF89a magic number to PHP file to pass server-side magic number inspection |
| Defense Evasion | T1027 | Obfuscated Files or Information | Renamed PHP webshell with .php5 extension to evade the server-side blacklist blocking .php and .phtml |
| Discovery | T1083 | File and Directory Discovery | Used dirb to enumerate web directories and identify upload paths across all target vhosts |
| Collection | T1005 | Data from Local System | After gaining shell access, read sensitive files from the target using cat /var/www/flag.txt |
| Exfiltration | T1048 | Exfiltration Over Alternative Protocol | Flag data was exfiltrated back over the reverse shell TCP connection on port 1234 |
| Command and Control | T1071.001 | Application Layer Protocol: Web Protocols | Webshell communicated via standard HTTP GET requests, blending with normal web traffic |
| Command and Control | T1095 | Non-Application Layer Protocol | Netcat reverse shell used raw TCP for interactive C2 between the target and my attacker machine |
| Impact | T1565.001 | Data Manipulation: Stored Data Manipulation | Overwrote an existing server file (mountains.jpg) by uploading a file with an identical name |
 

 

 
