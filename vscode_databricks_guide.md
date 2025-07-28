# VSCode + Databricks Integration Guide

This guide walks you through setting up a local Python development environment in **VSCode** that runs against a **remote Databricks cluster** using **Databricks Connect**. Choose your platform below:

- [WSL (Windows Subsystem for Linux)](#wsl-setup)
- [macOS](#macos-setup)
- [Windows (Native)](#windows-setup)

---

## 🖥️ Platform-Specific Setup

### WSL Setup

#### Prerequisites

1. **Install Ubuntu (WSL)**

   - Install Ubuntu via Microsoft Store
   - Ensure WSL 2 is enabled
   - Verify installation: `wsl --list --verbose`

2. **Install Required VSCode Extensions**

   - `databricks.databricks` (Databricks official extension)
   - `ms-vscode-remote.remote-wsl` (Remote WSL extension)

3. **Install Git and Enable Credential Management**

   - Install Git inside WSL: `sudo apt update && sudo apt install git`
   - Set up Git Credential Manager or SSH for repo access
   - Configure Git: `git config --global user.name "Your Name"` and `git config --global user.email "your.email@example.com"`

#### Python & Virtual Environment Setup

4. **Install Python 3.11+ Inside WSL**

   ```bash
   sudo add-apt-repository ppa:deadsnakes/ppa
   sudo apt update
   sudo apt install python3.11 python3.11-venv python3.11-dev
   ```

5. **Create Virtual Environment in Your Project Root**

   ```bash
   python3.11 -m venv venv
   source venv/bin/activate
   ```

6. **Install Required Python Packages**

   ```bash
   pip install databricks-connect==15.1.0  # Use specific version for stability
   pip install --upgrade pip  # Ensure pip is up to date
   ```

Continue to [Common Configuration](#common-configuration) below.

---

### macOS Setup

#### Prerequisites

1. **Install Homebrew** (if not already installed)

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

2. **Install Required VSCode Extensions**

   - `databricks.databricks` (Databricks official extension)

3. **Install Git** (if not already installed)

   ```bash
   brew install git
   ```
   - Configure Git: `git config --global user.name "Your Name"` and `git config --global user.email "your.email@example.com"`

#### Python & Virtual Environment Setup

4. **Install Python 3.11+**

   ```bash
   brew install python@3.11
   ```

5. **Create Virtual Environment in Your Project Root**

   ```bash
   python3.11 -m venv venv
   source venv/bin/activate
   ```

6. **Install Required Python Packages**

   ```bash
   pip install databricks-connect==15.1.0  # Use specific version for stability
   pip install --upgrade pip  # Ensure pip is up to date
   ```

Continue to [Common Configuration](#common-configuration) below.

---

### Windows Setup

#### Prerequisites

1. **Install Python 3.11+**

   - Download from [python.org](https://www.python.org/downloads/)
   - During installation, check "Add Python to PATH"
   - Verify installation: Open Command Prompt and run `python --version`

2. **Install Required VSCode Extensions**

   - `databricks.databricks` (Databricks official extension)

3. **Install Git for Windows**

   - Download from [git-scm.com](https://git-scm.com/download/win)
   - During installation, select "Git from the command line and also from 3rd-party software"
   - Configure Git: `git config --global user.name "Your Name"` and `git config --global user.email "your.email@example.com"`

#### Python & Virtual Environment Setup

4. **Create Virtual Environment in Your Project Root**

   Open PowerShell or Command Prompt in your project directory:

   ```powershell
   python -m venv venv
   ```

5. **Activate Virtual Environment**

   PowerShell:
   ```powershell
   .\venv\Scripts\Activate.ps1
   ```

   Command Prompt:
   ```cmd
   .\venv\Scripts\activate.bat
   ```

   > **Note:** If you get an execution policy error in PowerShell, run: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`

6. **Install Required Python Packages**

   ```powershell
   pip install databricks-connect==15.1.0
   pip install --upgrade pip
   ```

Continue to [Common Configuration](#common-configuration) below.

---

## 🔧 Common Configuration

### Clone Your Repository

```bash
git clone <your-repo-url>
cd your-repo
git remote add upstream <upstream-url>  # if using an upstream
git branch --set-upstream-to=origin/your-branch
```

### 🔐 Authentication and Cluster Config

1. **Generate a Personal Access Token (PAT)**

   - In Databricks: *User Settings > Access Tokens > Generate New Token*

2. **Create `.databrickscfg` File**

   Create the file in your home directory:
   - **WSL/macOS**: `~/.databrickscfg`
   - **Windows**: `%USERPROFILE%\.databrickscfg`

   ```ini
   [DEFAULT]
   host = https://<your-workspace>.databricks.net
   token = <your-pat-token>
   cluster_id = <your-cluster-id>
   jobs-api-version = 2.1
   ```

> **Tip:** Find your `cluster_id` in the Databricks UI by clicking on your cluster. The URL will show: `https://<workspace>/clusters/<cluster-id>/configuration`

### 🧪 Writing and Running Python Code

1. **Initialize SparkSession Correctly**

> Databricks Connect v15+ uses a new builder:

```python
from databricks.connect import DatabricksSession

spark = DatabricksSession.builder.getOrCreate()
df = spark.read.table("my_catalog.my_schema.my_table").limit(10)
df.show()
```

2. **Run Your Script Like a Normal Python File**

**WSL/macOS:**
```bash
source venv/bin/activate
python test.py
```

**Windows:**
```powershell
.\venv\Scripts\Activate.ps1  # or activate.bat for Command Prompt
python test.py
```

---

## 💡 Additional Tips & Troubleshooting

- **Avoid pushing .deb installers or large files**: Add to `.gitignore`

  ```bash
  echo '*.deb' >> .gitignore
  ```

- **Environment Variable Fallback**

  **WSL/macOS:**
  ```bash
  export DATABRICKS_HOST=https://<workspace>
  export DATABRICKS_TOKEN=<token>
  export DATABRICKS_CLUSTER_ID=<cluster-id>
  ```

  **Windows (PowerShell):**
  ```powershell
  $env:DATABRICKS_HOST="https://<workspace>"
  $env:DATABRICKS_TOKEN="<token>"
  $env:DATABRICKS_CLUSTER_ID="<cluster-id>"
  ```

- **Test Databricks Connect**

  ```bash
  databricks-connect test
  ```

- **Common Issues:**
  - If `databricks-connect test` fails, ensure your cluster is running
  - For "Invalid access token" errors, regenerate your PAT
  - If imports fail, check Python version compatibility with `python --version`
  - **Windows:** If you see SSL certificate errors, you may need to configure corporate proxy settings
  - **macOS:** If you encounter permission issues, ensure you're not using the system Python

---

## 🎉 You're Ready!

You can now:

- Develop Spark code locally
- Run against your Databricks cluster
- Use VSCode's debugger, Git integration, and Python tools seamlessly

---

## 📋 Quick Checklist

**All Platforms:**
- [ ] VSCode with Databricks extension installed
- [ ] Git configured
- [ ] Python 3.11+ installed
- [ ] Virtual environment created and activated
- [ ] Databricks Connect installed
- [ ] `.databrickscfg` file created with valid credentials
- [ ] Connection tested successfully

**Platform-Specific:**
- [ ] **WSL**: WSL 2 with Ubuntu installed, Remote-WSL extension
- [ ] **macOS**: Homebrew installed
- [ ] **Windows**: Python added to PATH, execution policy configured (if using PowerShell)