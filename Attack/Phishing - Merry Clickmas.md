# Phishing - Merry Clickmas

**Platform:** Try Hack Me 

**Start Time:** 11:09 PM 

**Start Date:** 3/30/2026 

**Tools Used:** Social Engineer Toolkit Set 

**GOAL:** Exploit McRed and Pivot McRed. See if more cyber security awareness training is required 

**Attacker Machine IP (Mine):** 10.67.113.244 

**Target Machine IP:** 10.67.156.51 

**Social Engineering –** manipulating a user to make a mistake. Using psychological factors like urgency, curiosity, and authority can play a key role. 
 
TBFC cyber security awareness training teaches users about two anti-phishing mnemonics written as S.T.O.P. The first S.T.O.P. is from All Things Secured,(opens in new tab) which tells users to ask the following questions before acting on an email: 

- Suspicious? 
- Telling me to click something? 
- Offering me an amazing deal? 
- Pushing me to do something now? 
- The second S.T.O.P. reminds users to follow the following instructions: 
- Slow down. Scammers run on your adrenaline. 
- Type the address yourself. Don’t use the message’s link. 
- Open nothing unexpected. Verify first. 
- Prove the sender. Check the real From address/number, not just the display name. 

**Goal of phishing attack:** to acquire the target user's login credentials by using a fake TBFC portal login page which is attached to the phushing email sent to the target. 

A script has been set up that when ran will host a fake login page which will capture all the credentials that has been entered into the page.  

## Attack Steps 
I first headed to where my script was located on my machine and ran [./server.py] so that it can start listening for any credentials. If the target gets trapped and enters the credentials it will be shown in the terminal.  

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi1.png)

From the screenshpt you can see that the phsihing web application is listening on port 8000; and the 0.0.0.0 implies that it is bound to all interfaces. 

To test I entered the link [ http://10.67.113.244:8000 or http://127.0.0.1:8000 ] to see what the user will see. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi1.png)

The URL shows up as a fake staff login portal
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi2.png)

I can also see the activity on my terminal 

Now that I know my fake website is working and I can view the activity on it. I start on creating the fake phishing email. The fake phishing email will be created using Social Engineer Toolkit (SET) which is an open source tool used for social engineering attacks from my terminal. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi3.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi4.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi5.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi6.png)

After my phishing email has been sent and setup I check back at my server to see if the target has clicked the link. And when they did I was able to get their user name and password. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi7.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi8.png)

Using already known credentials from my phishing attack I can log in to the page as the user and view sensitive information. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi9.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/phi10.png)

From the above screenshot you can also see the phishing email I sent to the user. 

**Attack End time:** 11:53pm 
<br>**Attack End Date:** 3/30/2026 


