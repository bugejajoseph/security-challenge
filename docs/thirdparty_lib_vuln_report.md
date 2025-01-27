# Third-Party Library Vulnerability Report

Date: January 27, 2025  
Classification: CONFIDENTIAL

## Overview
This document summarizes the vulnerabilities found in third-party libraries used in the project, including Tomcat Embed Core, Logback Classic, and Logback Core. These vulnerabilities could lead to security risks like code execution, unauthorized access, or information leakage. The document provides details on affected versions, severity, and mitigation recommendations.

## 1. Tomcat Embed Core Vulnerability

### Package Information
- **Package**: `org.apache.tomcat.embed:tomcat-embed-core`
- **Type**: Time-of-check Time-of-use (TOCTOU) Race Condition

### Severity Scores
- **Snyk**: 
  - CVSS v4.0: 9.2 (Critical Severity)
- **NVD**: Not available
- **Exploit Maturity**: No known exploit

### Affected Versions
Introduced through: `org.springframework.boot:spring-boot-starter-web@3.3.5`

### Fixed Versions
- `org.apache.tomcat.embed:tomcat-embed-core@11.0.2`
- `org.apache.tomcat.embed:tomcat-embed-core@10.1.34`

### Vulnerability Paths
1. Primary Path:
```
com.yieldstreet.challenges:security-challenge@0.0.1-SNAPSHOT
└─ org.springframework.boot:spring-boot-starter-web@3.3.5
   └─ org.springframework.boot:spring-boot-starter-tomcat@3.3.5
      └─ org.apache.tomcat.embed:tomcat-embed-core@10.1.31
```

2. Secondary Path:
```
com.yieldstreet.challenges:security-challenge@0.0.1-SNAPSHOT
└─ org.springframework.boot:spring-boot-starter-web@3.3.5
   └─ org.springframework.boot:spring-boot-starter-tomcat@3.3.5
      └─ org.apache.tomcat.embed:tomcat-embed-websocket@10.1.31
         └─ org.apache.tomcat.embed:tomcat-embed-core@10.1.31
```

### Issue Details
On case-insensitive file systems, when the default servlet is write-enabled, attackers can:
- Upload malicious files with executable code
- Bypass case sensitivity checks
- Execute files as JSP

### Mitigation by Java Version

| Java Version | Configuration Required |
|-------------|------------------------|
| Java 8/11 | Set `sun.io.useCanonCaches=false` (default: true) |
| Java 17 | Ensure `sun.io.useCanonCaches=false` if set (default: false) |
| Java 21+ | No configuration needed (cache removed) |

## 2. Logback Classic Vulnerability

### Package Information
- **Package**: `ch.qos.logback:logback-classic`
- **Type**: Improper Neutralization of Special Elements

### Severity Scores
- **Snyk**:
  - CVSS v4.0: 5.9 (Medium Severity)
- **NVD**: Not available
- **Exploit Maturity**: No known exploit

### Fixed Versions
- `ch.qos.logback:logback-classic@1.5.13`

### Vulnerability Path
```
com.yieldstreet.challenges:security-challenge@0.0.1-SNAPSHOT
└─ org.springframework.boot:spring-boot-starter-web@3.3.5
   └─ org.springframework.boot:spring-boot-starter@3.3.5
      └─ org.springframework.boot:spring-boot-starter-logging@3.3.5
         └─ ch.qos.logback:logback-classic@1.5.11
```

### Issue Details
Vulnerable through JaninoEventEvaluator extension:
- Attackers can execute arbitrary code
- Attack vectors:
  - Compromised logback configuration files
  - Injected environment variables before execution

## 3. Logback Core Vulnerabilities

### Package Information
- **Package**: `ch.qos.logback:logback-core`
- **Types**: 
  1. Improper Neutralization of Special Elements
  2. Server-side Request Forgery (SSRF)

### Severity Scores
1. Improper Neutralization:
   - **Snyk**:
     - CVSS v4.0: 5.9 (Medium Severity)
   - **NVD**: Not available

2. SSRF:
   - **Snyk**:
     - CVSS v4.0: 2.4 (Low Severity)
   - **NVD**: Not available

### Fixed Versions
- `ch.qos.logback:logback-core@1.5.13`

### Vulnerability Path
```
com.yieldstreet.challenges:security-challenge@0.0.1-SNAPSHOT
└─ org.springframework.boot:spring-boot-starter-web@3.3.5
   └─ org.springframework.boot:spring-boot-starter@3.3.5
      └─ org.springframework.boot:spring-boot-starter-logging@3.3.5
         └─ ch.qos.logback:logback-classic@1.5.11
            └─ ch.qos.logback:logback-core@1.5.11
```

### Issue Details
Vulnerable through the SaxEventRecorder process:
- Attackers can forge requests
- Attack vectors:
  - Compromised logback configuration files in XML

---

**Note**: All version numbers, paths, and severity scores should be regularly reviewed and updated as new information becomes available.