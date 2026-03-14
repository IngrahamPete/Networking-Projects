# Web Application Denial-of-Service Mitigation Report

**Author:** Pete Ingraham 
**Date:** December 5, 2025
**Classification:** Educational / Training Exercise  
**Version:** 1.0  

---

## 1. Executive Summary

This report documents a controlled security and resilience assessment focused on denial-of-service (DoS) mitigation for a web server environment using NGINX. The objective of the project was to implement and validate rate limiting, connection limiting, and timeout-based protections against abusive or slow client behavior.

Testing was conducted in a lab environment using a vulnerable web server configuration and a hardened server configuration with DoS mitigation rules enabled. The analysis compared server behavior, connection metrics, and packet captures to determine whether the mitigation strategy successfully reduced the effectiveness of excessive or slow connection attacks.

The results showed that the hardened configuration was more resilient under abusive traffic conditions. In the mitigated environment, dropped connections increased while accepted connections remained near the configured threshold, indicating that excess traffic was being refused. At the same time, idle or zero-payload connections were actively terminated rather than allowed to accumulate. These observations support the conclusion that the NGINX hardening rules improved service stability and reduced server exposure to connection exhaustion attacks.

---

## 2. Scope & Objectives

### 2.1 Scope

#### In Scope
- NGINX web server configuration
- DoS mitigation rules in `/etc/nginx/conf.d/default.conf`
- Connection limiting behavior
- Timeout handling for idle or abusive sockets
- Comparison of vulnerable and hardened packet captures
- Server-side metrics interpretation

#### Out of Scope
- Distributed denial-of-service (DDoS) simulation at Internet scale
- Application-layer exploit development
- Social engineering
- Physical security
- Third-party cloud protections

### 2.2 Objectives

- Implement DoS mitigation controls in NGINX
- Validate that connection abuse is limited by configuration
- Compare vulnerable and hardened environments using traffic evidence
- Interpret metrics and packet captures to explain mitigation effectiveness
- Present findings in a clear technical report format

---

## 3. Environment Overview

### Target Components
- **Web Server:** NGINX
- **Configuration File:** `/etc/nginx/conf.d/default.conf`
- **Assessment Focus:** Connection-based DoS mitigation
- **Evidence Sources:** Configuration screenshot, monitoring metrics, and `.pcap` analysis

### Assessment Context

This project was performed as an educational lab exercise. The purpose was to observe the operational difference between:
1. A server without effective DoS protections
2. A server with NGINX mitigation controls enabled

---

## 4. Methodology

### 4.1 Testing Approach

The assessment followed a practical validation workflow:

#### Phase 1: Configuration Review
- Review NGINX configuration
- Confirm presence of connection and request limiting directives
- Verify timeout-related controls

#### Phase 2: Traffic Observation
- Monitor connection behavior during abusive or sustained traffic
- Review accepted connections and dropped connection metrics
- Observe whether concurrent connections exceed expected thresholds

#### Phase 3: Packet Capture Analysis
- Compare `.pcap` files from the vulnerable and mitigated servers
- Identify TCP teardown behavior
- Look for signs of idle, long-lived, or zero-payload sessions

#### Phase 4: Reporting
- Document observations
- Explain which capture corresponds to which server state
- Summarize mitigation effectiveness

---

## 5. Tools Used

| Tool | Purpose |
|---|---|
| NGINX | Web server and mitigation enforcement |
| Packet capture analysis tools | Review TCP behavior and server responses |
| System / dashboard metrics | Observe accepted, dropped, and current connection counts |
| Linux configuration files | Validate server-side mitigation rules |

---

## 6. Detailed Findings

### Finding #1: NGINX DoS Mitigation Rules Successfully Restrict Excess Connections

**Severity:** High  
**Category:** Availability Protection  
**Status:** Mitigated in Hardened Configuration  

#### Description

The hardened NGINX configuration was designed to prevent excessive client connections from exhausting server resources. The goal of these controls was to limit abusive connection behavior and ensure that traffic beyond configured thresholds would be refused or closed rather than allowed to accumulate indefinitely.

#### Evidence

A screenshot of the `/etc/nginx/conf.d/default.conf` file was included as part of the project requirements to demonstrate that DoS mitigation rules were implemented. :contentReference[oaicite:1]{index=1}

#### Security Impact

Without connection limits and timeout enforcement, a web server may remain vulnerable to slow or excessive connection abuse, which can degrade performance or deny service to legitimate users. Proper mitigation reduces the likelihood that a small number of abusive clients can monopolize connection slots.

#### Conclusion

The server hardening effort improved resilience against connection exhaustion by enforcing server-side controls within NGINX.

---

### Finding #2: Metrics Confirm That Excess Connections Were Refused

**Severity:** High  
**Category:** Operational Validation  
**Status:** Confirmed  

#### Description

Observed metrics indicate that the mitigated server correctly enforced connection limits during abusive traffic conditions.

#### Evidence

