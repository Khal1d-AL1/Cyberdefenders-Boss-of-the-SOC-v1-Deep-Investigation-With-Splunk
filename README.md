# Cyberdefenders-Boss-of-the-SOC-v1-challenge

##  Incident Response

## scenario 1 from Splunk:

Today is Alice's first day at the Wayne Enterprise Security Operations Center. Lucius sits Alice down and gives her first assignment: A memo from Gotham City Police Department (GCPD). Apparently GCPD has found evidence online (http://pastebin.com/Gw6dWjS9) that the website www.imreallynotbatman.com hosted on Wayne Enterprise's IP address space has been compromised. The group has multiple objectives... but a key aspect of their modus operandi is to deface websites in order to embarrass their victim. Lucius has asked Alice to determine if www.imreallynotbatman.com. (the personal blog of Wayne Corporations CEO) was really compromised.


## The investigatation will cover the Cyber Attack Kill Chain from BOTsv1 dataset using Splunk.

## Step 1 - Reconnaissance

### 1. What is the likely IPv4 address of someone from the Po1s0n1vy group scanning imreallynotbatman.com for web application vulnerabilities?

<br><br> 
  -  Our organization’s website is imreallynotbatman.com.

     Now, we will identify traffic related to the imreallynotbatman.com website.

     by using following query.
     
       ### index="botsv1" imreallynotbatman.com

   ![splunk2](https://github.com/user-attachments/assets/a062c715-5d6c-413f-92ee-4602bfee5287)  

<br><br> 
   - There are 78,683 events relating to this website. Currently, not all of them are essential to us.
     We can observe relevant streams as the following:

   ![splunk1](https://github.com/user-attachments/assets/828bf146-0b10-4e6d-aafe-696dcd848645)

<br><br> 
- sourcetype of stream:http is definitely interesting to us, now we concentrate on the HTTP traffic.
  using below query:

### index="botsv1" sourcetype="stream:http" imreallynotbatman.com

![splunk3](https://github.com/user-attachments/assets/7dfec733-bf93-490a-a538-c2b9f56177eb)


<br><br> 
- the output is too overwhelming to inspect those traffic manually.
  By adding | fields src_ip, to the  previous query, we can extract unique source ip address.

### index="botsv1" sourcetype="stream:http" imreallynotbatman.com | fields src_ip

![splunk4](https://github.com/user-attachments/assets/91d19c81-12ae-4b6b-b654-6564194d7b9b)

query show that we have 2 suspects.
The addresses - 40.80.148.42 and 23.22.63.114 involved in most parts of the HTTP stream.
Due to the high volume of requests to “imreallynotbatman.com” most likely 40.80.148.42 is suspect.

<br><br> 
- See suricata alerts against this address 40.80.148.42 by following query:
  ### index="botsv1" sourcetype="suricata" src="40.80.148.42" | search event_type=alert | stats count(alert.signature) as "Alert" by alert.signature | sort - "Alert"

  ![splunk5](https://github.com/user-attachments/assets/c79686aa-3b5f-4e95-bb81-bf2bfd037aaa)

  - As we can see, multiple attacks are mounted by the APT group.

    Let’s look at one of the HTTP request being sent by this adversary. The search is as below:
  
  ### index="botsv1" sourcetype="stream:http"  src_ip="40.80.148.42"
  After the query is submitted, we scroll down a little.
  There is an interesting HTTP header indicating the existence of Acunetix vulnerability scanner being used.
  
  ![splunk6](https://github.com/user-attachments/assets/952ff3af-2a47-4959-b800-e4ca1dec6705)


  

  








