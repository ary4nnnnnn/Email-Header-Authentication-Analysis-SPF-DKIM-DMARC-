# Email Header & Authentication Analysis (SPF, DKIM, DMARC)

## 📌 Why I Did This
As part of my SOC L1 practice projects, I wanted to actually understand 
how email authentication works instead of just reading about SPF, DKIM, 
and DMARC in theory. So I picked a real email I received, analyzed its 
headers properly, and then — to really see the difference — I used a 
fake mailer tool to send myself a spoofed email and compared the two.

This project is basically my hands-on attempt at learning what a SOC 
analyst actually looks at when a "phishing email" ticket lands on their desk.

## 🧪 What I Actually Did

### Step 1: Analyzed a legitimate email
I had a genuine marketing email from Splunk (events@splunk.com) sitting 
in my inbox — a webinar invite. I pulled up the raw header using Gmail's 
"Show Original" option and ran it through MXToolbox's Email Header 
Analyzer.

Result: SPF, DKIM, and DMARC all came back as **PASS**. The email was 
sent through Marketo (a legitimate email marketing platform companies 
use), and everything lined up — no mismatch between the display name 
and the actual sending domain.

### Step 2: Created a controlled spoofing test
To actually see what a "failing" email looks like (instead of just 
imagining it), I used **Emkei's Fake Mailer** to send an email to my own 
inbox — not to anyone else, just myself, purely to study the behavior.

What happened next was interesting:
- Gmail automatically routed it straight to **Spam**
- I also got an **"Undelivered Mail Returned to Sender"** bounce notice
- When I ran this one through MXToolbox too, the results were the 
  complete opposite of the Splunk email:
  - DMARC Compliant: ❌ FAIL
  - SPF Alignment: ❌ FAIL
  - DKIM Alignment: ❌ FAIL
  - DKIM Authenticated: ❌ FAIL

I also checked the sending domain (`emkei.cz`) on VirusTotal, and 5 out 
of 93 security vendors had already flagged it as Phishing/Malicious.

### Step 3: Compared both and documented the difference

| Check | Legitimate Email (Splunk) | Spoofed Test Email |
|---|---|---|
| SPF | PASS | FAIL (alignment) |
| DKIM | PASS | FAIL |
| DMARC | PASS | FAIL |
| Delivery | Inbox | Spam + Bounced |
| VirusTotal | Clean | Flagged by 5 vendors |

## 🔍 What I Learned From This

Honestly, the biggest thing I understood is that **SPF passing alone 
means nothing**. In my test, SPF "Authenticated" technically showed a 
pass because the email was sent from emkei.cz's own server and its SPF 
record allowed that — but the moment you check DKIM and DMARC alignment, 
it completely falls apart. That combination (SPF pass, DKIM/DMARC fail) 
is actually a classic sign of a spoofed sender, not a legitimate one.

I also learned that Gmail's spam filter isn't just guessing — it's 
actually reacting to these exact authentication failures. The second my 
test email failed DKIM/DMARC, Gmail routed it to spam before I even 
opened it, and the server bounced it back.

## 🛠️ Tools I Used
- Gmail's "Show Original" (to get raw headers)
- MXToolbox Email Header Analyzer
- VirusTotal (URL/domain reputation check)
- Emkei's Fake Mailer (for the controlled spoofing test — only used 
  against my own inbox, no third party involved)

## 🎯 MITRE ATT&CK Mapping
- T1566 — Phishing

## 📷 Screenshots
Check the `/screenshots` folder — includes the full header breakdown 
for both the legitimate and the spoofed email, plus the VirusTotal scan.

## ⚠️ A Note on Ethics
The spoofing test here was done entirely against my own email address, 
using a known testing tool, purely to understand how authentication 
failures look in practice. I didn't target or send anything to anyone 
else — this was a self-contained learning exercise.

## 🔜 What's Next
I want to extend this by analyzing a real phishing sample from my spam 
folder (if I find one) and building a quick checklist/playbook for 
triaging suspicious emails based on what I learned here.