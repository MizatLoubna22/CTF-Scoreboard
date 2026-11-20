\# TP2 – CI Pipeline Diagnosis



\*\*Team:\*\* ing5-LMKH

&#x09;MIZAT Loubna Kheira
	LEDHEM Meriem 
	CHEMSEDDINE Khadidja

&#x09;BENALI Housna



\## Identified defects



| Defect | Failed gate | Error found in log | Cause |

|---|---|---|---|

| Test 1 | Tests | `test\_defaults\_are\_applied` – expected 10 but got 100 | Incorrect default value for `decay\_solves`. |

| Test 2 | Tests | `test\_a\_missing\_file\_is\_reported\_clearly` – `FileNotFoundError` | Missing file error is not handled properly. |

| Credential | Secret scan | `gitleaks: leaks found: 1` | A secret was detected in the repository. |

| Dependency | Dependency audit | `urllib3 2.0.5` – 18 vulnerabilities | An outdated vulnerable dependency was added. |



\## Conclusion



The CI pipeline detected four intentional defects. The tests, secret scan, and dependency audit failed. Test coverage was 85.58%, which is above the required 70%.

