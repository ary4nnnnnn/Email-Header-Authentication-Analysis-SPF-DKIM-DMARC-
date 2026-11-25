## My Observations While Doing This

One thing that genuinely surprised me — I used to think SPF passing 
meant an email was safe. But when I checked my own spoofed test email, 
SPF came back as "Authenticated" even though DKIM and DMARC both failed. 
That's when it clicked for me that real email security comes from 
looking at all three checks together, not trusting just one of them.

The second thing I noticed — Gmail automatically routed the spoofed 
email straight to Spam and even bounced it back, without me having to 
do anything. This showed me that email providers already have fairly 
smart detection systems running in the background, but it's still 
important to manually check the headers yourself before confirming 
anything as safe or malicious.

## Issues Faced
- None major — the Emkei Fake Mailer worked smoothly on the first try.
- Had to scroll through the MXToolbox report carefully since the SPF/DKIM/
  DMARC section loads further down the page and isn't visible immediately.

## Key Takeaway
A single "PASS" (like SPF) can