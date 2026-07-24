---
name: web-deployment-mcp-seo
description: Setup, debug, and automate Vercel web deployments, Namecheap DNS configuration, WebMCP form security, and Model Context Protocol (MCP) integrations for Google Search Console and GA4. Use when deploying static or full-stack sites to Vercel, configuring custom domain DNS records, setting up search-console-mcp or GA4 service accounts, resolving Vercel CLI upload timeouts, or fixing Chrome insecure form autofill warnings.
---

# Web Deployment, DNS Routing & Search Console MCP Skill

An extensive, production-grade guide for web deployment automation, DNS troubleshooting, secure WebMCP forms, and Model Context Protocol (MCP) analytics integrations.

---

## 🔗 Official Repositories & Documentation Links

### Core Repositories & Tools
- 📦 **Search Console MCP Repository:** [saurabhsharma2u/search-console-mcp](https://github.com/saurabhsharma2u/search-console-mcp)
- 📦 **Vercel CLI Repository:** [vercel/vercel](https://github.com/vercel/vercel)
- 📦 **Google APIs Node Client:** [googleapis/google-api-nodejs-client](https://github.com/googleapis/google-api-nodejs-client)

### Essential Documentation Links to Visit
- 📚 **Search Console MCP Official Documentation:** [searchconsolemcp.saurabh.app](https://searchconsolemcp.saurabh.app/)
- 📚 **Google Analytics Data API Reference:** [developers.google.com/analytics/devguides/reporting/data/v1](https://developers.google.com/analytics/devguides/reporting/data/v1)
- 📚 **Google Cloud Console Credentials:** [console.cloud.google.com/apis/credentials](https://console.cloud.google.com/apis/credentials)
- 📚 **Bing Webmaster Tools API Settings:** [www.bing.com/webmasters/settings/api](https://www.bing.com/webmasters/settings/api)
- 📚 **Vercel Custom Domains & DNS Routing:** [vercel.com/docs/projects/domains](https://vercel.com/docs/projects/domains)
- 📚 **Schema.org ContactAction Microdata Spec:** [schema.org/ContactAction](https://schema.org/ContactAction)

---

## Quick start

```bash
# 1. Optimize deployment payload and deploy to Vercel production
npx vercel --prod --yes --scope <your-scope> --token=<your-token>

# 2. Authenticate Google Search Console via MCP
npx search-console-mcp setup --engine=google

# 3. Authenticate Google Analytics 4 (GA4) via MCP
npx search-console-mcp setup --engine=ga4
```

---

## Guided Workflows

### 🌐 1. Custom Domain & DNS Setup (Namecheap + Vercel)
- 💡 **Hint (Redirect Loops):** Check if `www` points to your root domain while the root domain redirects back. Keep only ONE target CNAME.
- 💡 **Hint (Vercel Anycast):** Point the `@` A Record to Vercel's Anycast IP (`216.198.79.1` or `76.76.21.21`).
- 💡 **Hint (Email Records):** Keep existing `v=spf1...` TXT records intact when updating DNS for mail forwarding.

### 🔒 2. Form Security & Chrome Autofill Compliance
- 💡 **Hint (Insecure Action):** Replace `action="mailto:"` with a secure HTTPS POST endpoint (e.g. FormSubmit).
- 💡 **Hint (Autofill Flags):** Add `autocomplete="on"` to `<form>` and explicit `autocomplete` types to inputs.
- 💡 **Hint (WebMCP Schema):** Annotate form fields with `itemscope itemtype="https://schema.org/ContactAction"`.

### 🔍 3. Search Console & GA4 MCP Integration
- 💡 **Hint (OAuth Callback):** The local OAuth server expects `/oauth2callback` at `http://localhost:3000/oauth2callback`.
- 💡 **Hint (GA4 API Denial):** If getting `7 PERMISSION_DENIED`, enable `analyticsdata.googleapis.com` in Google Cloud Console.
- 💡 **Hint (Service Account Role):** Grant the Service Account email **Viewer** access under GA4 *Property Access Management*.

---

## References & Documentation

- See [REFERENCE.md](REFERENCE.md) for deep technical diagnostics, error codes, and API enablement links.
- See [EXAMPLES.md](EXAMPLES.md) for complete template patterns, MCP client configs, and CLI snippets.
