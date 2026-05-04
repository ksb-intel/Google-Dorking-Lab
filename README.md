# Google-Dorking-Lab

## Overview

<img width="1274" height="659" alt="googledork" src="https://github.com/user-attachments/assets/b266b442-a97a-4a0d-8cd0-f9ef74a73e1f" />

<br>

A hands-on exercise simulating passive OSINT reconnaissance using advanced search operators in Google Search. In this excercise, the target of reconnaissance is the publicly indexed digital footprint of the University of California, Los Angeles (UCLA). 

## ⚠️ Disclaimer

This lab was produced as part of a OSINT training exercise of "Google Dorking." All reconnaissance was conducted passively using publicly indexed information only. No systems were accessed, no authentication was attempted, and no data was exfiltrated. This lab is for educational purposes and should not be used for any unauthorized or malicious activity.


---

## What is Google Dorking?

Google Dorking uses advanced search operators to find public information less accessible when conducting normal searches. It can be used in cybersecurity recon to identify exposed databases, confidential files, login portals, and vulnerabilities. Additionally it is also used in OSINT and threat analysis.

**Key Uses:**
- Cybersecurity Reconnaissance: Discover weaknesses in public websites such as unpatched systems or exposed assets.
- Finding Sensitive Information: Locating documents accidently left public or finding other confidential files.
- Locating Vulnerable Assets: Finding exposed admin login portals, unsecured directories, or logs with confidential technical details that threaten a website's infrastructure.
- Asset Discovery: Utilized by bug bounty hunters and researchers to find forgotten subdomains.

---

## Operators Used in This Lab

| Operator | What it does | Example |
|----------|-------------|---------|
| `site:` | Restrict results to a domain | `site:example.com` |
| `filetype:` | Find specific file types | `filetype:pdf` |
| `intitle:` | Match words in the page title | `intitle:"index of"` |
| `inurl:` | Match words in the URL | `inurl:admin` |
| `" "` | Exact phrase match | `"annual report"` |
| `-` | Exclude a term or operator | `-site:example.com` |
| `OR` | Match either term | `filetype:pdf OR filetype:xlsx` |

---

## Metadata
 
| Field | Detail |
|-------|--------|
| Target | University of California, Los Angeles (UCLA) |
| Domain | ucla.edu |
| Methodology | Passive OSINT by Google dorking |
| Classification | Training exercise |
 
---
 
## Executive Summary
 
UCLA has a large and distributed public web presence with over four million Google-indexed pages. The reconnaissance revealed publicly accessible institutional documents, visible faculty and student profiles on LinkedIn, and — most notably — exposed directory listings across multiple subdomains resulting from web server misconfiguration. No login portals were indexed, which is a positive security indicator. No sensitive personal data was accessed or retained during this exercise.
 
---
 
## Phase 1 — Surface Mapping
 
### Task 1 — Map the main domain
 
**Dork:**
```
site:ucla.edu
```

<img width="1287" height="874" alt="sitemapping1" src="https://github.com/user-attachments/assets/06b0a5a0-e12d-4b43-8b4e-5a76cec6aada" />

<br>

**Results:** ~4,050,000 indexed results (0.18s)
 
**Findings:**
 
The top results surfaced three notable subdomains:
- `clarklibrary.ucla.edu` — Clark Library portal
- `fowler.ucla.edu` — Fowler Museum
- `equity.ucla.edu` — Office of Equity, Diversity and Inclusion
The initial result set skewed heavily toward arts, museum, and library content, suggesting these sections are heavily indexed by webcrawlers and frequently linked externally.
 
---
 
### Task 2 — Find exposed documents
 
**Dork:**
```
site:ucla.edu filetype:pdf OR filetype:xlsx OR filetype:docx
```

<img width="1110" height="903" alt="filetype" src="https://github.com/user-attachments/assets/463eefdc-30aa-497d-8463-dc72a48f9493" />

<br>

**Findings:**
 
All surfaced documents were in PDF format. No spreadsheets or Word documents appeared in top results. Document categories identified:
 
- Practical training documentation
- Online workshop materials
- Registrar information
- Legal services documents
These represent standard institutional disclosures. Based on titles and snippets alone, no documents appeared to contain sensitive operational data.
 
---
 
