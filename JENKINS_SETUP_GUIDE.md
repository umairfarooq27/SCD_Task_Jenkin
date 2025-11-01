# Jenkins Setup and Configuration Guide

## Part 1: Jenkins Installation (If Not Already Installed)

### Step 1: Download Jenkins
1. Go to [https://www.jenkins.io/download/](https://www.jenkins.io/download/)
2. Download the **Windows** version (jenkins.msi installer)
3. Or download the Generic Java package (.war file) if you prefer

### Step 2: Install Jenkins (Windows)
1. Run the downloaded `jenkins.msi` installer
2. Follow the installation wizard
3. Jenkins will install to `C:\Program Files\Jenkins` by default
4. Jenkins service will start automatically

### Step 3: Initial Jenkins Setup
1. Open your web browser and navigate to: `http://localhost:8080`
2. You'll see an "Unlock Jenkins" screen
3. Find the initial admin password in one of these locations:
   - `C:\Program Files\Jenkins\secrets\initialAdminPassword`
   - Or check the Jenkins service logs
4. Copy the password and paste it into the "Administrator password" field
5. Click **Continue**

### Step 4: Install Suggested Plugins
1. Select **"Install suggested plugins"**
2. Wait for all plugins to install (this may take a few minutes)
3. Plugins needed include:
   - Git plugin
   - GitHub plugin
   - Pipeline plugin
   - Blue Ocean (optional, but nice UI)

### Step 5: Create Admin User
1. Fill in the admin user details:
   - Username: `admin` (or your choice)
   - Password: (create a strong password)
   - Full name: (optional)
   - Email: (optional)
2. Click **Save and Continue**

### Step 6: Configure Jenkins URL
1. Keep the default: `http://localhost:8080/`
2. Or change it if you have a different setup
3. Click **Save and Finish**
4. Click **Start using Jenkins**

---

## Part 2: Installing Required Plugins

### Step 1: Install Additional Plugins
1. In Jenkins dashboard, click **Manage Jenkins** (left sidebar)
2. Click **Manage Plugins**
3. Go to the **Available** tab
4. Search for and install these plugins (if not already installed):
   - **Git plugin** ✅ (usually installed by default)
   - **GitHub plugin** ✅ (for webhook integration)
   - **Pipeline** ✅ (for Jenkinsfile support)
   - **Pipeline: Stage View** ✅
   - **Email Extension Plugin** (optional, for real email notifications)
5. Check the boxes and click **Install without restart**
6. Wait for installation to complete
7. Restart Jenkins if prompted

---

## Part 3: Configure Git (If Not Already Done)

### Step 1: Verify Git Installation
```powershell
git --version
```

### Step 2: Configure Git in Jenkins
1. Go to **Manage Jenkins** → **Global Tool Configuration**
2. Scroll down to **Git**
3. If Git is not auto-detected, add it manually:
   - Click **Add Git**
   - Name: `Default`
   - Path to Git executable: Usually `C:\Program Files\Git\bin\git.exe` or `git`
   - Click **Save**

---

## Part 4: Task 1 - Basic CI Pipeline (NodeApp-CI)

### Step 1: Create New Pipeline Project
1. In Jenkins dashboard, click **New Item** (left sidebar)
2. Enter item name: `NodeApp-CI`
3. Select **Pipeline** (not Freestyle project)
4. Click **OK**

### Step 2: Configure Pipeline
1. Scroll down to **Pipeline** section
2. **Definition**: Select **Pipeline script from SCM**
3. **SCM**: Select **Git**
4. **Repository URL**: `https://github.com/umairfarooq27/SCD_Task_Jenkin.git`
5. **Branch Specifier**: `*/main`
6. **Script Path**: `Jenkinsfile` (default)
7. Click **Save**

### Step 3: Run the Build
1. Click **Build Now** on the project page
2. Wait for the build to complete
3. Click on the build number (#1) in the left sidebar
4. Click **Console Output** to see the execution logs
5. You should see:
   - ✅ Checkout Code
   - ✅ Install Dependencies (npm install)
   - ✅ Run Tests (npm test)
   - ✅ Build successful message

---

## Part 5: Task 2 - Extended Pipeline (NodeApp-CI-Extended)

### Step 1: Create New Pipeline Project
1. Click **New Item**
2. Name: `NodeApp-CI-Extended`
3. Select **Pipeline**
4. Click **OK**

### Step 2: Configure Pipeline (Same as Task 1)
1. **Pipeline** → **Definition**: Pipeline script from SCM
2. **SCM**: Git
3. **Repository URL**: `https://github.com/umairfarooq27/SCD_Task_Jenkin.git`
4. **Branch**: `*/main`
5. **Script Path**: `Jenkinsfile`
6. Click **Save**

### Step 3: Run the Build
1. Click **Build Now**
2. Check the build output - you should see:
   - ✅ All stages from Task 1
   - ✅ Linting stage
   - ✅ Archive Artifacts stage
   - ✅ Email notification messages (simulated)

### Step 4: View Artifacts
1. After build completes, go to the build page
2. You'll see **Build Artifacts** section
3. Download the archived files

---

## Part 6: Task 3 - GitHub Webhook Integration

### Step 1: Get Your Jenkins URL
1. Note your Jenkins URL:
   - If running locally: `http://localhost:8080`
   - If on a server: `http://your-ip:8080` or `http://your-domain:8080`
2. For webhooks to work from GitHub, Jenkins must be accessible:
   - **Option A**: Use ngrok to expose localhost (for testing)
   - **Option B**: Deploy Jenkins on a public server
   - **Option C**: Use GitHub Actions (alternative approach)

### Step 2: Configure Jenkins to Accept Webhooks
1. Go to **Manage Jenkins** → **Configure System**
2. Scroll down to **GitHub** section
3. Click **Advanced...**
4. Check **"Specify another hook URL"** if needed
5. Add GitHub Server (optional):
   - Click **Add GitHub Server**
   - Name: `GitHub`
   - API URL: `https://api.github.com`
   - Click **Save**

### Step 3: Enable Webhook Trigger in Pipeline
1. Go to your `NodeApp-CI` project
2. Click **Configure**
3. Under **Build Triggers**, check:
   - ✅ **GitHub hook trigger for GITScm polling**
4. Click **Save**

### Step 4: Configure GitHub Webhook
1. Go to your GitHub repository: `https://github.com/umairfarooq27/SCD_Task_Jenkin`
2. Click **Settings** → **Webhooks** → **Add webhook**
3. Fill in the webhook:
   - **Payload URL**: 
     - Local (if using ngrok): `http://your-ngrok-url.ngrok.io/github-webhook/`
     - Or use a public Jenkins instance: `http://your-jenkins-url:8080/github-webhook/`
   - **Content type**: `application/json`
   - **Secret**: (leave empty for now, or generate one)
   - **Which events**: Select **"Just the push event"**
   - ✅ **Active**
4. Click **Add webhook**

### Step 5: Test Webhook
1. Make a small change to any file (e.g., README.md)
2. Commit and push:
   ```bash
   git add .
   git commit -m "Test webhook trigger"
   git push origin main
   ```
3. Go back to Jenkins
4. You should see a new build automatically triggered
5. Check the console output - you should see:
   - "Build #X triggered by GitHub Webhook at [timestamp]"
   - "Triggered by GitHub Webhook"

---

## Part 7: Task 4 - Multi-Branch Pipeline

### Step 1: Create Multi-Branch Pipeline
1. In Jenkins, click **New Item**
2. Name: `NodeApp-MultiEnv-Pipeline`
3. Select **Multibranch Pipeline**
4. Click **OK**

### Step 2: Configure Multi-Branch Pipeline
1. **Branch Sources** → Click **Add source** → Select **GitHub**
   - **Credentials**: Add if needed (or use anonymous)
   - **Repository HTTPS URL**: `https://github.com/umairfarooq27/SCD_Task_Jenkin.git`
   - Or use **Git** option:
     - **Project Repository**: `https://github.com/umairfarooq27/SCD_Task_Jenkin.git`
2. **Build Configuration**:
   - **Mode**: By Jenkinsfile
   - **Script Path**: `Jenkinsfile`
3. **Scan Multibranch Pipeline Triggers**:
   - ✅ Check **"Periodically if not otherwise run"**
   - Interval: Every 1 day (or as needed)
   - ✅ Check **"Trigger builds remotely"** (for webhook)
4. Click **Save**

### Step 3: Create Different Branches (If Not Exists)
1. Create a `dev` branch:
   ```bash
   git checkout -b dev
   git push origin dev
   ```
2. Create a feature branch:
   ```bash
   git checkout -b feature/test
   git push origin feature/test
   ```

### Step 4: Scan Branches
1. In the multi-branch pipeline project, click **Scan Multibranch Pipeline Now**
2. Jenkins will discover all branches with Jenkinsfile
3. You'll see separate builds for each branch:
   - `main` → Production deployment message
   - `dev` → Staging deployment message
   - `feature/*` → Deployment skipped message

### Step 5: Verify Parallel Execution
1. Click on any branch build
2. Check the console output
3. You should see:
   - ✅ Parallel execution of "Unit Tests" and "Linting"
   - ✅ Conditional deployment based on branch
   - ✅ Artifact archiving
   - ✅ Success notification with branch name

---

## Part 8: Verify All Tasks

### Task 1 Checklist ✅
- [ ] Pipeline project `NodeApp-CI` created
- [ ] Build executes successfully
- [ ] Console shows: Checkout, Install, Test stages
- [ ] Build status: Success ✅

### Task 2 Checklist ✅
- [ ] Pipeline project `NodeApp-CI-Extended` created
- [ ] Build includes linting stage
- [ ] Artifacts are archived and downloadable
- [ ] Email notification message appears

### Task 3 Checklist ✅
- [ ] Webhook trigger enabled in Jenkins
- [ ] GitHub webhook configured
- [ ] Push to repository triggers automatic build
- [ ] Console shows "Triggered by GitHub Webhook"
- [ ] Build number and timestamp displayed

### Task 4 Checklist ✅
- [ ] Multibranch pipeline created
- [ ] Multiple branches detected (main, dev, feature)
- [ ] Parallel test execution working
- [ ] Conditional deployment messages shown:
  - Main → Production
  - Dev → Staging
  - Feature → Skipped
- [ ] Artifacts archived per branch
- [ ] Success notification with branch name

---

## Troubleshooting

### Jenkins Not Starting
```powershell
# Check Jenkins service status
Get-Service Jenkins

# Start Jenkins service
Start-Service Jenkins

# Or restart
Restart-Service Jenkins
```

### Git Not Found
- Install Git for Windows: [https://git-scm.com/download/win](https://git-scm.com/download/win)
- Add Git to PATH during installation
- Restart Jenkins after Git installation

### Webhook Not Working (Local Jenkins)
- Use **ngrok** to expose localhost:
  1. Download ngrok: [https://ngrok.com/download](https://ngrok.com/download)
  2. Run: `ngrok http 8080`
  3. Use the ngrok URL in GitHub webhook settings
  4. Update Jenkins URL in GitHub webhook to use ngrok URL

### Build Fails at npm install
- Ensure Node.js is installed: `node --version`
- Install Node.js: [https://nodejs.org/](https://nodejs.org/)
- Restart Jenkins after Node.js installation

### Permission Issues
- Run Jenkins service as Administrator
- Or configure file permissions for Jenkins workspace folder

---

## Quick Reference: Jenkins URLs

- **Dashboard**: `http://localhost:8080`
- **Manage Jenkins**: `http://localhost:8080/manage`
- **New Item**: `http://localhost:8080/view/all/newJob`
- **Plugin Manager**: `http://localhost:8080/pluginManager/available`
- **Webhook Endpoint**: `http://localhost:8080/github-webhook/`

---

## Summary

All 4 tasks are now configured:

1. **Task 1** - Basic CI pipeline with checkout, install, test
2. **Task 2** - Extended pipeline with artifacts and notifications  
3. **Task 3** - Webhook integration for automatic builds
4. **Task 4** - Multi-branch pipeline with parallel tests and conditional deployment

Your Jenkinsfile in the repository already contains all the necessary configurations for all tasks!

