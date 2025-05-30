---
title: 🚀 Recommendations
toc: false
---

<div style="width: 100%; display: flex; justify-content: center; margin-bottom: 20px;">
  <h1 style="text-align: center;">Global Cybersecurity Threats: Recommendations 🚀</h1>
</div>

```js
const data = FileAttachment("data/Global_Cybersecurity_Threats_2015-2024.csv").csv({typed: true});
```

```js
const data_clean = data.map(d => ({
  ...d,
  Country:
    d.Country === "UK"
      ? "United Kingdom"
      : d.Country === "USA"
      ? "United States of America"
      : d.Country
}))
```

```js
const data_clean_withLossPerUser = data_clean.map(d => ({
  ...d,
  ["Loss per User"]: d["Number of Affected Users"] > 0
    ? (d["Financial Loss (in Million $)"] / d["Number of Affected Users"]) * 1000000
    : undefined
}))
```


```js
const country_select = Inputs.select(
  ["All", ...new Set(data_clean_withLossPerUser.map(d => d.Country))],
  { label: "Select a Country" }
)
```

```js
const  industry_select = Inputs.select(
  ["All", ...new Set(data_clean_withLossPerUser.map(d => d["Target Industry"]))],
  { label: "Select an Industry" }
)
```

```js
const industry_select_value = Generators.input(industry_select)
  ```

```js
const country_select_value = Generators.input(country_select)
  ```

<div style="width: 100%; margin-bottom: 40px; margin-top: 40px;">
    We enable our users to identify the most harmful attack types and sources for their respective target countries and industries. For each attack type and source, we suggest specific measures to prevent such attacks from happening. Additionally, we recommend the defence mechanism with the lowest average resolution time for the identified attack type, as this is the most effective way to quickly dissolve the attack.
</div>

<div style="text-align: center; font-weight: bold; font-size: 26px; margin-bottom: 20px; margin-top: 40px;">
            Please select the country and the industry that you are working in.
</div>

<div class="grid grid-cols-2">
    <div class="card">
      ${country_select}
    </div>
    <div class="card">
      ${industry_select}
    </div>
  </div>

<div style="height: 1px; background-color: #922847; margin: 1em 0;"></div>

```js
const avgLossByAttackType = Array.from(
  d3.rollups(
    data_clean_withLossPerUser.filter(d =>
          (country_select_value === "All" || d.Country === country_select_value) &&
          (industry_select_value === "All" || d["Target Industry"] === industry_select_value)
        ),
    v => d3.mean(v, d => d["Loss per User"]),
    d => d["Attack Type"]
  ),
  ([attackType, avgLoss]) => ({ attackType, avgLoss })
);
  ```

```js
  const highestAvgLossType = d3.max(avgLossByAttackType, d => d.avgLoss);
  ```


```js
  const mostSevereAttackType = avgLossByAttackType.find(d => d.avgLoss === highestAvgLossType);
  ```

```js
let answerAttackType;

switch (mostSevereAttackType.attackType) {
  case "SQL Injection":
    answerAttackType = "To mitigate SQL injection risks, ensure all database queries are parameterized or use prepared statements. Implement robust input validation on all user inputs and restrict database permissions using the principle of least privilege.";
    break;
  case "Phishing":
    answerAttackType = "Use advanced email filtering systems and implement multi-factor authentication (MFA) to reduce the impact of credential theft. Conduct regular security awareness training and simulated phishing exercises to increase employee vigilance.";
    break;
  case "Man-in-the-Middle":
    answerAttackType = "Enforce strong encryption protocols (e.g., TLS 1.3), use VPNs for remote access, and avoid untrusted Wi-Fi networks. Implement certificate pinning and continuous network monitoring for anomalies.";
    break;
  case "Ransomware":
    answerAttackType = "Maintain regular, offline backups and test restoration procedures. Use endpoint detection and response (EDR) tools, patch vulnerabilities promptly, and restrict user privileges to limit infection spread.";
    break;
  case "DDoS":
    answerAttackType = "Utilize DDoS mitigation services or a content delivery network (CDN) to filter traffic surges. Set up rate limiting on APIs and prepare a DDoS incident response plan to minimize downtime.";
    break;
  case "Malware":
    answerAttackType = "Deploy modern anti-malware tools, restrict macro execution, update software regularly, and educate staff on identifying suspicious downloads or links. Application whitelisting can further harden defenses.";
    break;
  default:
    answerAttackType = "ℹ️ No specific message for this attack type.";
}
```

