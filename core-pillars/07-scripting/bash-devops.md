## 🐚 Core Operating System Automation: Linux Shell Scripting

**Topic:** Bash Automation & System Telemetry | **Kernel Environment:** Ubuntu 24.04 LTS

Shell scripting is the ultimate administrative glue of the Unix architecture. Every high-level automation tool (Ansible, Terraform, Docker) eventually compiles down to execute core kernel-level operations. Mastering Bash scripting allows an SRE to create self-healing routines, parse unstructured system logs, and interface directly with system resources.

---

### 🧱 Script Anatomies & Functional Lifecycles

#### 1. Host Target Discovery

Provision a standard enterprise Linux sandbox layer to run automation scripts safely.

* **Environment:** Ubuntu 24.04 LTS | 20 GB Storage.
* **Privilege Elevation:** `sudo -i` *(Transitions execution context to the system administrative superuser root)*.
* **Shell Handshake:** Identify your shell binary interpreter:

```bash
echo $SHELL  # Typically returns /bin/bash

```



#### 2. Declarative Script Creation & Execution Pipeline

To ensure standard execution across different Linux distributions, scripts must follow a strict lifecycle setup pattern:

```
[ vi health.sh ] ──> [ #!/bin/bash ] ──> [ chmod +x ] ──> [ ./health.sh ]
 (Write Code)        (Define Shebang)    (Grant Exec)       (Run Runtime)

```

* **The Shebang Line (`#!/bin/bash`):** This directive absolute path must sit on line 1, column 1 of your script. It instructs the kernel's program loader to bypass the calling shell and interpret the script's lines specifically using the `/bin/bash` binary engine.
* **The Permission Bit Mod Matrix:** New text files lack execution flags by default. You must explicitly modify the file's access control bits:

```bash
chmod +x health.sh

```



#### 3. Operational System Telemetry Script (`monit.sh`)

This lightweight telemetry harness maps system states using standard terminal control escape sequences:

```bash
#!/bin/bash
# Color definition headers using standard ANSI escape codes
CYAN='\033[0;36m'
GREEN='\033[0;32m'
RESET='\033[0m'

echo -e "${CYAN}=== NODE IDENTIFICATION ===${RESET}"
echo "Hostname: $(hostname)"
echo "IP Routing Table: $(hostname -I)"

echo -e "${CYAN}=== COMPUTE AND STORAGE LIFECYCLE ===${RESET}"
echo -e "${GREEN}[Memory Profile]${RESET}" && free -h
echo -e "${GREEN}[Disk Volumetric Allocations]${RESET}" && df -h /

echo -e "${CYAN}=== RUNTIME METRICS ===${RESET}"
echo "Host Uptime Profile: $(uptime)"

```

#### 4. Advanced Iteration, Control Structures, & Math

* **Loop Traversal Patterns:** Execute time-spaced monitoring loops to sample process data dynamically:

```bash
# Loops exactly three times, holding execution for 30-second cycles
for i in {1..3}; do
    ps -eo pid,ppid,cmd,%mem --sort=-%mem | head -n 5
    sleep 30
done

```


* **Arithmetic Scaling Contexts:** Compute calculations natively using the double-parentheses expansion operator:

```bash
read -p "Enter base allocation number: " base
result=$((base * 1024))
echo "Calculated Metric Value: ${result}MB"
```



---

### 💡 DevOps "Production-Grade" Pro-Tips

* **1. Mandate the "Unofficial Bash Strict Mode":** Plain-vanilla shell scripts fail silently, moving onto the next line even if a previous critical command crashed. Always insert these three safety flags immediately under your shebang line to stop bad scripts instantly:

```bash
set -euo pipefail
```


* `-e`: Exits the script immediately if any command returns a non-zero exit code.
* `-u`: Treats unset/undefined variables as errors and halts execution immediately.
* `-o pipefail`: Prevents hidden errors in pipelines (e.g., `bad_command | grep foo` will now correctly crash the script).


* **2. Ditch Backticks for Command Substitution:** Using backticks (`commands`) to save command output to a variable is legacy behavior. It is hard to read and breaks easily when nested. Use the clean syntax `$()` instead:

```bash
# Avoid this: current_dir=`pwd`
# Do this instead:
current_dir=$(pwd)
```


