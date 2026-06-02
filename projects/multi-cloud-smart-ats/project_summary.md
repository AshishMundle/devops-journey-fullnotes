📊 Project 6 passes the Image Relevance Test because it is a multi-cloud system deployment. To make the cloud architecture easier to understand at a glance, a dedicated infrastructure topological routing diagram is provided below.

---

## 🚀 Multi-Cloud Smart ATS (Applicant Tracking System) Application

**Topic:** Cross-Cloud AI Integration & App Modernization | **Runtime:** Python 3.14 / Streamlit | **AI Engine:** Google Gemini API (v3.5-flash)

Modern enterprise architectures rarely live inside a single cloud ecosystem. This hands-on project demonstrates a **Multi-Cloud Architecture** pattern: hosting a user-facing Python web application on an **AWS EC2** compute node while offloading its heavy artificial intelligence parsing, natural language processing, and cognitive logic to **Google Cloud Platform (GCP)** via the **Gemini API**.

---

### 🗺️ System Topology & Cross-Cloud Routing

```Architecture Diagram
                  [ Web Browser User ]
                           │
             HTTPS Traffic │ (Inbound Port 8501)
                           ▼
+--------------------------------------------------------+
|                      AMAZON WEB SERVICES (AWS)         |
|  [ Ubuntu 24.04 LTS EC2 Instance (t3.large / 30GB) ]    |
|                           │                            |
|     +---------------------┴----------------------+     |
|     | Isolated Python Sandbox (.venv)            |     |
|     |  - Streamlit Frontend (Port 8501)          |     |
|     |  - PyPDF Resume Parsing Engine             |     |
|     |  - Google Generative AI SDK Client         |     |
|     +---------------------┬----------------------+     |
+---------------------------│----------------------------+
                            │ Secure HTTPS Outbound
                            │ (Authenticated JSON Payload)
                            ▼
+--------------------------------------------------------+
|                    GOOGLE CLOUD PLATFORM (GCP)         |
|   [ GCP API Gateway Gateway Interface ]                |
|                           │                            |
|                           ▼                            |
|   [ Google AI Studio Studio / Gemini API Hub ]         |
|   🤖 Engine: gemini-3.5-flash                          |
+--------------------------------------------------------+

```

---

### 🛠️ Step-by-Step Implementation Guide

#### 1. Compute Layer Provisioning (AWS)

Spin up an isolated Linux compute workspace to act as our core application hosting node.

* **Infrastructure Parameterization:** Launch an AWS EC2 instance running an **Ubuntu 24.04 LTS** base image. Allocate a `t3.large` instance tier (to handle multiple multi-threaded parsing jobs) and attach a 30GB storage volume.
* **Initial Security Group Setting:** For rapid initialization and staging validation, accept open inbound traffic rules (Warning: Ensure this is strict before moving to production).
* **Access Loop:** Connect to your target workspace via secure browser-based SSH or your local terminal client. Elevate your context directly to administrative mode:

```bash
sudo -i

```

#### 2. Local Environment Hardening & Code Ingestion
Isolate package dependencies to avoid breaking system-level libraries.
*   **System Package Preparation:** Synchronize local mirror files and pull down modern developer dependencies:
    
```bash
    apt update && apt install -y python3-pip python3-venv git
```

* **Code Clones & Directory Structuring:** Pull down the core Applicant Tracking System codebase directly from the class repository resource:

```bash
git clone <class-ats-repo-url> && cd application-tracking-system
```

*   **Virtual Sandbox Isolation:** Build and activate an isolated virtual runtime environment to anchor application execution:
```bash
    python3 -m venv .venv
    source .venv/bin/activate
```

* **Package Resolution:** Install the multi-thread web interface application driver along with the Google Cognitive API SDK wrapper:

```bash
pip install -r requirements.txt
pip install google-generativeai pypdf
```