```js
let answerAttackTypeDefense;

switch (mostSevereAttackType.attackType) {
  case "SQL Injection":
    answerAttackTypeDefense = "🛡 Recommended Defense Mechanism: Encryption & AI-based Detection - Offers the fastest response time against injection-based attacks by securing data layers and proactively identifying injection patterns.";
    break;
  case "Phishing":
    answerAttackTypeDefense = "🛡 Recommended Defense Mechanism: Encryption - Protects the integrity of sensitive communications and limits data exposure even if credentials are compromised.";
    break;
  case "Man-in-the-Middle":
    answerAttackTypeDefense = "🛡 Recommended Defense Mechanism: Antivirus - Provides quick detection and response capabilities for tampered data packets and unauthorized system intrusions.";
    break;
  case "Ransomware":
    answerAttackTypeDefense = "🛡 Recommended Defense Mechanism: Antivirus - Enables early detection and containment of ransomware behavior before encryption or propagation occurs.";
    break;
  case "DDoS":
    answerAttackTypeDefense = "🛡 Recommended Defense Mechanism: Firewall - Offers the most responsive control for filtering malicious traffic and protecting key infrastructure.";
    break;
  case "Malware":
    answerAttackTypeDefense = "🛡 Recommended Defense Mechanism: VPN - Reduces exposure to malware from unsafe networks and enforces encrypted, secure communication paths.";
    break;
  default:
    answerAttackTypeDefense = "ℹ️ No specific message for this attack type.";
}
```


<div style="text-align: center; font-weight: bold; font-size: 26px; margin-bottom: 20px; margin-top: 40px;">
    Your most severe attack type is ${mostSevereAttackType.attackType}.
</div>

<div style="font-size: 18px; margin-bottom: 20px;">
  <span style="font-weight: bold;">Average Loss per User:</span> ${highestAvgLossType.toFixed(0)} $
</div>

<div style="font-weight: bold; font-size: 18px; margin-bottom: 20px;">
    Recommendation:
</div>

<div style="width: 100%; margin-bottom: 20px;">
    ${answerAttackType}
</div>

<div style="width: 100%; margin-bottom: 40px;">
    ${answerAttackTypeDefense}
</div>




```js
const avgLossByAttackSource = Array.from(
  d3.rollups(
    data_clean_withLossPerUser.filter(d =>
          (country_select_value === "All" || d.Country === country_select_value) &&
          (industry_select_value === "All" || d["Target Industry"] === industry_select_value)
        ),
      v => d3.mean(v, d => d["Loss per User"]),
      d => d["Attack Source"]
    ),
  ([attackSource, avgLoss]) => ({ attackSource, avgLoss })
);
  ```

```js
  const highestAvgLossSource = d3.max(avgLossByAttackSource, d => d.avgLoss);
  ```
```js
  const mostSevereAttackSource = avgLossByAttackSource.find(d => d.avgLoss === highestAvgLossSource);
  ```

```js
let answerAttackSource;

switch (mostSevereAttackSource.attackSource) {
  case "Nation-state":
    answerAttackSource = "Given the sophistication of state-sponsored actors, invest in threat intelligence subscriptions to detect early indicators of targeted campaigns. Implement zero-trust architecture and harden systems that could be of strategic interest. Collaborate with national cybersecurity authorities and share IOCs (Indicators of Compromise) when available.";
    break;
  case "Hacker Group":
    answerAttackSource = "Strengthen external-facing systems such as VPNs, web apps, and email servers, which are commonly targeted by organized groups. Prioritize patch management and monitor the dark web for leaked credentials or exposed vulnerabilities in your sector. Establish an incident response plan to reduce impact from coordinated attacks.";
    break;
  case "Unknown":
    answerAttackSource = "Adopt a defense-in-depth strategy to ensure resilience against diverse and evolving threats. Log all user and system activities for traceability and maintain regular audits. Leverage anomaly detection tools to flag new or unknown attack patterns proactively.";
    break;
  case "Insider":
    answerAttackSource = "Enforce strict access controls and monitor for unusual activity among users with elevated privileges. Use data loss prevention (DLP) systems and maintain logs for sensitive data access. Foster a culture of security awareness and implement whistleblower channels to detect early signs of insider threats.";
    break;
  default:
    answerAttackSource = "ℹ️ No specific message for this attack source.";
}
```

<div style="height: 1px; background-color: #922847; margin: 1em 0;"></div>

<div style="text-align: center; font-weight: bold; font-size: 26px; margin-bottom: 20px; margin-top: 40px;">
    Your most severe attack source is ${mostSevereAttackSource.attackSource}.
</div>

<div style="font-size: 18px; margin-bottom: 20px;">
  <span style="font-weight: bold;">Average Loss per User:</span> ${highestAvgLossSource.toFixed(0)} $
</div>

<div style="font-weight: bold; font-size: 18px; margin-bottom: 20px;">
    Recommendation:
</div>

<div style="width: 100%;">
    ${answerAttackSource}
</div>
