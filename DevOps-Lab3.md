           **Lab report 3: Containers** 

Names of the Members:  **MIZAT Loubna Kheira**                     Module: **DevOps**  
          **CHEMSEDDINE Khadidja**  
          **LADHEM Meriem Wafaa**  
          **BENALI Hosna**      
     
Team Name: **ing5-LMKH**                 


Part A: **A multi-stage image**  

**Exercise1:** 

1) **Give the size of scoreboard:local and the size of python:3.12-slim** 

   scoreboard:local: **181 MB**

   python:3.12-slim: approximately **119 MB**

   

2)  **Name two things that are in the build stage but not in the runtime image.**   
* The build tools used by pip install (pip and the build backend) and the build cache.  
* The source and build files: requirements.txt, pyproject.toml, README.md and the src/ folder. Only the finished virtual environment /opt/venv is copied into the runtime image.


3) **For each of these two things, explain why its absence makes the image more secure.**  
* Build tools: an attacker who gets into the container cannot use them to install or build new software, and there are fewer packages that could contain vulnerabilities.  
* Source and build files: the attacker has no project files to read or modify, such as the dependency list or the unpacked sources, so the image exposes less information and has a smaller attack surface.


  

4) **COPY requirements.txt comes before COPY src/. Why? Use what the second build printed.** 

   Because Docker caches layers and reuses a layer when its inputs have not changed. Dependencies change rarely and source code changes often. In the second build, after I changed one byte of app.py, the steps up to RUN pip install \-r requirements.txt were printed as CACHED. Only COPY src/ and the steps after it were run again. If src/ were copied first, every code change would reinstall all dependencies. 

   

Part B: **Run it with the minimum it needs** 

**Exercise2:** 

1) **Which mount makes this possible?** 

   The named volume sbdata, mounted at /app/data, makes it possible to save application data even when the root filesystem is read-only.

2) **Why is it a named volume (sbdata) and not the host folder \$PWD/data?** 

   A named volume is managed by Docker and keeps data persistent, separate from the project folder. It is initialised from the image’s /app/data, which belongs to uid 10001, so the non-root user can write to it. A host folder keeps the host user’s ownership, so the container user would get “permission denied”. A host folder also ties the container to the host’s file layout and exposes host files. 

3) **Imagine that an attacker can run code inside the container. Choose two other options of the docker run command. For each one, say what the attacker can no longer do.**   
* \--cap-drop ALL:the attacker has no Linux capabilities. They cannot open raw sockets (sniffing, ARP spoofing), change file ownership, change user IDs or mount filesystems.   
* \--security-opt no-new-privileges: the attacker cannot gain more privileges, for example by running a setuid binary. Privilege escalation inside the container is blocked.


  

   

Part C: **Who receives SIGTERM?**  

**Exercise3:** 

1) **Give both stop times.** 

   Direct Uvicorn process: approximately **1.25 seconds**.

   Shell as PID 1: approximately **10.45 seconds**.

2) **Explain the difference. Use the words PID 1 and SIGTERM.** 

   docker stop sends SIGTERM to PID 1\. When Uvicorn is PID 1, it receives SIGTERM, shuts down gracefully and exits in about 1.25 s. When the shell is PID 1, the Python process is only a child. PID 1 has no default signal handler, and the shell does not forward SIGTERM, so the signal is ignored. Docker waits 10 s and then sends SIGKILL (10.45 s). 

   

3) **A rolling update replaces twenty replicas, one after the other. What does this difference cost during such an update?** 

   Each container takes about 9 s longer to stop, so twenty replicas add about 3 minutes (20 × 9 s) to the update. Each container is also killed by SIGKILL, so in-flight requests are cut and there is no clean shutdown, which can cause errors for users. 

Part D: **Scan the image**  

**Exercise 4**

1) **How many findings are there, and in which packages?** 

   There are 2 HIGH findings and 0 CRITICAL findings. Both are in the Debian package zlib1g (CVE-2026-76844 and CVE-2026-85091), which comes from the base image. The Python packages have 0 findings. 

2) **In lab sheet 2, pip-audit found nothing for the same application. Explain the difference.** 

   pip-audit checks Python packages, while Trivy also checks operating system packages inside the Docker image. This is why Trivy can find vulnerabilities that pip-audit does not detect.

   

3) **In two lines: why is the scanner itself pinned (a fixed version)?** 

   Pinning the scanner version makes results reproducible: the same image gives the same result.

   A new scanner version or database could change results without any change in our code, and a pinned version is only upgraded deliberately. 

Part E: **Build and publish from the pipeline**  

**Exercise 5**

1) **The commit identifier travels from Git to the running process. Name these different steps.**   
1. Git creates a commit with a unique SHA.  
2. GitHub Actions exposes it as github.sha.  
3. The workflow passes it to Docker with build-args: BUILD\_SHA=\${{ github.sha }}.  
4. The Dockerfile receives it with ARG BUILD\_SHA and stores it with ENV BUILD\_SHA.  
5. The application reads the environment variable and returns it in the /version endpoint (checked: commit \= 4e1b3ad…, the same as the image tag).  
     
2) **In a later lab sheet, two versions of the application run at the same time. Imagine that both images have the tag latest. Give one concrete problem** 

   A tag can point to only one image at a time, so two versions cannot both be the latest. Some nodes may keep an old latest and others pull the new one, so replicas run different versions under the same name. We cannot tell which version is running from the image name, and we cannot roll back reliably. 

URL : [https\://github.com/MizatLoubna22/CTF-Scoreboard](https://github.com/MizatLoubna22/CTF-Scoreboard)