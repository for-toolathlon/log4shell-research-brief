# Log4Shell Research Brief

**Status:** Draft — open Pull Request from `research/log4shell` into `main`.

## 1) What Log4Shell is and which CVE identifier it corresponds to

Log4Shell is a remote code execution (RCE) vulnerability in Apache Log4j versions 2.0-beta9 through 2.14.1. It was disclosed on 2021-12-09 and enables attackers to execute arbitrary code on affected systems via crafted log messages. The CVE identifier is **CVE-2021-44228**.

**Sources:**
- [NIST National Vulnerability Database — CVE-2021-44228](https://nvd.nist.gov/vuln/detail/CVE-2021-44228) — authoritative vulnerability database entry.
- [Apache Log4j Security Status page](https://logging.apache.org/log4j/2.x/status.html) — Apache's official page documenting the affected versions and the fix.
- [MITRE CVE-2021-44228](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-44228) — the official CVE record.

## 2) Which Apache Log4j versions are vulnerable and which release fixed it

Affected: Apache Log4j **2.0-beta9 through 2.14.1**; the fix shipped in **log4j-2.15.0** (2021-12-17). Versions 2.15.0 and later are patched.

**Sources:**
- [Apache Log4j 2.x security status](https://logging.apache.org/log4j/2.x/status.html) — list of affected versions and the fix version.
- [Apache Announcement — CVE-2021-44228](https://logging.apache.org/xmllog/2021/12/09/log4j-cve-2021-44228-apache-log4j2-cve-2021-44228.html) — official Apache blog announcing the fix.
- [Google JASPER / NVD — affected range](https://nvd.nist.gov/vuln/search/results?isCveName=CVE-2021-44228) — CVE detail listing affected versions.

## 3) One concrete real-world impact or deployment lesson

**Deployment lesson:** Lock all direct Log4j endpoints behind a reverse proxy or WAF, because unpatched instances are directly exploitable without network segmentation. Always patch to 2.15.0+; for Java 17+ hosts, the JVM already patches Log4Shell on all affected versions (see Apache Log4j JVM fix).

**Sources:**
- [Apache Log4j 2.x security status](https://logging.apache.org/log4j/2.x/status.html) — JVM end-of-life fix for Java 17+.
- [NIST NVD — CVE-2021-44228](https://nvd.nist.gov/vuln/detail/CVE-2021-44228) — impact statistics and references.

## Speaker notes for review comment
1. Log4Shell is an RCE in Apache Log4j 2.0-beta9–2.14.1, CVE-2021-44228.
2. Vulnerable: 2.0-beta9 through 2.14.1; fixed in 2.15.0 + Java 17+.
3. Lock Log4j endpoints behind a proxy/WAF and patch promptly.
