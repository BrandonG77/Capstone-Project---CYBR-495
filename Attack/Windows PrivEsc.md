# Windows PrivEsc
**Attack Start Date:** 4/19/2026 
<br>**Attack Start Time:** 1:05 PM 

**Attack End Date:** 4/19/2026 
<br>**Attack End Time:** 2:15 PM 

**Target IP Address:** 10.64.135.176 
<br>**Attacker IP Address:** 10.64.112.93 

**Tools Used:**  msfvenom, netcat, impacket (smbserver/psexec), accesschk.exe, hashcat, socat, RoguePotato.exe, PrintSpoofer.exe, PSExec64.exe 

## IP ADDRESS LOG 
Due to multiple VM crashes and machine resets throughout the engagement, the following IP addresses were used across different sessions: 

| Session | Target Machine IP | Attacker Machine IP | Tasks Covered |
|---------|-------------------|---------------------|---------------|
| 1 | 10.64.135.176 | 10.64.112.93 | Tasks 1–4 (Reverse Shell Setup, Insecure Service Permissions, Unquoted Service Path) |
| 2 | 10.65.154.1 | 10.65.83.21 | Tasks 6–12 (Insecure Service Executables through Passwords/Pass the Hash) |
| 3 | 10.67.140.189 | 10.67.81.155 | Tasks 13–17 (Scheduled Tasks through PrintSpoofer) |

Each session required regenerating reverse.exe with the updated LHOST value and re-transferring it to the target machine. 

## GENERATING A REVERSE SHELL EXECUTABLE 
A reverse shell executable was generated using msfvenom on the attacker machine. The payload windows/x64/shell_reverse_tcp was used, configured with the attacker's IP (LHOST) and port 4444 (LPORT). The output was saved as reverse.exe. The file was transferred to the Windows target machine using an SMB server hosted on the attacker machine via impacket's smbserver.py. Once transferred to C:\PrivEsc\reverse.exe, a netcat listener was started on the attacker machine and the executable was run on the target, successfully establishing a reverse shell connection running as the low-privilege user account. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE2.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE3.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE4.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE5.png)

## SERVICE EXPLOIT – INSECURE SERVICE PERMISSIONS 
This technique exploits a misconfigured service where a low-privileged user has been granted the ability to modify the service configuration (SERVICE_CHANGE_CONFIG). Using accesschk.exe, it was confirmed that the user account had write permissions on the daclsvc service. Querying the service with sc qc daclsvc revealed the original BINARY_PATH_NAME was C:\Program Files\DACL Service\daclservice.exe and the service runs as LocalSystem (SYSTEM privileges). The service's binary path was modified to point to reverse.exe using sc config. A netcat listener was started and the service was started with net start daclsvc, triggering a callback and establishing a shell with SYSTEM privileges (C:\Windows\system32>). 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE6.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE7.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE8.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE9.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE10.png)

## SERVICE EXPLOITS – UNQUOTED SERVICE PATH 
When a Windows service's binary path contains spaces and is not enclosed in quotes, Windows attempts to resolve the executable path in multiple locations. This behavior was exploited by placing a malicious executable in one of the candidate paths. The unquotedsvc service was found to have an unquoted path: C:\Program Files\Unquoted Path Service\Common Files\unquotedpathservice.exe. Using accesschk.exe, it was confirmed that BUILTIN\Users had write access to C:\Program Files\Unquoted Path Service\. The reverse.exe was copied and renamed to Common.exe in that directory. When the service was started, Windows resolved the path incorrectly and executed Common.exe as SYSTEM, delivering a SYSTEM shell to the attacker's listener. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE11.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE12.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE13.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE14.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE15.png)

## SERVICE EXPLOITS --- WEAK REGISTRY PERMISSIONS 
Windows stores service configurations in the registry under HKLM\SYSTEM\CurrentControlSet\Services. If a low-privileged user has write access to a service's registry key, they can modify the executable path. Using accesschk.exe, it was found that BUILTIN\Users and NT AUTHORITY\INTERACTIVE had full write access to the registry key for the regsvc service. The ImagePath value was modified using reg add to point to reverse.exe. When the service was started, it executed the modified path as SYSTEM, resulting in a SYSTEM-level reverse shell. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE15.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE16.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE17.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE18.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE19.png)

