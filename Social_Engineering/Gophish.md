# 📌 **Phishing With GoPhish — Complete Notes**

---

# 🧠 **What is GoPhish?**

GoPhish is a powerful, open-source phishing simulation toolkit built for red teamers, blue teamers, cybersecurity trainers, and pentesters. It allows organizations to test how employees respond to phishing attacks in a safe, controlled environment.

### 🚀 Key Advantages

* Works perfectly in offline labs and VPS environments
* Highly customizable (emails, landing pages, users)
* Supports automation and reporting
* Ideal for *OSINT-free internal phishing simulations*
* Lightweight—can run on Windows, Linux, or even a Raspberry Pi

---

# 🧩 **GoPhish Architecture (Visual Breakdown)**

              +-----------------------+
              |   GoPhish Admin UI    |
              | 127.0.0.1:3333 (HTTP) |
              +-----------+-----------+
                          |
                          | Manages Campaigns, Users, SMTP
                          |
              +-----------v------------+
              |    GoPhish Core        |
              |   (gophish.exe)        |
              +-----------+------------+
                          |
                          | Serves Phishing Pages
                          |
              +-----------v------------+
              |  Phishing Server       |
              |   Port 80/8080         |
              +------------------------+

---

# 🛠️ **GoPhish Workflow (Simple Steps)**

1. **Prepare environment**

   * Edit templates
   * Remove external links
   * Configure `config.json`

2. **Start GoPhish**

3. **Log in to UI**

4. **Create Sending Profile**

5. **Create Landing Page**

6. **Create Email Template**

7. **Add Users / Groups**

8. **Launch campaign**

9. **Monitor results**

---

# 🎯 **AND NOW — YOUR ORIGINAL CONTENT (UNEDITED)**

---

"# Phishing With Gophish

## Gophish

GoPhish is an open-source phishing framework designed for penetration testers and security professionals to simulate phishing attacks against their own organizations.
It provides a user-friendly platform to create, execute, and analyze phishing campaigns, allowing users to assess their organization's susceptibility to phishing attacks and improve their security posture.
GoPhish is a powerful tool for penetration testers and security professionals to conduct phishing assessments, educate employees about phishing risks, and strengthen the organization's defenses against social engineering attacks.

gophish ideally is hosted on VPS

## Gophish Features

Campaign Creation: GoPhish allows users to create customized phishing campaigns tailored to their specific objectives and targets. Users can create multiple campaigns with different templates, email content, and target lists.
Email Template Editor: The platform provides a built-in email template editor with a WYSIWYG (What You See Is What You Get) interface, making it easy to design professional-looking phishing emails that mimic legitimate communications.
Target Management: Users can manage their target lists and segment
them based on various criteria, such as department, role, or location. This allows for targeted phishing campaigns that closely mirror real-world attack scenarios.
Landing Page Creation: GoPhish enables users to create phishing
landing pages that mimic legitimate login portals or websites. These landing pages can be customized to capture credentials, personal information, or other sensitive data from targets
Tracking and Reporting: The platform provides comprehensive tracking and reporting capabilities, allowing users to monitor the progress of their phishing campaigns in real-time. Users can track email opens, link clicks, and submitted data, and generate detailed reports for analysis.

* Scheduling and Automation: GoPhish supports campaign scheduling and automation, allowing users to schedule campaign launches at specific dates and times or set up recurring campaigns for ongoing testing and assessment.

Gophish Website: [https://getgophish.com/](https://getgophish.com/)
Gophish GitHub Repo: [https://github.com/gophish/gophish](https://github.com/gophish/gophish)
Gophish Installation Guide: [https://docs.getgophish.com/user-guide/installation](https://docs.getgophish.com/user-guide/installation)

demo
target the intern as they are weakest chain
open mozilla thunderbird
this lab is not connected to internet
cd templates
open base.html in notepad
remove the link that will be loaded online so as we don;t have netwrok in this lab it will be slowed or won;t work
only leave the locally loading files
i.e. remove extarnal references here in this mail

remove from base , login.html , nav.html and dashboad.html

now go to parent directory...
open config.json in notepad
set admi_server url to the url of gophish server
set the tls to false .i.e. https to http

in phish_server
here we gonna host malicious login pages configued to run on 80 port

then there is sqlite3 database that will be storing the phishing data

now click on gophish.exe

now open the firefox at 127.0.0.1:3333
accept the risk

now enter the credentials

on page
there is everything that we learned in last lecture
in user management if we working in team we can create accounts for them
we can also create acc for the client

steps to setup phishing campaign

1 configure sending profiles
process of senfing email
here we provide smtp server detail and email
here as it is on local host
so it is goin to be local host and port is standard smtp 25
name: <red team bla bla>
interface type smtp
from  info [rnicrosoft.support@demo.ine.local](mailto:rnicrosoft.support@demo.ine.local)
host: localhost:25
(check if 25 is listning or not in terminal netstat -ano)
Username:red@demo.ine.local
Passowrd:penetrationtesing
(provided in lab docs)
ignore certs error
lets test first
send test email
vic	tim	[victim@demo.ine.local](mailto:victim@demo.ine.local)	intern
send

save profile
go to landing pages

name : INE PASWORD RESET
ine password change page is locally hosted on port 8080
[http://localhost:8080](http://localhost:8080)
so importsite: [http://localhost:8080](http://localhost:8080)
here we ask them to check the password
tick on cpaturere submtted data
tick on capture passwrods
redirect to
[https://localhost:8080](https://localhost:8080)
or anything that won't look suspicious
go to email templates section
name : INE Passwrods reset
import email
paste the raw email content
in this lab content is on desktop
passwords reset email.txt
we can also add the malicious document by clicking on add files

now go to new groups
Nmae : ine Emplooyess
bulk import users
import csv of mail and emploees

now go to campaigns
new campaigns
Name: INE passwrods reset
email template : INE Passwrod Reset
Landing Page: InePasswrods rest
URL: [http://localhost](http://localhost)
Launch date
set somwhere near that time
set profile
then groups
INE EMployees

on interface we can refresh and all
we ll see how many emailes sent and how many open and ckicked link and submitted data and email reported"

---

# ⚡ Additional Red-Team Tips (Added Section)

### ✔ Email Template Best Practices

* Use *internal tone* to seem realistic
* Add minor typos intentionally (if internal mail usually has them)
* Use believable subjects like:

  * “Password Expiry Alert”
  * “New HR Policy”
  * “Shift Roster Update”

### ✔ Landing Page Tips

* Clone only what is necessary
* Use local assets (no external links)
* Use HTTPS look-alike (self-signed OK for labs)

### ✔ OPSEC Tips

* Use VPS / NAT to avoid revealing your real machine
* Do not reuse phishing domains across campaigns
* Always sanitize captured credentials after assessment

---

