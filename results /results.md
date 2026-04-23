# Scan Results and Reporting Format

This document outlines the structure and classification of the intelligence gathered by Infined. All automated reports are generated in real-time and stored within the internal memory-bridge before being exported to the designated output format.

## 1. Report Classification

Infined categorizes findings into three primary severity levels based on the potential for system compromise:

* **CRITICAL**: Direct access to backend infrastructure (e.g., exposed .env, valid admin JWT, leaked AWS Secret Keys).
* **HIGH**: Sensitive information that facilitates exploitation (e.g., API documentation leaks, exposed staging subdomains, session cookies).
* **MEDIUM**: Information leakage and configuration oversights (e.g., active Source Maps, internal IP disclosure, weak CORS policies).

## 2. Standard Output Format (JSON)

By default, Infined exports data in a structured JSON format to allow for seamless integration with other security tools or custom post-exploitation scripts.

### Schema Example:
```bash
  json
{
  "timestamp": "2026-04-23T11:00:00Z",
  "target": "[https://api.target-redacted.com](https://api.target-redacted.com)",
  "intelligence_unit": "The-Capsule",
  "findings": [
    {
      "type": "API_KEY_LEAK",
      "source": "/static/js/main.chunk.js",
      "value": "AIzaSyD-xxxxxxxxxxxxxx",
      "severity": "CRITICAL"
    }
  ],
  "exploitation_status": {
    "unit": "The-Frog",
    "attempt": "Token Injection",
    "result": "SUCCESS",
    "access_level": "Unauthorized/Partial"
  }
}
```
## 3. Terminal Interface Visualization

Infined utilizes the **Rich** library to provide high-fidelity visual feedback during the scanning and exploitation phases. Below is a representation of the localized terminal output:

| Module | Status | Found Item | Severity |
| :--- | :--- | :--- | :--- |
| **The-Capsule** | COMPLETED | `firebase_config.json` | CRITICAL |
| **The-Capsule** | COMPLETED | `auth_token_secret` | HIGH |
| **The-Frog** | EXECUTING | `POST /api/v1/admin/login` | ATTEMPTING |

## 4. Audit Trail and Session Logs

Every recursive cycle generates an audit trail. This is crucial for security researchers to understand the path Infined took from initial reconnaissance to successful exfiltration.

* **Logic Path Tracking**: Logs which JavaScript file led to which API endpoint.
* **Header Effectiveness**: Records which spoofed headers (e.g., `X-Forwarded-For`) successfully bypassed rate-limiting.
* **Error Handling**: Detailed capture of WAF (Web Application Firewall) responses to refine the next recursive attempt.

## 5. Exporting Results

Users can export findings into multiple formats using specific CLI flags:

* `--export-json`: Generates the structured schema shown in Section 2.
* `--export-csv`: Simplified table for quick manual audit.
* `--export-txt`: Raw log format for terminal-based piping.

> **Storage Note**: To ensure maximum operational security, Infined does not store logs in a centralized cloud. All results are kept within the user's local `results/` directory or process memory until manually saved.


# How to Use

Infined is designed with a streamlined Command Line Interface (CLI). You can initiate the recursive engine by targeting a specific URL or a list of domains.

## CLI Arguments

| Flag | Long Flag | Description | Example |
| :--- | :--- | :--- | :--- |
| `-h` | `--help` | Show help message and exit | `python main.py -h` |
| `-t` | `--target` | Define the primary target URL | `-t https://target.com` |
| `-l` | `--list` | Scan multiple targets from a file | `-l targets.txt` |
| `-m` | `--module` | Run a specific unit (Capsule/Frog) | `-m capsule` |
| `-r` | `--recursive` | Enable the continuous feedback loop | `--recursive` |
| `-o` | `--output` | Define output file name | `-o result.json` |

## Basic Usage

To run a full recursive scan on a single target:
```bash
python main.py --target [https://example.com](https://example.com) --recursive
```
To run only the intelligence unit (The-Capsule) on a list of targets:
```bash
python main.py --list targets.txt --module capsule
```
## Real-time Execution Feed

When running, the terminal will display the high-fidelity feed as follows:

| Module | Status | Found Item | Severity |
| :--- | :--- | :--- | :--- |
| **The-Capsule** | COMPLETED | `firebase_config.json` | CRITICAL |
| **The-Capsule** | COMPLETED | `auth_token_secret` | HIGH |
| **The-Frog** | EXECUTING | `POST /api/v1/admin/login` | ATTEMPTING |

> **Note**: For advanced configuration, such as custom header rotation or proxy settings, please refer to the internal documentation in the `information/` directory.
