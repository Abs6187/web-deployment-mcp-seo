# Technical Reference & Troubleshooting Manual

An extensive technical reference covering diagnostic procedures, root-cause analyses, error code catalog, and documentation links to visit.

---

## 🔗 Official Documentation & API Portal Links to Visit

### Google Cloud & Analytics APIs
- 🔗 **Google Analytics Data API Enablement Portal:**
  `https://console.developers.google.com/apis/api/analyticsdata.googleapis.com/overview?project=<YOUR_PROJECT_ID>`
- 🔗 **Google Analytics Admin API Enablement Portal:**
  `https://console.developers.google.com/apis/api/analyticsadmin.googleapis.com/overview?project=<YOUR_PROJECT_ID>`
- 🔗 **Google Cloud IAM & Credentials Console:**
  `https://console.cloud.google.com/apis/credentials`
- 🔗 **Google Search Console Overview:**
  `https://search.google.com/search-console`

### Analytics & Search Engine Portals
- 🔗 **Bing Webmaster Tools API Access Key Page:**
  `https://www.bing.com/webmasters/settings/api`
- 🔗 **Google Analytics Admin Dashboard:**
  `https://analytics.google.com/`

### Hosting & Web Standards
- 🔗 **Vercel CLI Command Reference:**
  `https://vercel.com/docs/cli`
- 🔗 **Vercel Custom Domain Verification Guide:**
  `https://vercel.com/docs/projects/domains`
- 🔗 **Schema.org ContactAction Specification:**
  `https://schema.org/ContactAction`
- 🔗 **FormSubmit Endpoint Documentation:**
  `https://formsubmit.co/`

---

## 🛠️ Deep Diagnostic Procedures

### 1. Vercel CLI Upload & Network Diagnostics

#### Symptom: `ECONNRESET` / Deployment Upload Timeout
```text
Error: request to https://api.vercel.com/v13/deployments?teamId=... failed, reason: read ECONNRESET
```
* **Root Cause Analysis:** Vercel CLI packages all files in the current working directory by default. When `.git` directories, `node_modules`, build caches, or binary test data are present, the payload size easily exceeds 10MB+. On slower connections, the HTTP POST stream breaks, resulting in a TCP `ECONNRESET`.
* **Diagnostic Command:**
  ```bash
  # Measure actual directory payload size
  Get-ChildItem -Recurse | Measure-Object -Property Length -Sum
  ```
* **Resolution Workflow:**
  1. Create a `.vercelignore` file in the project root:
     ```gitignore
     .git
     .gitignore
     README.md
     SETUP.md
     LICENSE
     *.cmd
     client_secret*.json
     service-account*.json
     ```
  2. Verify payload size drops from ~10MB to < 1KB.
  3. Re-run deployment with `--scope`:
     ```bash
     npx vercel --prod --yes --scope <your-team-scope> --token=<VERCEL_TOKEN>
     ```

---

### 2. DNS Verification & Redirect Loop Diagnostics

#### Symptom: `ERR_TOO_MANY_REDIRECTS` (Infinite 301 Loop)
* **Root Cause Analysis:** Occurs when a `CNAME` for `www` points directly to `example.com` while the web server or DNS host redirects `example.com` back to `www.example.com`.
* **Diagnostic Command:**
  ```bash
  # Windows PowerShell DNS Verification
  Resolve-DnsName -Name example.com -Type A
  Resolve-DnsName -Name www.example.com -Type CNAME
  ```
* **Standard Vercel DNS Resolution Matrix:**
  | Record Type | Host | Target / Value | Purpose |
  |---|---|---|---|
  | **A Record** | `@` | `216.198.79.1` | Vercel Anycast IP (Recommended) |
  | **CNAME Record** | `www` | `cname.vercel-dns.com.` | Subdomain Target |
  | **TXT Record** | `@` | `v=spf1 include:spf.efwd.registrar-servers.com ~all` | Mail Forwarding Protection |

---

### 3. WebMCP & Form Security Diagnostics

#### Symptom: Chrome "Form is not secure. Autofill disabled" Warning
* **Root Cause Analysis:** Chrome flags HTML forms using `action="mailto:..."` as non-secure handlers on HTTPS pages and disables the native browser Autofill feature.
* **Resolution Workflow:**
  1. Replace `action="mailto:..."` with an HTTPS POST target (e.g. FormSubmit):
     ```html
     <form id="contact-form" action="https://formsubmit.co/user@example.com" method="POST" autocomplete="on" itemscope itemtype="https://schema.org/ContactAction">
     ```
  2. Add `autocomplete="on"` to `<form>` and `autocomplete="name"`, `autocomplete="email"` to inputs.
  3. Annotate input fields with `itemprop="name"`, `itemprop="email"`, `itemprop="description"` for WebMCP AI agent discovery.

---

### 4. Search Console & GA4 MCP Diagnostics

#### OAuth Redirect URI Requirements
* **OAuth Callback Endpoint:** `search-console-mcp` hardcodes the local OAuth callback server to:
  ```text
  http://localhost:3000/oauth2callback
  ```
* **Diagnostic Note:** Redirecting to `http://localhost:3000` without `/oauth2callback` will cause `404 Not Found` or server hang.

#### Error Code `7 PERMISSION_DENIED` (GA4 Data API)
* **Symptom:** `Google Analytics Data API has not been used in project X before or it is disabled.`
* **Fix Workflow:**
  1. Visit: `https://console.developers.google.com/apis/api/analyticsdata.googleapis.com/overview?project=<YOUR_PROJECT_ID>`
  2. Click the blue **ENABLE** button.
  3. In Google Analytics Admin → *Property Access Management*, add the Service Account email (`mcp-ga4@...`) with **Viewer** role.
  4. Run `npx search-console-mcp setup --engine=ga4` and supply the JSON key path and numeric Property ID (e.g. `123456789`).
