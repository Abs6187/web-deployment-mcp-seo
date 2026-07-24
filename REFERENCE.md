# Technical Reference & Troubleshooting Manual

An extensive technical reference covering diagnostic procedures, root-cause analyses, error code catalog, and step-by-step directions.

---

## Directional Navigation & API Setup Guide

### Google Cloud & Analytics Setup Directions
1. **Google Analytics Data API Enablement:**
   - Navigate to Google Cloud Console → APIs & Services → Library.
   - Search for **Google Analytics Data API** and click **Enable**.
2. **Service Account Setup:**
   - Navigate to Google Cloud Console → IAM & Admin → Service Accounts.
   - Create a Service Account, generate a JSON key, and download it locally.
3. **Google Analytics Access Delegation:**
   - Open Google Analytics → Admin → Property Settings → Property Access Management.
   - Add the Service Account email address with **Viewer** permissions.

### Hosting & Search Engine Directions
1. **Google Search Console Ownership:**
   - Open Google Search Console, add your domain property, and submit `sitemap.xml`.
2. **Bing Webmaster Tools Setup:**
   - Open Bing Webmaster Tools, import your verified Search Console property, or generate an API key under Account Settings.

---

## Deep Diagnostic Procedures

### 1. Vercel CLI Upload & Network Diagnostics

#### Symptom: `ECONNRESET` / Deployment Upload Timeout
* **Root Cause Analysis:** Vercel CLI packages all files in the current working directory by default. When `.git` directories, `node_modules`, or build caches are present, the payload size easily exceeds 10MB+. On slower connections, the HTTP POST stream breaks, resulting in a TCP `ECONNRESET`.
* **Resolution Directions:**
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
  3. Re-run deployment using official Vercel CLI:
     ```bash
     vercel --prod
     ```

---

### 2. DNS Verification & Redirect Loop Diagnostics

#### Symptom: `ERR_TOO_MANY_REDIRECTS` (Infinite 301 Loop)
* **Root Cause Analysis:** Occurs when a `CNAME` for `www` points directly to the root domain while the root domain redirects back to `www`.
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
* **Resolution Directions:**
  1. Replace `action="mailto:..."` with a secure HTTPS POST form target endpoint.
  2. Add `autocomplete="on"` to `<form>` and `autocomplete="name"`, `autocomplete="email"` to inputs.
  3. Annotate input fields with `itemprop="name"`, `itemprop="email"`, `itemprop="description"` for WebMCP AI agent discovery.
