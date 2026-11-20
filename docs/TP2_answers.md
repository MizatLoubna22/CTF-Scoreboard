# TP2 – Answers

## 1. Which dependency was vulnerable?

The vulnerable dependency was urllib3 version 2.0.5.
It was a direct dependency because it was explicitly listed in requirements.txt.

## 2. Which command was used to identify vulnerabilities?

The following command was used:

pip-audit -r requirements.txt

It checks the project dependencies for known security vulnerabilities.

## 3. What was the result after fixing the issue?

After removing the vulnerable version, the audit returned:

No known vulnerabilities found.
