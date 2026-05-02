# 09 - Ransomware Password Cracking

**Attack Date:** April 8, 2026  
**Attack Duration:** 17 minutes (2:40 PM to 2:57 PM)  
**Severity:** Critical  
**MITRE ATT&CK:** T1110.002, T1486, T1491  

---

## What Happened?

Ransomware is a type of attack where an attacker locks your files and demands payment to unlock them. In most ransomware attacks, files are encrypted using complex encryption. In this case the attacker used a simpler method — they just password-protected the files.

The ransomware locked two important files on the target system: `flag.pdf` and `flag.zip`. A ransom note was left demanding 0.5 Bitcoin (roughly thousands of dollars) to be paid within 48 hours.

Instead of paying, the security team decided to try and crack the passwords themselves. Here is what they used:

- **pdfcrack** — a tool that tries every password in a wordlist against a password-protected PDF file
- **zip2john** — a tool that extracts the password hash from a ZIP file
- **John the Ripper** — a password cracking tool that then tries to crack that hash using the rockyou.txt wordlist

The rockyou.txt wordlist contains millions of real passwords that were leaked in data breaches over the years. If a password appears in that list, it can be cracked in minutes.

The results:
- PDF password cracked: `naughtylist`
- ZIP password cracked: `winter4ever` (after 2,740,224 attempts)

The total time from attack to both passwords cracked was 17 minutes.

---

## What Signs Did This Attack Leave Behind?

- Ransomware deployment event on the target system — a clear high-priority alert
- A ransom note file being created in the user's directory
- Password cracking tools like `pdfcrack`, `john`, and `zip2john` running on the system
- Multiple accesses to the rockyou.txt wordlist file in quick succession
- High CPU usage combined with wordlist file access — the signature of active password cracking
- Successful password recovery events logged in the cracking session files

---

## Detection Rules

**Detect ransomware deployment:**
```
index=ransomware_logs
| search alert="RANSOMWARE_DETECTED" OR event_type="ransomware_deployment"
| table _time, host, event_type, message, alert
```

**Detect ransom note creation:**
```
index=ransomware_logs
| search event_type="ransom_note_created" OR alert="RANSOM_NOTE_CREATED"
| table _time, host, filename, message, alert
```

**Detect password cracking tools running:**
```
index=ransomware_logs
| search process_name="pdfcrack" OR process_name="john" OR process_name="zip2john"
| table _time, host, user, process_name, command, alert
```

**Detect access to rockyou.txt wordlist:**
```
index=ransomware_logs
| search wordlist="*rockyou*" OR command="*rockyou*"
| table _time, host, process_name, wordlist, command, alert
```

**Full attack timeline:**
```
index=ransomware_logs
| search alert="*DETECTED*" OR alert="*CRACKED*" OR alert="*FOUND*" OR alert="*CREATED*"
| table _time, host, process_name, event_type, alert, message
| sort _time
```

---

## What To Do If This Happens To You

1. **Do not pay the ransom** — there is no guarantee you will get your files back, and paying funds further attacks
2. **Isolate the infected machine** from the rest of your network immediately to stop the ransomware spreading
3. **Check if you have backups** — if you have recent offline backups, you can restore your files without paying
4. **Report the incident** to the FBI's Internet Crime Complaint Center (IC3) at ic3.gov — ransomware is a crime
5. **Contact a cybersecurity firm** if you need professional help recovering files
6. **Do not try to remove the ransomware yourself** without guidance — some variants will destroy files if tampered with

---

## How To Prevent This

- **Maintain regular offline backups.** This is the single most effective defence against ransomware. If you have a backup from yesterday, you can restore everything without paying anything. Backups should be kept somewhere the ransomware cannot reach — an external drive that is disconnected, or a cloud backup service.
- **Use strong passwords that are not in any wordlist.** Both passwords cracked here (`naughtylist` and `winter4ever`) appeared in the rockyou.txt wordlist. A strong password uses a random mix of letters, numbers, and symbols.
- **Deploy endpoint protection** that can detect ransomware behaviour before it finishes running
- **Use application whitelisting** to prevent unauthorised tools from running — password cracking tools should never be running on a business computer

---

## Key Takeaway

The most important takeaway from this attack is not about how to crack passwords — it is about backups. If you have a recent offline backup, ransomware is an inconvenience, not a catastrophe. If you do not have backups, you are left with three bad options: pay the ransom, lose your data, or hope someone can crack the encryption. Set up backups today.
