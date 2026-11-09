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

## License

MIT. See [LICENSE](LICENSE). Header check logic: Excession Labs, https://penetrationtestservices.com/tools/security-headers-checker/.
