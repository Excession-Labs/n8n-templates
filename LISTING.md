# Listing text for n8n.io

Title (51 characters):
Weekly security headers check for your websites

Short description (plain text):
Check the HTTP security headers of your websites once a week. The workflow sends one request per URL to the free penetrationtestservices.com checker, waits 6 seconds between calls, and emails you when any site has a "fail" result.

Long description:
This workflow reads a list of website URLs, checks each one with the free security headers checker at penetrationtestservices.com, and sends you an email when a site fails at least one check.

Who it helps: anyone who runs public websites and wants a weekly look at Strict-Transport-Security, Content-Security-Policy, clickjacking protection, X-Content-Type-Options and related headers.

How it works: a Schedule Trigger runs every Monday at 08:00. The URLs are set in one node. A loop sends one request per URL and waits 6 seconds after each, which stays under the checker's limit of 12 requests per minute per IP address. A Code node builds a summary. If any site has a "fail" result, an Email Send node sends the summary. If not, the run ends in a NoOp node.

Setup: replace the example URLs, select an SMTP credential in the email node, and set the From and To addresses. You can swap the email node for a Slack node.

Costs: no API key and no external cost.

Limits: the checker reads response headers only. It checks public http and https addresses on ports 80 and 443. It is a header check, not a penetration test.

Categories (pick the closest in the n8n form): IT Ops, Engineering
Tags / nodes used: Schedule Trigger, Manual Trigger, Edit Fields (Set), Split Out, Loop Over Items, HTTP Request, Wait, Code, If, Send Email, NoOp
Creator bio (from FACTS.md): Excession Labs is a security lab. It runs authorized security tests of web applications, APIs, multi-tenant SaaS platforms and AI applications, and it publishes lab research.

---

# Listing text: Pentest provider shortlist from PentestCompare data

Title (53 characters):
Pentest provider shortlist from PentestCompare data

Short description (plain text):
Enter a delivery model, a scope and a prices filter. The workflow reads the open PentestCompare provider dataset and returns a table of matching pentest providers with sourced facts and check dates.

Long description:
This workflow helps a buyer make a first list of penetration test providers. You enter three needs: the delivery model (any, human-led, hybrid or automated), the scope (web_app, api, ai_apps, cloud, network or mobile) and whether to keep only providers with published prices. The workflow downloads the open provider dataset from pentestcompare.com (MIT, no key) and filters it in a Code node. The result is a Markdown table. Each row has the provider's delivery model, scope status, pricing model, first published price, turnaround claim, retesting claim, sample report status, source link and the date the provider was last checked. Rows are sorted by name, not ranked. The publisher of the dataset has its own row, marked publisher_owned. Credit PentestCompare when you share the result. Setup: no credentials needed. Edit the three values in the node "Buyer needs", or use the form trigger.

Categories (pick the closest in the n8n form): Security, IT Ops
Tags / nodes used: Manual Trigger, Form Trigger, Edit Fields (Set), NoOp, HTTP Request, Code

---

# Listing text: Track CISA KEV additions ranked by EPSS

Title (42 characters):
Track CISA KEV additions ranked by EPSS

Short description (plain text):
Every week, find the new entries in the CISA Known Exploited Vulnerabilities catalog, add their FIRST EPSS scores, and send a ranked digest with NVD links.

Long description:
This workflow sends a weekly digest of new entries in the CISA Known Exploited Vulnerabilities (KEV) catalog. It downloads the KEV JSON feed, compares it with the catalog version and CVE IDs that n8n saved on the last run, and gets the EPSS score of each new CVE from the free FIRST API. The digest lists the entries from the highest EPSS score to the lowest, with vendor, product, date added, CISA due date, known ransomware use and a link to the NVD. If nothing is new, the run ends in a NoOp node. Setup: select an SMTP credential in the email node and set the From and To addresses. You can use a Slack node instead. No API key and no external cost. Limit: n8n saves the state only for runs started by the Schedule Trigger in an active workflow. EPSS estimates the chance of exploitation. It does not measure impact in your systems.

Categories (pick the closest in the n8n form): Security, IT Ops
Tags / nodes used: Schedule Trigger, Manual Trigger, HTTP Request, Code, If, Split Out, Send Email, NoOp