## SERVICE EXPLOITS – INSECURE SERVICE EXECTUABLES 
This technique involves a service whose executable binary itself is writable by low-privileged users. Using accesschk.exe, it was confirmed that BUILTIN\Users had FILE_ALL_ACCESS on C:\Program Files\File Permissions Service\filepermservice.exe. The legitimate service binary was replaced with reverse.exe using the /Y flag to force overwrite. A listener was started and the service was started with net start filepermsvc, executing the malicious binary as SYSTEM and returning a SYSTEM shell. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE20.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE21.png)

 ----- at this point I had to terminate the machine so there is a new target and attacker IP 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE22.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE23.png)

## REGSISTRY --- AUTORUNS 
Windows AutoRun entries in the registry automatically execute specified programs on startup or user login. Querying HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run revealed an entry for C:\Program Files\Autorun Program\program.exe. Using accesschk.exe, it was confirmed that BUILTIN\Users had write access to this executable. The file was replaced with reverse.exe. A netcat listener was started and a new RDP session was initiated using rdesktop. When the admin logged in, the AutoRun entry fired and executed the malicious binary, returning a shell with admin privileges. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE24.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE25.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE26.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE27.png)

## REGISTRY – ALWAYSINSTALLELEVATED 
The AlwaysInstallElevated policy, when enabled in both HKCU and HKLM registry hives, allows any user to install MSI packages with SYSTEM privileges. Both keys were queried and confirmed to be set to 0x1. A malicious MSI payload was generated using msfvenom with the msi format flag and transferred to the target via HTTP server and certutil. A netcat listener was started and the installer was executed using msiexec /quiet /qn /i, which ran the payload as SYSTEM and returned a SYSTEM shell. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE28.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE29.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE30.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE31.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE32.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE33.png)

## PASSWORDS – REGISTRY 
Windows sometimes stores credentials in the registry, particularly in the AutoLogon configuration keys. Querying HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon revealed the DefaultUserName set to admin. No DefaultPassword value was present in this instance, however the known password password123 was used. The psexec.py tool from impacket was used with the admin credentials to spawn a SYSTEM-level command shell on the target. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE34.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE35.png)

## PASSWORDS – SAVED CREDS 
Windows Credential Manager can store user credentials for reuse. Running cmdkey /list on the target revealed saved credentials for the admin account. Using the runas /savecred command, it was possible to execute programs as admin without needing to know the password directly. The reverse.exe payload was executed using the saved admin credentials, and a netcat listener captured the resulting shell running with admin privileges (C:\Windows\system32>). 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE36.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE37.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE38.png)

## PASSWORD – SECURITY ACCOUNT MANAGER (SAM) 
The SAM database stores Windows user password hashes. This VM had insecure backups of the SAM and SYSTEM files stored in C:\Windows\Repair\. These files were transferred to the attacker machine via the SMB share. The creddump7 tool was used to extract NTLM hashes from the SAM file using the SYSTEM file as the key. The admin NTLM hash a9fdfa038c4b75ebc76dc855dd74f0da was recovered. Hashcat was run against the hash using the rockyou.txt wordlist with mode 1000 (NTLM), successfully cracking it to reveal the plaintext password password123. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE39.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE40.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE41.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE42.png)

## SCHEDULED TASKS 
**[new IPs again]**

**Target Machine IP:** 10.67.140.189 
<br>**Attacker Machine IP:** 10.67.81.155 

**Start time:** 4/22/2026 9:25am 
<br>**End Time:** 10:13am 4/22/2026 

Windows Task Scheduler can be configured to run scripts or executables automatically at defined intervals. This VM had a PowerShell script C:\DevTools\CleanUp.ps1 configured to run every minute as SYSTEM. Viewing the script with type revealed it deleted log files from C:\DevTools\. Using accesschk.exe, it was confirmed that the user account had full write access to the script file (RW permissions including FILE_WRITE_DATA and FILE_APPEND_DATA). Since the script runs as SYSTEM and is writable by a low-privileged user, the attacker appended a line to the script using the echo command with the >> append operator, adding C:\PrivEsc\reverse.exe as the last line. A netcat listener was started on the attacker machine. Within 60 seconds, the scheduled task fired automatically, executed the appended line as SYSTEM, and the listener captured a SYSTEM-level reverse shell (C:\Windows\system32>). 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE43.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE44.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE45.png)