## Phase 2 — Employee & Contact Intelligence
 
### Task 3 — Find staff on LinkedIn
 
**Dork:**
```
site:linkedin.com/in "UCLA"
```

<img width="1085" height="880" alt="uclalinkedin" src="https://github.com/user-attachments/assets/aaef581f-5d37-4153-abe4-5915b632b221" />

<br>
 
**Findings:**
 
LinkedIn profiles for UCLA-affiliated individuals surfaced readily, including faculty members across multiple academic departments and PhD candidates. This level of visibility is expected for a large research university, but in an adversarial context this data provides a basis for org-chart reconstruction, social engineering, or spear phishing targeting.
 
---
 
### Task 4 — Find email format clues
 
**Dork:**
```
"@ucla.edu" -site:ucla.edu
```

<img width="1297" height="894" alt="emailformatclues" src="https://github.com/user-attachments/assets/5e99bb04-32d5-4ea8-a176-f6e5988385c8" />

<br>
 
**Findings:**
 
Top results did not surface individual employee email addresses. Instead, results returned:
- UCLA's official social media accounts
- External websites referencing UCLA
- UCLA's Wikipedia page
- The UCLA subreddit
Email format could not be confirmed from this dork alone. This suggests addresses are not widely referenced on third-party sites, or that Google's indexing of them is suppressed — a relatively positive indicator.
 
---
 
## Phase 3 — Infrastructure & Technology
 
### Task 5 — Find login and admin portals
 
**Dork:**
```
site:ucla.edu inurl:login OR inurl:admin OR inurl:portal OR inurl:dashboard
```

<img width="1025" height="793" alt="uclalogin" src="https://github.com/user-attachments/assets/47c791e2-56dd-4413-9d97-24e075838c0f" />

<br>
 
**Findings:**
 
No results returned. This is a positive security indicator — login and administrative interfaces are either not publicly indexed, hosted on separate domains, or effectively delisted from Google. This phase produced no actionable findings.
 
---
 
### Task 6 — Check for exposed directories 
 
**Dork:**
```
site:ucla.edu intitle:"index of"
```

<img width="1135" height="889" alt="directories" src="https://github.com/user-attachments/assets/88958437-764b-4b5e-bc6b-e5fb40fcb564" />

<br>
 
**⭐ Key Finding:**
 
Multiple directory and index listings were identified across different UCLA subdomains. These open directories result from web server misconfiguration — specifically, directory listing being enabled without an index file to suppress it.
 
> Note: Identified directories were not clicked or accessed. Findings are based on Google result titles and URL previews only, consistent with passive reconnaissance methodology.
 
**Why this matters:**
- Exposed directories can reveal internal file structures, backup files, configuration files, and unpublished assets
- Even if files appear benign, the directory structure itself leaks information about system architecture
- Remediation: disable directory listing at the web server level (`Options -Indexes` in Apache, `autoindex off` in Nginx)
---
 
## Phase 4 — Third-Party Mentions
 
### Task 7 — Find mentions in job postings
 
**Dork:**
```
"UCLA" site:indeed.com OR site:glassdoor.com OR site:lever.co
```
<img width="1178" height="888" alt="uclajobs" src="https://github.com/user-attachments/assets/7b18a7f4-9a93-4f3c-8ace-23495285f55c" />

<br>
 
**Findings:**
 
Job postings were found for a range of UCLA positions including part-time roles, student employment, and on-campus positions. For a university this is expected and low-risk. In a corporate OSINT context, job postings at this stage would typically reveal tech stack, tooling, and team structure — here the postings reflect no notable technology disclosures.
 
---
 
## Risk Summary
 
| Finding | Severity | Recommendation |
|---------|----------|----------------|
| Open directory listings on subdomains | **Medium** | Disable directory listing at the web server level |
| Public document indexing (PDFs) | Low | Audit publicly accessible documents; remove any sensitive materials |
| Staff profiles visible on LinkedIn | Informational | User awareness — expected for a university |
| No login portals indexed | N/A — Positive | No action required |
| Email format not confirmed | Informational | No action required |
 
---
 
## Responsible Disclosure
 
If the open directory listings identified in Phase 3 represent a genuine misconfiguration of UCLA systems, findings should be reported to UCLA's information security team.

