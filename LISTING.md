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
