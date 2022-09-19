# Incident-Response-Writeup
LetsDefend SOC165 - Possible SQL Injection Payload Detected EventID: 115 Writeup

<br> LetsDefend is an awesome platform for blue team learning, training, and practice!
<br> Lets get started...

<h2>Alert Triggered!</h2>

- Here is the case management page in this SOC where, we as Analysts, see alerts get triggered and respond. In this case we had an incident occur involving a Possible SQL Injection Payload Detected

![LetsDefendAlert](https://user-images.githubusercontent.com/98111674/190935338-d1c7bb2f-a693-42fd-94c0-8194f447eac8.png)

- Once we take ownership of the alert we can start our playbook and create a case. Before creating a case I like to take note of the data surrounding the alert. I will usually take note of: Hostnames, Destination IP Address, Source IP Address, Requested URL, File names and hashes, and etc. Once I have built a decent mapping of the landscape then I like to start the playbook.

- With this being a web attack I take note of the IP addresses, the HTTP request method and URL, and get to work.

![LetsDefendAlertvirustotal](https://user-images.githubusercontent.com/98111674/190935792-506c667a-7624-47ac-89cc-42acd699b986.png)
- Usually the first thing I will scan is the IP address to VirusTotal to get a better picture of who the actor is. This can tell us things such as GeoLocation to help understand who the threat actor may be. In this case I will look up the source IP since I have already checked my endpoints and found out the destination IP is a endpoint in our network, WebServer1001. VirusTotal returns 11/94 security vendors have flagged this IP address as malicious. 
- Welp.. that is it. It's malicious and we are done? 
- No. Our job here is to triage and gather more information to understand the bigger picture. I will usually double check and triple check to be sure. Other sources we can use include Cisco Talos Reputation Lookup and AbuseIPDB to verify. 
![LetsDefendciscotalos](https://user-images.githubusercontent.com/98111674/190936065-418693af-6a4e-489b-9e1c-cc43162889ae.png)
![LetsDefendabuseipdb](https://user-images.githubusercontent.com/98111674/190936069-447768fe-d0c6-4232-8c99-2394ee441f66.png)
- So our source IP address is probably malicious and trying to execute a malicious payload against our endpoint, WebServer1001. 
- Let's dive further into the log management page to query for our malicious actor with the malicious IP address. 

![LetsDefendAlert3LogManagment](https://user-images.githubusercontent.com/98111674/190936277-e6e0bb2e-bcba-415c-a985-b24d7810e81f.png)
- Here in the log management section of our SIEM we can triage further and dive more into detail. I have located the Raw Log that gives us more information about the attacker. We can take note of this.
- Next we can examine the HTTP Request. Right away I can tell url encoding is involved with the HTTP request, so we can use our favourite url decoder. I will use cyberchef to decode the HTTP request URL.

![letsdefenedurldecode](https://user-images.githubusercontent.com/98111674/190936579-975afbe1-a922-4eb9-a69f-c3d6a7dc135b.png)
- Bingo! That looks like an SQL injection attack to me. We can head back to the log management page and take note that the device action was permitted, HTTP response size 948, and the HTTP Response status 500. The response status indicates to me an internal server error meaning the malicious payload was most likely unsuccessful. We can dive further into this by looking at other logs with a similiar response status and comparing the response size. I will also take a look at the endpoint and check for the command history to see if anything else malicious took place. 

- Now we have built a solid understanding of what took place and we can dive into the playbook. 

<h2> Let's resolve this case </h2>

- I love the playbook for LetsDefend because it helps build a understand of the landscape and what occured. Essentially it is a step by step for a security analyst to go through.

![LetsDefendAlert4Mal](https://user-images.githubusercontent.com/98111674/190937040-d057022a-53f5-46c1-9b91-f7fe38bb2a82.png)
- I would say malicious. A noble user wouldn't use URL encoding nor would they try to commit a SQLi against a database.

![LetsDefendAlert5SQL](https://user-images.githubusercontent.com/98111674/190937122-09a0f4f5-0da6-44df-bf95-8638a8034123.png)
- We have confirmed it was a SQL Injection

![LetsDefendAlert6NotPLanned](https://user-images.githubusercontent.com/98111674/190937137-bc2fe9f5-9e1e-4567-a705-fdb004d01e2a.png)
- I searched through the Email Exchange and found no evidence of a planned test, so it was Not Planned

![LetsDefendAlert7InternettoCompany](https://user-images.githubusercontent.com/98111674/190937170-6b4cebdd-15cc-46b3-b27c-ddcc25465964.png)
- The traffic came from the Internet into our Company Network

![LetsDefendAlert8](https://user-images.githubusercontent.com/98111674/190937193-92fce1ff-65be-4144-91e7-84b5fc19a15c.png)
- Glancing at my notes I would say the attack was unsuccessful. The HTTP response status of 500 and not seeing anything suspicious or malicious in the endpoint leads me to believe it was not successful.

![LetsDefendAlert9Artifacts](https://user-images.githubusercontent.com/98111674/190937270-187c524d-10db-4291-84e3-ba9c268375f1.png)
- Here I add some artifacts for the report.

![LetsDefendAlert10AnalystNotes](https://user-images.githubusercontent.com/98111674/190937291-04fc32ea-56ea-4e59-a3e9-87cec137bbd7.png)
- My quick notes about the case. This does not include my research and investigation. I usually like to keep that on a text file in notepad++. This is just a quick summary.

![LetsDefendAlert11CloseAlert](https://user-images.githubusercontent.com/98111674/190937348-e71fd6e6-fba3-4220-a05b-eec1f500f9bd.png)
- Finally we close the alert as a true positive again with a quick summary. 

![LetsDefendAlert12Result](https://user-images.githubusercontent.com/98111674/190937384-b0d9d833-5bb8-4a8a-899c-1b98a5ab369e.png)
- We see our results. Woohoo! All correct! No one is perfect all the time however, and if you missed anything you can always re-investigate the case. This is why this platform is great for learning! 



