# Threat model

## What this project does and where untrusted input enters
PHP web application ("Discount Bandit") that tracks product prices across multiple stores and
notifies the user when prices drop. Untrusted input: store/product URLs entered through the
web UI (fetched and parsed server-side), HTML/JSON returned by third-party stores (parsed to
extract price and stock), user credentials and notification settings (webhook/SMTP targets,
thresholds), and .env configuration.

## Components that matter most / least
- most: the store scraping/parsing code (server-side fetch of attacker-influenced URLs plus
  parsing of hostile store responses), the notification dispatch (where webhook/SMTP targets
  come from), and the web auth/session handling.
- less: UI assets, docs.

## How to exercise it
Add trackers pointing at hostile URLs (internal addresses, huge or malformed responses,
crafted HTML/JSON); log in and inspect session handling; register notification targets that
exfiltrate data; test the price-drop notification path with crafted values.

## How you rate severity
- RCE (e.g. via template/eval on parsed content or command assembly): critical. Auth/session
  bypass: critical. SSRF through the store fetcher: high. Credential or webhook-secret
  exposure: medium. Stored XSS in tracker fields rendered in the UI: medium-high.

## Anything to leave alone
- Third-party store websites and their responses are the input, not the code under review.
