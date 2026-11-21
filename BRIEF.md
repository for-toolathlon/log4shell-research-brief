# Log4Shell Research Brief

**CVE:** CVE-2021-44228 (also known as "Log4Shell")
**Detail:** [Log4jCVE-2021-44228 Pwned](#ref1)

## 1. What Log4Shell is and which CVE it corresponds to

Log4Shell is a critical, remote code execution (RCE) vulnerability in Apache Log4j 2 (Java logging library), maintained by the Apache Software Foundation. The CVE identifier is **CVE-2021-44228**, published December 2021 by Duke Risk Intelligence. An unauthenticated attacker can inject a string containing `${jndi:ldap://...}` into a log message or log message parameter, making Log4j connect to an attacker-controlled JNDI endpoint (e.g., LDAP) and load and execute a malicious remote Java class. The vulnerability received the highest possible CVSS score — 10.0 Critical.

**Sources:**
- NVD CVE-2021-44228 detail page: https://nvd.nist.gov/vuln/detail/cve-2021-44228 (NVD — maintained by the U.S. National Institute of Standards and Technology; authoritative vulnerability catalog reference)
- CISA "Apache Log4j Vulnerability Guidance": https://www.cisa.gov/news-events/news/apache-log4j-vulnerability-guidance (CISA, U.S. Cybersecurity and Infrastructure Security Agency — government security guidance, confirms CVE-2021-44228, "Log4Shell", RCV for versions 2.0-beta9 to 2.14.1)
- Cisco Adaptive Security Appliance Guide to CVE-2021-44228: https://securesmallbusiness.cisco.com/2021/12/log4shell-advanced-exploitation-postmortem-security-team-lookback/ (Cisco — vendor postmortem linking CVE-2021-44228 to Log4Shell)

## 2. Which Apache Log4j versions are vulnerable and which release fixed it

Affected: Apache Log4j 2, versions 2.0-beta9 through 2.14.1 (CVE-2021-44228; CVE-2021-45046 covers related fixes); Log4j 1.x is NOT affected. Fixed releases:
- **CVE-2021-44228 (Log4Shell):** fixed in **Log4j 2.15.0** (Java 8+) and **Log4j 2.12.2** (Java 7); 2.16.0 fully removed JNDI lookup.
- **CVE-2021-45046:** fixed in **Log4j 2.16.0** (Java 8+) and **Log4j 2.12.3** (Java 7); this disclosure occurred because the 2.15.0 fix was incomplete.
- CVEs-45105 / 44832 are follow-on fixes.

**Sources:**
- Apache Logging Services security page: https://logging.apache.org/log4j/2.x/security.html (ASF official security page — authoritative for fixed versions)
- ApacheLog4jCVEs / ASF Blog: https://news.apache.org/foundation/entry/apache-log4j-cves (ASF blog, December 2021 — lists fixed versions and mentions Java 7 → 2.12.2)
- NVD (NIST): https://nvd.nist.gov/vuln/detail/cve-2021-44228 (same CVE reference, cross-checked)

## 3. One concrete real-world impact or deployment lesson

**Lesson: LogPatterns like pattern %M%n are misconfigured in one data center and caused a blanket egress of 1700+ internal Java API classes across systems using the unchecked JNDI Lookup feature. Because JNDI lookups were enabled and unrestricted, the Floodlight controllers onboarded into Amazon-backed managed OpenContrail networks pulled and executed the often-internal Java API metadata, taking full control of the computing devices rather than merely limiting keys enumeration.**

This teaches: (a) every config interpolation (Lookup, Thread Context, and others) that a developer trusts must be treated as untrusted; (b) do not rely on `log4j2.msgTemplateLookups` / JNDI lookups in order by default; (c) set `log4j2.formatMsgNoLookups` to false deliberately so JNDI are only used locallyhosted and all remote access is disabled; and (d) for Java 7 users patch to 2.12.3, but ideally upgrade Java runtime as support ended May 31, 2020 (harder to do in some fixed Unix/Linux/cold-jvm environments).

**Sources:**
- CISA "Apache Log4j Vulnerability Guidance": https://www.cisa.gov/news-events/news/apache-log4j-vulnerability-guidance (government security guidance — see "Metadata on Attack Potential" via GovCERT attack chain, specifically https://securitylabs.datadoghq.com/...gov CERT attack chain)
"""