* **3. Always Quote Your Variable Expansions:** If a variable contains space characters (like a file path or user string), an unquoted reference will split into multiple unexpected arguments, breaking your script. Make it a hard habit to double-quote every variable call:

```bash
# Dangerous: rm $filename
# Safe and Correct:
rm "$filename"
```


* **4. Use `/usr/bin/env` for Portability:** Different Linux distros can store the Bash binary in different directories (e.g., `/bin/bash` vs `/usr/local/bin/bash`). For cross-platform scripts, make your shebang dynamic:

```bash
#!/usr/bin/env bash
```



---

### 🧠 Comprehensive Scenario-Based Questions

**Scenario 1: Silent Pipeline Crashes in Production**

* **Q:** Your script runs a backup pipeline: `tar -czf backup.tar.gz /data | aws s3 cp ...`. The source directory data copy fails due to a missing folder, but the script still returns a success status of `0`, leaving you with an empty backup file. Why?
* **A:** By default, standard shell environments only check the exit status of the *last* command in a pipeline sequence (the AWS upload command). Because the file upload finished successfully (even though the file was empty), the script assumed everything went perfectly. To catch these stealth failures, include `set -o pipefail` at the top of your script.

**Scenario 2: Floating-Point Math Redirection Blocks**

* **Q:** You attempt to calculate a system percentage ratio inside your script using `result=$(( 10 / 3 ))`. Bash throws a configuration failure error or cuts off the fractional numbers completely. How do you process decimal math?
* **A:** Bash's built-in `$(( ))` execution wrapper only supports integer arithmetic. It cannot handle decimal math. To process precise floating-point operations, pipe your calculation into the **Basic Calculator (`bc`)** system tool:

```bash
percentage=$(echo "scale=2; 10 / 3" | bc)
```



**Scenario 3: Multi-Platform Storage Device Variable Drift**

* **Q:** Your storage audit monitoring script runs perfectly on your personal local test Ubuntu server using `awk '{print $5}'` to pull disk utilization details. However, when it runs against a different production target node, the script scrapes random textual headers instead of numerical values. How do you make the logic safer?
* **A:** Text positions inside raw commands like `df -h` drift depending on the operating system version and volume naming styles. To fix this, explicitly point your script to query a fixed volume path target directly:

```bash
disk_utilization=$(df --output=pcent / | tail -n 1 | tr -d '% ')
```



---

### 🎤 Potential Technical Interview Questions

**1. What is the fundamental difference between the commands `sh` and `bash`?**

> **Answer:** `sh` represents the legacy Bourne Shell standard, which features a restricted, minimal command set. `bash` (Bourne-Again Shell) is a modern extension framework that introduces features like arrays, advanced double-bracket testing `[[ ]]`, command histories, and calculation wrappers.

**2. Explain why the shebang line must be the first line of an executable script.**

> **Answer:** When you run a script via `./script.sh`, the Linux kernel looks at the first two bytes of the file. If it detects the magic number characters `#!`, it recognizes the file as an interpreted script. It reads the remaining path on that line to launch the specified binary shell instance, passing the script contents directly into that environment.

**3. What is the operational value of utilizing the double bracket operator `[[ ]]` over the single bracket `[ ]` in conditional statements?**

> **Answer:** The single bracket `[ ]` is a legacy alias pointing to an external file command utility (`test`), which requires strict variable quoting to avoid syntax errors when dealing with empty strings or spaces. The modern double bracket `[[ ]]` is a built-in Bash keyword that natively handles word splitting, prevents globbing issues, and supports regular expression matching without spawning an external process.

**4. How do you safely check if a specific log file exists and is not empty before parsing it in a script?**

> **Answer:** Use a combination of conditional file operators: `-f` to verify the path points to a valid file, and `-s` to confirm its byte size is greater than zero:

```bash
if [[ -f "$log_path" && -s "$log_path" ]]; then echo "Log is valid for processing." fi
```

---

### 🧹 Post-Session Lab Cleanup

Ensure no orphan virtual instances continue racking up active compute charges on your cloud subscription platform. Execute these local terminal commands to clean your testing environment:

```bash
# Clean up your testing files inside your local workspace directory
rm -f filename.sh monit.sh loop_test.sh
```

*Log into your AWS or GCP console interface and terminate your scratch VM testing instance directly from the dashboard view.*