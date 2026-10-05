# Hybrid SIEM Detection Engineering Repository (Wazuh & Sigma)

This repository contains production-ready custom detection rules designed for comprehensive infrastructure monitoring. It demonstrates a practical application of the **Detection as Code (DaC)** concept, implementing both vendor-specific logic (**Wazuh XML**) and industry-standard vendor-agnostic signatures (**Sigma YAML**).

The rules focus on high-fidelity alerts, minimizing false positives, and are directly mapped to the **MITRE ATT&CK®** framework.

---

## 🌍 Мовна версія (Ukrainian Version)

<details>
<summary><b>Натисніть тут, щоб відкрити опис українською мовою</b></summary>

### Гібридний репозиторій інженерії детекції (Wazuh & Sigma)

Цей репозиторій містить готові до використання кастомні правила детекції для комплексного моніторингу інфраструктури. Проєкт демонструє практичне застосування концепції **Detection as Code (DaC)**, поєднуючи специфічну логіку для конкретної SIEM (**Wazuh XML**) та універсальний індустріальний формат сигнатур (**Sigma YAML**).

### 📁 Структура проєкту
* `wazuh/local_rules.xml` — Комплексний набір правил для Wazuh SIEM, що покриває атаки на Linux (SSH, Sudo, FIM), Windows та Web-сервери.
* `sigma/rules.yml` — Універсальні правила для Windows Endpoint & Sysmon логів у форматі Sigma.

</details>

---

## 📁 Repository Structure

```text
rules-collection/
├── wazuh/
│   └── local_rules.xml    # Vendor-specific rules for Wazuh SIEM (Linux, Windows, Web)
└── sigma/
    └── rules.yml          # Vendor-agnostic Endpoint & Sysmon rules (Windows/Host focus)
```

---

## 🛡️ MITRE ATT&CK® Coverage Matrix

| Rule ID | Technology / Logsource | Detection Name | MITRE ID | Tactics | Severity |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **100101** | Linux Auth Logs | SSH Bruteforce Detection | [T1110.001](https://mitre.org) | Credential Access | High (10) |
| **100200** | Linux Auth Logs | Sudo Privilege Escalation | [T1548.003](https://mitre.org) | Privilege Escalation | High (9) |
| **100300** | Wazuh Syscheck (FIM) | Critical System File Modification | [T1565](https://mitre.org) | Data Manipulation | Critical (10) |
| **100400** | Windows Security / Sysmon | User Added to Local Administrators | [T1136.001](https://mitre.org) | Persistence | Medium (8) |
| **100500** | Windows Security | Security Log Clearing (Event ID 1102) | [T1070.001](https://mitre.org) | Defense Evasion | Critical (11) |
| **100501** | Windows / Linux Execution | Log Tampering via `wevtutil` | [T1070.001](https://mitre.org) | Defense Evasion | High (9) |
| **100600** | Apache / Nginx Logs | Web Application Attack: SQLi | [T1190](https://mitre.org) | Initial Access | High (10) |
| **100601** | Apache / Nginx Logs | Web Application Attack: Path Traversal | [T1190](https://mitre.org) | Initial Access | High (9) |
| **100700** | Linux Process Monitoring | Anomalous Outbound Connection (Reverse Shell) | [T1095](https://mitre.org) | Command and Control | Critical (10) |
| **100800** | Linux Auditd / Sudo | Network Scanners and Utilities Launch | [T1046](https://mitre.org) | Reconnaissance | Medium (8) |
| **Sigma 1** | Windows / Sysmon (EDI 1) | Encoded PowerShell Command Execution | [T1059.001](https://mitre.org) | Execution | High |
| **Sigma 2** | Windows / Sysmon (EDI 10) | LSASS Memory Dump Access Attempt | [T1003.001](https://mitre.org) | Credential Access | Critical |
| **Sigma 5** | Windows / Sysmon (EDI 8) | Process Injection via `CreateRemoteThread` | [T1055](https://mitre.org) | Defense Evasion | High |

---

## 🚀 Deployment and Validation Instructions

### 1. Applying Wazuh Rules
1. Copy the content of `wazuh/local_rules.xml`.
2. Append it to your Wazuh Manager configuration file located at: `/var/ossec/etc/rules/local_rules.xml`.
3. Validate the rule syntax using the built-in tool:
   ```bash
   /var/ossec/bin/wazuh-logtest-realtime -t
   ```
4. Restart the Wazuh Manager to apply changes:
   ```bash
   systemctl restart wazuh-manager
   ```

### 2. Using Sigma Rules
Since Sigma rules are vendor-agnostic, they cannot be deployed directly into a SIEM. You must compile them into your SIEM's query language (e.g., Splunk SPL, Elastic Query, or Microsoft Sentinel KQL).
* You can use tools like [Uncoder.io](https://uncoder.io) or the native CLI tool `sigmac` to translate `sigma/rules.yml` into your target platform syntax.

---

## 🧠 Key Design Principles
* **Noise Reduction & False Positive Filtering:** Rules are fine-tuned with strict boundaries (e.g., regular expressions use boundary tags like `\b` to avoid false triggers on filenames, and exclusions are configured for native Windows binaries like `msmpeng.exe`).
* **Correlation-Driven Alerting:** High-frequency attacks like SSH bruteforce are handled via stateful correlation (`frequency="8"` within a `timeframe="30"` window from the `same_source_ip`) instead of flooding the console with individual alerts.

## 📝 Disclaimer
These detection signatures are developed using open-source specifications, official vendor documentation, threat intelligence frameworks, and AI-assisted optimization. Fully validated for syntactic correctness.
