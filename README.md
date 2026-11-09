# n8n templates by Excession Labs

Excession Labs is a security lab. It runs authorized security tests of web applications, APIs, multi-tenant SaaS platforms and AI applications, and it publishes lab research (https://excessionlabs.ai).

## Weekly security headers check for your websites

The workflow checks the HTTP security headers of a list of websites every week and emails you when a site has a check with the status "fail". It uses the free headers checker at https://penetrationtestservices.com/tools/security-headers-checker/ through its public endpoint `GET https://penetrationtestservices.com/api/check-headers?url=...`.

File: [weekly-security-headers-check/workflow.json](weekly-security-headers-check/workflow.json)

### What it does

1. A Schedule Trigger starts the run every Monday at 08:00. A Manual Trigger starts a test run.
2. The node "Websites to check" holds the URL list.
3. The workflow sends one request per URL and waits 6 seconds after each. The checker allows 12 requests per minute per IP address.
4. A Code node counts pass, warn and fail results for each site and writes the email text.
5. An IF node sends the email when at least one site has a "fail" result. Otherwise the run ends in a NoOp node.

### Setup

1. Import `workflow.json` into n8n (Workflows, then Import from File).
2. In "Websites to check", replace the two example URLs with your own. Each entry needs `https://` or `http://`.
3. In "Send alert email", select an SMTP credential and set the From and To addresses. To use Slack, delete this node and connect a Slack node to the "true" output of the IF node. Use `{{ $json.text }}` as the message.
4. Click "Execute workflow" to test. Then activate the workflow.

### Requirements and costs

- n8n version 1.x or 2.x. Tested on n8n 2.46.7.
- An SMTP account (or a Slack credential) for the alert. The workflow file holds no secrets.
- No API key. The checker is free. This template has no external cost.

### Limits

- The checker reads response headers only and does not store page bodies. It checks public http and https addresses on the default ports 80 and 443. IP addresses and internal names are refused.
- A site that cannot be checked is listed in the email text as "could not be checked" and does not stop the run.
- Keep the list short. At 6 seconds per URL, 10 URLs take about 1 minute.
- The result is a header check. It is not a penetration test.

### Test record

On 9 November 2026 the workflow ran on n8n 2.46.7 (n8n CLI, `n8n execute`, manual trigger, email node disabled) against https://example.com and https://example.org. Both HTTP Request calls returned the checker JSON with `ok: true` and HTTP status 200. The summary node and the IF node ran, and the run finished with status "success". `weekly-security-headers-check/test-workflow.json` is that test variant.

### n8n submission

Listing text for the n8n template library is in [LISTING.md](LISTING.md).

## Pentest provider shortlist from PentestCompare data

The workflow turns three buyer needs into a table of matching penetration test providers. It reads the open dataset at https://pentestcompare.com/data/providers.json (MIT, no key). Each row carries the provider's sourced facts, a source link and the date the provider was last checked.

File: [pentest-provider-shortlist/workflow.json](pentest-provider-shortlist/workflow.json)

### What it does

1. A Manual Trigger or a Form Trigger supplies the needs: delivery model (any, human-led, hybrid or automated), scope (web_app, api, ai_apps, cloud, network or mobile) and published prices only (yes or no).
2. An HTTP Request node downloads providers.json.
3. A Code node keeps the providers that match. A scope marked "partial" counts as a match and is shown as "partial". With "published prices only" set to yes, a provider needs at least one listed price.
4. A second Code node builds a Markdown table in `table_markdown`, with the count and the most recent check date.

Each row holds: provider, delivery, scope status, pricing model, first published price, turnaround claim, retesting claim, sample report status, last checked date and source link. "Not published" means the provider does not state it. Rows are sorted by name. The data has no ranking and the workflow adds none.

The publisher of the dataset, Excession Labs, has its own row. The workflow marks that row `publisher_owned`. The CSV file carries a `publisher_owned` column. The JSON file did not carry the field when this template was tested (9 November 2026), so the Code node falls back to the top-level `publisher` name. If the field is added to the JSON, the node uses it.

### Setup

1. Import `workflow.json` into n8n.
2. Edit the three values in "Buyer needs" and click "Execute workflow". Or open the production URL of "Needs form", after you activate the workflow, and fill in the form.
3. Read the result in "Shortlist table". Add an email, Slack or Notion node after it to send the table.

### Requirements and costs

- n8n version 1.x or 2.x. Tested on n8n 2.46.7.
- No credentials, no API key and no external cost.
- Credit PentestCompare when you share the result (MIT). Method and sources: https://pentestcompare.com/methodology/

### Limits

- The table shows what providers state on their public pages. It is a starting list. It does not rate quality.
- Prices are the first published price of each provider. Read the source page for the terms.

### Test record

On 9 November 2026 the workflow ran on n8n 2.46.7 in two ways. First, `n8n execute` with the manual trigger and the needs delivery = any, scope = web_app, prices = no returned 58 of 67 providers, all dated 2026-11-09, with the publisher row marked. Second, a form submission to the production URL (hybrid, ai_apps, published prices yes) returned 8 providers. Both runs ended with status "success". `pentest-provider-shortlist/test-workflow.json` is the test copy.

## Track CISA KEV additions ranked by EPSS

The workflow sends a weekly digest of new entries in the CISA Known Exploited Vulnerabilities (KEV) catalog. It ranks them by their FIRST EPSS score and links each CVE to the NVD.

File: [cisa-kev-epss-digest/workflow.json](cisa-kev-epss-digest/workflow.json)

### What it does

1. A Schedule Trigger starts the run every Monday at 08:00. A Manual Trigger starts a test run.
2. An HTTP Request node downloads https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json.
3. "Find new entries" compares the feed with the n8n workflow static data from the last run: the catalog version and the list of CVE IDs already reported.
4. If nothing is new, the workflow saves the new catalog version and ends in a NoOp node.
5. If there are new entries, the CVE IDs go to the FIRST EPSS API in groups of 50 (https://api.first.org/data/v1/epss, free, no key). The workflow waits 1 second between requests.
6. "Rank and digest" sorts the entries by EPSS score, highest first, and writes the digest: CVE ID, EPSS score and percentile, vendor, product, date added, CISA due date, known ransomware use and the NVD link (https://nvd.nist.gov/vuln/detail/CVE-ID).
7. "Send digest email" sends the digest. Replace it with a Slack node if you prefer.

### Setup

1. Import `cisa-kev-epss-digest/workflow.json`.
2. In "Send digest email", select an SMTP credential and set the From and To addresses. The file holds a placeholder credential named "SMTP account (replace)".
3. Click "Execute workflow" to see a first digest, then activate the workflow.

### Requirements and costs

- n8n version 1.x or 2.x. Tested on n8n 2.46.7.
- An SMTP account or a Slack credential. No API key for CISA or FIRST, and no external cost.

### Limits

- n8n saves static data only for runs that the Schedule Trigger starts in an active workflow. A manual run shows the result and does not save the state.
- First run: with no saved state, the digest covers entries added in the last 7 days. Change `FIRST_RUN_DAYS` in "Find new entries" to change this.
- The state is saved in "Rank and digest" after the EPSS requests worked. If a run fails earlier, the next run reports the same entries again. If the email step fails, those entries are not sent again.
- A new CVE can have no EPSS score yet. It is listed last as "not scored yet".
- EPSS is the estimated probability of exploitation in the next 30 days. It does not measure impact or exposure in your systems. Check each CVE against your own assets.

### Test record

On 9 November 2026 the workflow ran on n8n 2.46.7 (email node disabled in the test copy). A manual run with no saved state returned 6 entries from the live feed (catalog version 2026.11.09, 1,772 entries), each with an EPSS score dated 2026-11-09 and an NVD link. A second test used the Schedule Trigger on a running n8n instance. Run 1 saved the catalog version and 1,772 CVE IDs. Run 2 found no change and ended in the NoOp node. After the saved state was edited to remove 3 CVE IDs and set an older catalog version, run 3 reported exactly those 3 entries, ranked by EPSS. All runs ended with status "success". `cisa-kev-epss-digest/test-workflow.json` is the test copy.

## License

MIT. See [LICENSE](LICENSE). Header check logic: Excession Labs, https://penetrationtestservices.com/tools/security-headers-checker/.