The project notes state that the **Dropped** metric rose sharply while **HTTP Connections Accepted** stayed flat at the configured limit. At the same time, the **NGINX Current Connections** graph did not exceed the expected threshold, and long-idle zero-payload streams disappeared. This indicates that excessive connections were being refused and that abusive slow connections were being closed instead of remaining active. :contentReference[oaicite:2]{index=2}

#### Analysis

These are strong indicators of effective DoS mitigation:

- A rising **Dropped** count suggests the server is actively rejecting traffic that exceeds policy.
- A stable **Accepted Connections** value suggests the system is enforcing a ceiling rather than allowing uncontrolled growth.
- A capped **Current Connections** graph suggests successful containment of concurrent sessions.
- The disappearance of long-idle streams suggests timeout controls are functioning as intended.

#### Conclusion

The metrics support the conclusion that the hardened server behaved as expected under stress and prevented excessive connection buildup.

---

### Finding #3: Packet Captures Distinguish the Vulnerable Server from the Hardened Server

**Severity:** Medium  
**Category:** Traffic Analysis  
**Status:** Confirmed  

#### Description

The packet captures from the two environments can be distinguished by analyzing connection teardown behavior and the persistence of idle sessions.

#### Evidence

The project explanation states that the **DoS-mitigated server** is identified by a surge of **TCP RST** packets originating from the server when limits or timeouts are exceeded. This behavior shows that NGINX is actively terminating slow or excessive client sessions.

The **vulnerable server** capture, by contrast, is characterized by long idle, zero-payload keep-alive packets, an absence of timely `FIN` or `RST` teardown, and a steadily increasing count of open connections. This indicates that abusive sessions remain open and consume server resources. :contentReference[oaicite:3]{index=3}

#### Analysis

This distinction is important because it shows the real network-level effect of mitigation:

- **Mitigated server:** active enforcement, quick teardown, resource protection
- **Vulnerable server:** lingering sessions, growing open connections, higher exposure to exhaustion

#### Conclusion

The `.pcap` evidence provides a reliable way to identify which server was protected and which remained vulnerable.

---

## 7. Comparative Assessment

| Characteristic | Vulnerable Server | Hardened Server |
|---|---|---|
| Connection growth | Continues increasing | Limited to configured threshold |
| Idle zero-payload sessions | Persist | Removed or closed |
| TCP reset behavior | Minimal or absent | Frequent when limits are exceeded |
| Resource protection | Weak | Improved |
| Resistance to slow connection abuse | Low | Higher |

---

## 8. Risk Assessment

### Before Mitigation
The vulnerable server faced a significant availability risk. A relatively small number of abusive or slow connections could consume available connection slots and reduce service quality for legitimate users.

### After Mitigation
The hardened server demonstrated improved control over connection handling. By limiting accepted sessions and actively dropping abusive traffic, the risk of successful connection exhaustion was reduced.

---

## 9. Recommendations

### Immediate Recommendations
- Keep connection limiting enabled in NGINX
- Maintain aggressive timeout settings for idle or suspicious sessions
- Regularly review current connection and dropped connection metrics
- Validate configuration changes after updates or deployments

### Additional Hardening Recommendations
- Add request rate limiting where appropriate
- Use upstream monitoring and alerting for connection spikes
- Place the service behind a reverse proxy or load balancer when possible
- Implement Web Application Firewall (WAF) protections if available
- Test resilience periodically using authorized lab simulations

---

## 10. Conclusion

This project demonstrated the practical value of NGINX-based DoS mitigation in a controlled lab environment. The hardened server showed measurable improvements in handling excessive and slow client connections. Metrics confirmed that abusive traffic was refused once configured thresholds were reached, while packet captures showed active termination behavior through TCP reset activity.

The vulnerable server, in contrast, allowed idle and slow connections to persist, making it more susceptible to connection exhaustion. Overall, the findings confirm that properly configured NGINX controls can significantly improve availability protection against basic DoS-style connection abuse.

---

## 11. Appendix

### Appendix A: Required Project Evidence

#### Item #1
Screenshot of `/etc/nginx/conf.d/default.conf` showing implemented DoS mitigation rules. :contentReference[oaicite:4]{index=4}

#### Item #2
Metrics interpretation showing:
- Dropped metric rises sharply
- HTTP Connections Accepted stays near configured limit
- NGINX Current Connections remains capped
- Long-idle zero-payload streams are removed :contentReference[oaicite:5]{index=5}

#### Item #3
PCAP identification explanation:
- Mitigated server shows TCP RST activity when limits/timeouts are exceeded
- Vulnerable server shows long idle keep-alive packets and increasing open connections :contentReference[oaicite:6]{index=6}

---

## 12. Reflection

### Reflection Question #1
If I had to explain what a proxy server is in three emojis, I would use:

`🌐 🔄 🛡️`

### Reflection Question #2
Some different types of DoS/DDoS attacks include:
- **Volumetric attacks**
- **Protocol attacks** :contentReference[oaicite:7]{index=7}