## INSECURE GUI APPS  
Some GUI applications running with elevated privileges can be abused to gain a higher-privileged shell. An RDP session was established as the user account. Using tasklist /V, it was found that mspaint.exe was running as the admin user. Within Paint, the File → Open dialog was used to navigate to C:\Windows\System32\cmd.exe and open it, spawning a command prompt running with admin privileges (C:\Users\admin\Pictures>). 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE46.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE47.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE48.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE49.png)

## STARTUP APPS 
Programs placed in the Startup directory are executed when any user logs in. Using accesschk.exe, it was confirmed that BUILTIN\Users had write access to C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp. The CreateShortcut.vbs script was run using cscript to create a shortcut to reverse.exe in the Startup directory. A netcat listener was started and rdesktop was used to simulate an admin login. When the admin logged in, the startup shortcut executed reverse.exe and the listener captured a shell running with admin privileges. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE50.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE51.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE52.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE53.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE54.png)

## TOKEN IMPERSONATION – ROGUE POTATO 
Service accounts often hold the SeImpersonatePrivilege or SeAssignPrimaryTokenPrivilege privileges, which can be abused to impersonate higher-privileged tokens. A socat redirector was set up on the attacker machine forwarding port 135 to port 9999 on the target. PSExec64 was used from an elevated command prompt to spawn a shell running as NT AUTHORITY\LOCAL SERVICE. From within that shell, RoguePotato.exe was executed, which exploited the DCOM/RPC endpoint to capture a SYSTEM token and launch reverse.exe with SYSTEM privileges. The second listener captured the SYSTEM shell. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE55.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE56.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE57.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE58.png)

## TOKEN IMPERSONATION – PRINTSPOOFER 
PrintSpoofer is a more reliable token impersonation tool that abuses the Print Spooler service to capture a SYSTEM token. Similar to RoguePotato, a LOCAL SERVICE shell was first obtained using PSExec64 from an elevated command prompt. From within that shell, PrintSpoofer.exe was executed with the -c flag pointing to reverse.exe and -i for interactive mode. PrintSpoofer successfully found the SeImpersonatePrivilege, impersonated the SYSTEM token, and launched reverse.exe, delivering a SYSTEM shell to the second netcat listener.

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE59.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE60.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/WPE61.png)
 
| Technique | MITRE ID | Tactic |
|-----------|----------|--------|
| Reverse Shell via msfvenom | T1059.003 – Command and Scripting Interpreter: Windows Command Shell | Execution |
| File Transfer via SMB/HTTP | T1105 – Ingress Tool Transfer | Command and Control |
| Insecure Service Permissions | T1574.010 – Hijack Execution Flow: Services File Permissions Weakness | Privilege Escalation, Persistence |
| Unquoted Service Path | T1574.009 – Hijack Execution Flow: Path Interception by Unquoted Path | Privilege Escalation, Persistence |
| Weak Registry Permissions | T1574.011 – Hijack Execution Flow: Services Registry Permissions Weakness | Privilege Escalation, Persistence |
| Insecure Service Executables | T1574.010 – Hijack Execution Flow: Services File Permissions Weakness | Privilege Escalation, Persistence |
| Registry AutoRuns | T1547.001 – Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder | Persistence, Privilege Escalation |
| AlwaysInstallElevated | T1218.007 – System Binary Proxy Execution: Msiexec | Defense Evasion, Privilege Escalation |
| Passwords in Registry | T1552.002 – Unsecured Credentials: Credentials in Registry | Credential Access |
| Saved Credentials (cmdkey) | T1078 – Valid Accounts | Defense Evasion, Privilege Escalation |
| SAM Credential Dumping | T1003.002 – OS Credential Dumping: Security Account Manager | Credential Access |
| Pass the Hash | T1550.002 – Use Alternate Authentication Material: Pass the Hash | Lateral Movement, Defense Evasion |
| Insecure GUI Apps | T1548.002 – Abuse Elevation Control Mechanism: Bypass User Account Control | Privilege Escalation, Defense Evasion |
| Startup Apps | T1547.001 – Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder | Persistence, Privilege Escalation |
| Scheduled Tasks | T1053.005 – Scheduled Task/Job: Scheduled Task | Execution, Persistence, Privilege Escalation |
| Token Impersonation (RoguePotato) | T1134.001 – Access Token Manipulation: Token Impersonation/Theft | Privilege Escalation, Defense Evasion |
| Token Impersonation (PrintSpoofer) | T1134.001 – Access Token Manipulation: Token Impersonation/Theft | Privilege Escalation, Defense Evasion |
 

 

 