#### 3. Cross-Cloud Authentication Configuration (GCP)
Establish a secure, authenticated channel between AWS and the GCP Gemini API runtime environment.
*   **Key Compilation Profile:** Navigate directly to `console.cloud.google.com`. Head over to **APIs & Services ➡️ Library**, find the **Gemini API** option, and select Enable.
*   **Secret Generation:** Open the **Credentials** settings menu layout and execute **Create Credentials ➡️ API Key**. Securely document the private alphanumeric key.
*   **Injecting Vault Secrets on AWS:** Return to your active AWS terminal window. Streamlit features a secure native configuration structure layout that intercepts secrets securely. Construct an isolated credentials file:
    
```bash
    mkdir -p .streamlit && vim .streamlit/secrets.toml
```

* **Secret Injection Structure:** Paste your key using the exact system structural parameter key name:

```toml
GOOGLE_API_KEY = "AIzaSyYourActualSecureGCPGeminiAlphanumericSecretKey"
```


#### 4. Upgrading Code Engine Configurations (`app.py`)
Ensure your application uses the latest LLM models to take advantage of speed improvements and context window upgrades.
*   **Source Refactoring:** Open the primary codebase runtime file (`vi app.py`) and locate the model identification parameter string block.
*   **Model Translation:** Replace old model references with the faster, more accurate current model generation:
    
```python
    # Locate and modify this string reference inside your app.py file:
    model = genai.GenerativeModel('gemini-3.5-flash')
```

#### 5. Exposing and Interacting with the Pipeline

* **Engine Launch:** Spin up the background webserver framework process thread:

```bash
streamlit run app.py --server.port 8501 --server.address 0.0.0.0
```

*   **Cloud Firewall Adjustments:** Jump back to your AWS Management Console. Edit your EC2 instance's Security Group, add an **Inbound Custom TCP Rule** targeting port `8501`, and set the source pointer filter list to your public IP.
*   **Interface Handshake:** Open a new browser tab and navigate to: `http://<YOUR_EC2_PUBLIC_IP>:8501`.
*   **End-to-End Processing Check:** Upload a sample resume in PDF format, paste an enterprise job description into the text area, and click the analysis button. The app will extract the text, send a secure JSON payload over HTTPS to GCP, and output a detailed match percentage, missing keywords list, and profile summary.
---

### 💡 DevOps "Enterprise-Standard" Pro-Tips

*   **1. Restrict GCP API Key Scopes Natively:** Leaving a raw GCP API key completely unrestricted means that if the key is leaked, malicious actors can use it to spin up expensive services across other GCP APIs. Go into your GCP console, look under API Restrictions, and configure your credential key to **exclusively execute calls matching the Generative Language API**.
*   **2. Secure Your Streamlit Environment Variables:** Storing keys in plain-text `.streamlit/secrets.toml` files is fine for local developer testing, but it's a major security risk for live deployments. For production apps on AWS, use the **AWS Secrets Manager** service or pass your credentials dynamically into system environments using standard OS environment variables:
    
```bash
    export GOOGLE_API_KEY="AIzaSy..."
```

* **3. Implement Asynchronous PDF Extraction Pipelines:** The `pypdf` extraction process runs synchronously by default. If multiple users attempt to upload dense 10-page resumes at the exact same moment, your single Streamlit thread can hang, causing timeout drops. Wrap file reading logic inside **Python Async Coroutines** or implement a task queue worker layer using Celery.
* **4. Prevent App Hanging with Streamlit Process Decoupling:** Launching your app directly via `streamlit run app.py` binds the webserver lifecycle to that specific terminal SSH window. If your connection drops, the app dies. Run your production servers as background processes using detached utility frameworks like `nohup`, `tmux`, or system service daemons:

```bash
nohup streamlit run app.py &
```


---

### 🧠 Comprehensive Scenario-Based Questions

