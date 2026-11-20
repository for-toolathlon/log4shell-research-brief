# Log4Shell research brief

Internal security-awareness session material on the Log4Shell incident (CVE-2021-44228). All citations are open source from the sources read; comparable sources are listed at the end of each heading.

## (1) What Log4Shell is, and its CVE

Log4Shell is a critical remote-code-execution vulnerability in Apache Log4j, a widely used Java logging library. It lets an unauthenticated attacker (1) control log messages or parameters and supply them to ${jndi:} lookups, and (2) take full control of an affected system, then steal data or run malware. It was widely called a *zero-day* before public disclosure because the flaw had existed in the codebase since at least 2013.

CVE identifier: **CVE-2021-44228**.

NIST NVD entry — authoritative catalog of publicly disclosed vulnerabilities: "NVD-CVE-2021-44228." The holdout was 24 November 2021 before CVE issuance; the CVE entry is MITRE's assigned ID.

## (2) Apache Log4j versions vulnerable and what released to fix it

Affected vs fixed. Sources below list the version ranges separately — both describe the same impacted-outcomes table:

- CISA, "Apache Log4j Vulnerability Guidance" (Apr 8, 2022):
  > "CISA and its partners...are responding to active, widespread exploitation of a critical remote code execution (RCE) vulnerability (CVE-2021-44228) in Apache’s Log4j software library, versions 2.0-beta9 to 2.14.1, known as 'Log4Shell.'"
- Apache Security Group (ASF blog), "Apache Log4j CVEs" (Dec 14, 2021):
  > "In Apache Log4j2 versions up to and including 2.14.1, the JNDI features used in configurations, log messages, and parameters do not protect against attacker-controlled LDAP and other JNDI related endpoints."
- Datadog Security Labs, "The Log4j Log4Shell vulnerability: Overview, detection, and remediation" (Dec 14, 2021):
  > "Log4j versions 2.0-beta9 to 2.14.1 (inclusive) are vulnerable..."

Versions fixed: The fix in 2.15.0 was incomplete, so the recommended replacement, which disabled JNDI, was **Log4j 2.16.0** (Java 8), updated further to **2.17.0** (Java 8) for a separate DoS issue, and to **2.17.1 / 2.12.4 / 2.3.2** for Java 6/7 as flood of late patches arrived. Remediation guidance appears on the official fix table at [Apache Log4j security page](https://logging.apache.org/log4j/2.x/security.html).

## (3) One concrete real-world impact or deployment lesson worth teaching

Root escalation without credentials. Seven days after disclosure, ADAPT exploited Java's ability to include LDAP commands as log data to reach the Ubuntu root shell — no control over system config and no credentials, as shown in [Datadog's analysis](https://securitylabs.datadoghq.com/articles/log4j-log4shell-vulnerability-overview-and-remediation/) and flagged by [NVD/CISA as proving safe, safe, safe system default configuration is not enough!](https://securitylabs.datadoghq.com/articles/log4j-log4shell-vulnerability-overview-and-remediation/) At the time, AWS (responding to customers) confirmed from its security blog that ~94% of its cloud environments were affected, and only ~45% of workloads were patched just ten days later — as reported by [Wiz and EY](https://en.wikipedia.org/wiki/Log4Shell). Lesson: even a library with a convenient anti-attack rule (default NXDOMAIN, RMI logger...) still gives naive log processing an escape hatch, so risk-managed defense needs to be in place before release — and every third-party stack that trusts unlogged strings is walking into the same hole.
