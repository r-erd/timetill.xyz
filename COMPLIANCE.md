# Compliance & Legal

This document covers the legal and privacy compliance status of **timetill.xyz**.

> **Disclaimer:** This is a best-effort compliance overview. It is **not legal advice**. The site operator is responsible for ensuring all legal requirements are met.

---

## TL;DR

- **No tracking, no ads, no analytics, no external fonts or CDNs.**
- **All runtime code is self-contained** in the built bundle.
- **Two functional cookies** are used to remember your timer/countdown settings.
- **Hosting** is provided by GitHub Pages (server logs are processed by GitHub).
- **All dependencies** use permissive open-source licenses (MIT, Apache-2.0, BSD, ISC).

---

## 1. Dependency License Audit

### Direct Dependencies

| Package | Version | License |
|---------|---------|---------|
| `core-js` | 3.49.0 | MIT |
| `vue` | 3.5.34 | MIT |
| `vue-cookie-next` | 1.3.0 | MIT |

### Build/Dev Dependencies

| Package | Version | License |
|---------|---------|---------|
| `@vitejs/plugin-vue` | 5.2.4 | MIT |
| `eslint` | 8.57.1 | MIT |
| `eslint-plugin-vue` | 9.33.0 | MIT |
| `vite` | 5.4.21 | MIT |

### Transitive Runtime Dependencies

All transitive dependencies pulled into the production bundle also carry permissive licenses (MIT, BSD-2/3-Clause, ISC). **No GPL, AGPL, LGPL, or other copyleft licenses** are present in the dependency tree.

### External Network Requests by Dependencies

None of the runtime dependencies initiate external network requests. `vue-cookie-next` only interacts with the browser's native `document.cookie` API locally.

---

## 2. Privacy Notice

> **Rationale:** A full, formal privacy policy is disproportionate for a minimal static site with no analytics, no tracking, and no data collection by the operator. Under GDPR Art. 12–14, privacy information must be "concise, transparent, intelligible and easily accessible." The EDPB and German DPAs confirm that the format should match the complexity of processing. For a site this small, a short, accurate notice is sufficient.

> **TODO:** Replace `[email@example.com]` before publishing.

### What data is processed?

**By this application:** None. The operator does not collect, store, or process any personal data.

**By GitHub (hosting):** When you access this site, GitHub Pages processes technical connection data (IP address, browser type, date/time of request) in server logs. This is necessary to deliver the page. See the [GitHub Privacy Statement](https://docs.github.com/en/site-policy/privacy-policies/github-privacy-statement).

### Local Storage

Your countdown settings are saved **locally in your browser** using the Web Storage API (`localStorage`). This data never leaves your device and is not transmitted to any server.

| Key | Purpose |
|-----|---------|
| `countdown-date` | Target date/time of your countdown/timer |
| `countdown-unit` | Your preferred display unit |

### No analytics, no tracking, no external resources

- No Google Analytics, Plausible, Matomo, or similar
- No Google Fonts or CDN resources
- No social media embeds or third-party scripts

### Contact

[Name] — [email@example.com]

---

## 3. Impressum (German Legal Notice)

> **Not required for this site.**

Under German law (§ 5 TMG), an Impressum is only mandatory for **"geschäftsmäßige Telemedien"** — websites operated in a business-like manner. This includes commercial sites, but also non-commercial sites that are run in a structured, purposeful way (e.g., clubs, associations, portfolios advertising services).

**timetill.xyz** is a purely private hobby project:
- No ads, no tracking, no monetization
- No services, products, or paid offerings
- No business contact or commercial purpose
- Explicitly created for personal use ("I wrote this because I want to have an equivalent to crossing boxes on the calendar for adults")

Because the site is **purely private and non-commercial**, it falls outside the scope of § 5 TMG and **does not require an Impressum**.

If the character of the site changes in the future (e.g., monetization, offering services, or using it as a professional portfolio), an Impressum would become necessary.

---

## 4. Technical Compliance Notes

### No IP Transmission to Third Parties

All runtime assets (HTML, CSS, JS, favicon) are served from the same origin. No third-party requests are made that would transmit the visitor's IP address to external providers.

### Privacy by Default

- No cookies are set until the user explicitly starts a timer or countdown.
- No local storage or session storage is used.
- No fingerprinting or device identification is performed.

### Cookie Configuration

`vue-cookie-next` is configured with:
- `SameSite=Lax`
- `Path=/`
- `Secure` flag omitted (works on both HTTP and HTTPS; recommended to enable `Secure` once HTTPS is enforced)

### Build Verification

To verify compliance after each build, run:

```bash
# Ensure no external URLs are embedded in the bundle
grep -rE 'https?://' dist/assets/ || echo "No external URLs found"

# Ensure no analytics/tracking keywords are present
grep -riE 'gtag|analytics|tracking|google-analytics|plausible|matomo' dist/ || echo "No tracking code found"
```

---

## 5. Action Checklist

- [x] Dependency license audit completed (all permissive)
- [x] Source code audited for external requests (none found)
- [x] Build output audited for external requests (none found)
- [ ] Replace `[email@example.com]` in Privacy Notice (section 2)
- [x] Confirmed: No Impressum required under § 5 TMG (purely private, non-commercial site)
- [x] Add Privacy Policy link to the site footer
- [x] Switched from cookies to localStorage (data no longer sent with HTTP requests)