**Scenario 1: Streamlit "Connection Refused" Layout Drops**
*   **Q:** You run your python script successfully on the terminal, and it logs that it's running on port 8501. However, when you paste `http://<YOUR_EC2_PUBLIC_IP>:8501` into your browser, the page times out and says "Connection Refused". What is your step-by-step troubleshooting checklist?
*   **A:** This is a classic networking disconnect. Step 1: Verify the EC2 security group rules actively allow inbound traffic on TCP port `8501`. Step 2: Ensure your firewall rule doesn't have an incorrect single-IP subnet filter (like `/32`) if your public IP shifted. Step 3: Check that Streamlit is binding to the global address `0.0.0.0` (all interfaces) rather than staying trapped inside the container's loopback address `127.0.0.1`.

**Scenario 2: Large PDF Text Extraction Memory Crashes (OOM)**
*   **Q:** Your ATS platform runs smoothly on simple 2-page text resumes. However, when a user uploads a high-resolution, scanned 45-page portfolio resume file, the entire web interface crashes instantly, and your terminal logs an `Out of Memory (OOM) Killed` execution notification. How do you patch this?
*   **A:** Scanned images inside PDFs trigger massive memory allocations during extraction. When these process loops hit your system boundaries, the Linux kernel's Out-Of-Memory (OOM) Killer terminates the python process to protect the operating system.
*   **Resolution:** Implement strict file-size upload limits inside your code (`if file.size > max_bytes`), and swap out basic text parsers for chunked page streaming structures. If your application handles a high volume of scanned images, consider offloading processing jobs to an asynchronous background worker layer.

**Scenario 3: GCP API Quota Limit Bottlenecks (`429 Too Many Requests`)**
*   **Q:** During an enterprise recruiting event, hundreds of students submit resumes simultaneously. The ATS app suddenly stops processing requests, showing an error stating `ResourceExhausted: 429 Resource has been exhausted (e.g. check quota)`. How do you protect application availability?
*   **A:** This means your application has breached your GCP account's free-tier API Rate Limits (Queries Per Minute). 
*   **Resolution:** Implement an exponential backoff retry loop within your Python code using libraries like `tenacity`. This ensures that when the app encounters a 429 error, it waits briefly before retrying instead of completely crashing. For enterprise-scale traffic, go to your Google Cloud Console and request a higher quota limit for the Generative Language API.

---

### 🎤 Potential Technical Interview Questions

**1. What are the key architectural advantages of deploying an application using a Multi-Cloud strategy?**
> **Answer:** Multi-cloud architectures help prevent vendor lock-in, optimize infrastructure costs, and maximize performance by leveraging the unique strengths of different cloud platforms (e.g., using AWS's reliable EC2 compute scaling alongside GCP's specialized, industry-leading AI and analytics tools).

**2. How does the Streamlit configuration manager process keys stored in the `.streamlit/secrets.toml` file?**
> **Answer:** Streamlit automatically parses the `.streamlit/secrets.toml` file during boot configuration phases. It loads those key-value rows directly into a secure memory collection available via `st.secrets` (e.g., `st.secrets["GOOGLE_API_KEY"]`). This keeps your sensitive credentials cleanly decoupled from your source code logic.

**3. Why is using a Python Virtual Environment (`venv`) considered an absolute requirement for multi-cloud app deployments?**
> **Answer:** A virtual environment isolates project dependencies, keeping them separate from your operating system's global package managers. This ensures that updating a system package won't break your app's dependencies, and guarantees that your code runs predictably across developer environments and production servers alike.

**4. How does the `pypdf` module convert a raw PDF object into a string payload that can be parsed by an LLM?**
> **Answer:** The `pypdf` engine opens the file stream, unpacks the document's internal hierarchy, and extracts text content markers from individual page arrays. This unstructured text is aggregated into a clean, single-string Python object block, which is then embedded into a structured JSON prompt payload and sent to the Gemini API via an HTTPS POST request.

---

### 🧹 Post-Session Lab Cleanup
To keep your cloud profiles clean and avoid racking up unexpected charges for your testing environment, make sure to completely purge your lab resources:
*   **AWS:** Log into your AWS Console and completely terminate your `t3.large` Ubuntu EC2 server instance.
*   **GCP:** Open your APIs & Services dashboard and delete your generated Gemini API key to ensure it can never be misused.