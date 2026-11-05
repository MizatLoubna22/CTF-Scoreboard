           **Lab report 2: ContinuousIntegration** 

Names of the Members:  **MIZAT Loubna Kheira**                     Module: **DevOps**  
          **CHEMSEDDINE Khadidja**  
          **LADHEM Meriem Wafaa**  
          **BENALI Hosna**         
Team Name: **ing5-LMKH**                 


Part A: **The pipeline** 

**Exercise1:** 

1) **What does pull\_request give you that push cannot give?**

   pull\_request runs the pipeline before the changes are merged into main. With branch protection, a failing check can block the merge. push only runs after the code is already on the branch, so it is too late to stop a bad change.

2)  **Why does lint run before tests?**

   Lint is faster and finds simple problems (style, syntax). Running it first gives quick feedback and saves time before the slower tests.

3)  **Why is this order more important for a test suite that takes 20 minutes?**

   If lint fails, we know in a few seconds. We do not wait 20 minutes for the tests to find that the code has a problem. This saves time and CI resources

Part B: **Nobody can skip the pipeline** 

**Exercise2:** 

1) **Which command skips a pre-commit hook?**

   git commit \--no-verify

2)  **Who can still put a secret on main that the pipeline detects?**

   A repository administrator, or a user allowed to bypass branch protection, can push directly to main without a pull request. In that case the pipeline runs only after the push, so it detects the secret too late. The rule “Do not allow bypassing the above settings” (include administrators) prevents this. 

Part C: **Why the install step is important** 

**Exercise3:** 

1) **Copy the error line from the log.**

   tests/conftest.py:17: in \<module\>

       from scoreboard.app import create\_app

   E   ModuleNotFoundError: No module named 'scoreboard'

I removed pip install \-e . on the branch test/missing-install and opened PR \#10. The tests step failed. I then closed the pull request and deleted the branch.

2)  **Explain why the tests cannot import scoreboard without the installation line.**

   The project uses a src layout. The scoreboard package is inside the src/ folder, not at the project root. Python only looks in the current directory and in the installed packages, so it cannot find a scoreboard in src/. The command pip install \-e . installs the project in editable mode and adds src/ to Python’s path, so the tests can import it.

3) **Which command ran the installation for you?**

   The following command in the GitHub Actions workflow:

   pip install \-e .

   

Part D: **Four defects you did not write** 

**Exercise 4**

I read the pipeline logs and identified the four intentional defects before opening the code changes.

| Defect | Gate that failed | Name in the log | One-line cause |
| :---: | :---: | :---: | :---: |
| Test 1 | Tests | test\_defaults\_are\_applied | The default value of decay\_solves was changed from 10 to 100\. |
| Test 2 | Tests | test\_a\_missing\_file\_is\_reported\_clearly | The missing file was not handled correctly, causing a FileNotFoundError. |
| Credential | Secret scan | gitleaks: leaks found: 1 | A hardcoded credential was detected in the database file. |
| Dependency | Dependency audit | urllib3 2.0.5 – 18 vulnerabilities | An outdated vulnerable dependency was added to requirements.txt. |

How did the two tests fail?

* Test 1 failed by assertion because the expected value was 10 but the actual value was 100\.  
* Test 2 failed by exception because a FileNotFoundError was raised instead of the expected ChallengeFileError.

**Exercise 5**

I opened the code changes after committing the diagnosis and fixed the four problems.

* Restored the default value of decay\_solves to 10\.  
* Restored the missing file check and ChallengeFileError.  
* Removed the hardcoded credential from db.py.  
* Removed urllib3==2.0.5 from requirements.txt.

1)  **Is the vulnerable package a direct or transitive dependency?**  
   It was a direct dependency because urllib3==2.0.5 was explicitly listed in requirements.txt.  
2)  **How did you find out? Give the command.**  
   I checked the dependency list and used the following command to identify known vulnerabilities:

   **pip-audit \-r requirements.txt**

   The package was directly listed in requirements.txt, which confirmed that it was a direct dependency.

After removing the vulnerable version, the audit returned:

No known vulnerabilities found.

URL : [https\://github.com/MizatLoubna22/CTF-Scoreboard](https://github.com/MizatLoubna22/CTF-Scoreboard)