# AmourScans VPS Deployment Log

This document tracks the deployment process, issues encountered, and solutions applied during the migration to the FlokiNET VPS, and subsequent cleanup.

## Deployment Details
- **Date**: 2026-06-07 (Initial Deployment), 2026-06-09 (Cleanup)
- **Target VPS**: 185.146.232.253
- **SSH User**: amurscans
- **GitHub Repository**: `goodvibez072-rgb/0000000000000000000000000000000`

---

## 🛠️ Progress Log (Previous Agent)

### 1. Preparation & Repository Sync
- Cloned the repository to inspect the package.
- Identified `amurscans-vps.tar.gz` as the primary deployment artifact.
- Reviewed `DEPLOY_TO_VPS.md` for specific instructions.

### 2. VPS Formatting
- Connected to the VPS.
- Stopped the existing `amurscans.service`.
- Wiped `/var/www/amurscans/` as requested to ensure a clean slate.
- Verified system dependencies (Node.js v20.20.2, Nginx, SQLite3).

### 3. Issue: Replit-Specific Registry in `package-lock.json`
- **Problem**: `npm install` failed on the VPS because `package-lock.json` contained references to `http://package-firewall.replit.local/npm`, which is only accessible within Replit.
- **Solution**: 
    1. Extracted the archive locally.
    2. Ran a global find-and-replace to switch the registry to `https://registry.npmjs.org/`.
    3. Repackaged the project and re-uploaded it to the VPS.

### 4. Issue: Missing Build Tools (`make`)
- **Problem**: `npm install` failed during the compilation of `better-sqlite3` because the `make` command was missing on the VPS.
- **Solution**: Installed `build-essential` on the VPS using `sudo apt-get install -y build-essential`.

### 5. New Deployment (2026-06-07)
- **Status**: In Progress
- **Action**: Deploying the latest version `amurscans-vps.tar.gz` from the GitHub repository.
- **Steps Taken**:
    1. Accessed VPS and verified SSH connectivity.
    2. Backed up the existing `/var/www/amurscans` to `/home/amurscans/amurscans_backup_before_new_deploy`.
    3. Transferred the new `amurscans-vps.tar.gz` to the VPS.
    4. Extracted the new files to `/var/www/amurscans/` using `rsync` for a clean sync.
    5. Triggered `npm install` to update dependencies.

### 6. Issue: `npm install` Timeout
- **Problem**: The `npm install` process is taking a long time, leading to terminal timeouts.
- **Action**: Running `npm install` in a persistent session to ensure completion.
- **Next Steps**: Verify the build, run migrations, and fix the database rendering issue (mangas/chapters not showing).


### 7. Deployment Resumed (2026-06-07)
- **Action**: Resumed deployment after a session handover.
- **Steps Taken**:
    1. Verified the VPS state: the new code was present, but the build was incomplete or not verified.
    2. Ran `npm run build` on the VPS to ensure the frontend and backend were correctly compiled.
    3. Restarted the `amurscans` service.
    4. Verified that the API is responding correctly with manga data from the database.
    5. Confirmed Nginx is running and its configuration is valid.
- **Status**: Successfully deployed and verified.

### 8. Hand-off Finalization
- **Documentation**: All steps have been logged in `DEPLOYMENT_LOG.md`.
- **Repository**: Pushing the updated log to the GitHub repository for future reference.
- **Next Steps**: Monitor the site for any production-specific issues and ensure the admin can log in.

### 9. Final Verification & Domain Status (2026-06-07)
- **Domain**: amurscans.com
- **Cloudflare**: Verified that amurscans.com is resolving to Cloudflare IPs and the site is accessible via HTTPS.
- **Nginx Configuration**: Confirmed Nginx is correctly configured as a reverse proxy for the Node.js backend on port 3000 and serving static files from `/var/www/amurscans/dist/public`.
- **SSL**: SSL is managed on the VPS via Let\'s Encrypt, which is compatible with Cloudflare\'s \'Full\' or \'Full (Strict)\' SSL settings.
- **System Health**: The application is running stably, serving API requests and static content without errors.

---

## 🛠️ Progress Log (Current Agent - 2026-06-09)

### 1. Audit VPS: SSH in and assess current file structure, disk usage, and running services
- **Action**: Connected to VPS via SSH and performed initial audit.
- **Findings**:
    - Initial disk usage in `/home/amurscans` was 3.9GB, with several large backup directories and archive files.
    - The manga website is running from `/var/www/amurscans` (687MB).
    - A Node.js process (PID 717) is running from `/var/www/amurscans`.
    - Nginx is running as a reverse proxy.

### 2. Review GitHub repository to understand the intended website structure and previous agent work
- **Action**: Reviewed `HANDOVER.md`, `DEPLOYMENT_LOG.md`, `PROGRESS.md`, and `agents.md` from the GitHub repository.
- **Findings**:
    - The website was successfully deployed and verified by the previous agent.
    - The primary website content is expected to be in `/var/www/amurscans`.
    - The previous agent created several backups in `/home/amurscans`.

### 3. Plan and execute cleanup: remove backups, old versions, and unnecessary files while preserving the live website and dependencies
- **Action**: Removed unnecessary files and directories from `/home/amurscans` and `/var/www/amurscans`.
- **Details**:
    - Removed backup directories: `/home/amurscans/amurscans_backup_20260605_145837`, `/home/amurscans/amurscans_backup_before_new_deploy`, `/home/amurscans/amurscans_backup_manual`, `/home/amurscans/new_deploy`, `/home/amurscans/tar_fixed_extract`, `/home/amurscans/zip_extract`.
    - Removed temporary files: `/home/amurscans/tmp/*`.
    - Removed backup archive files: `/home/amurscans/amourscans_ready.zip`, `/home/amurscans/amurscans-vps-fixed.tar.gz`, `/home/amurscans/amurscans-vps-new.tar.gz`, `/home/amurscans/amurscans-vps.tar.gz`, `/home/amurscans/database_backup_*.db`.
    - Removed large archives and unnecessary files from the web root: `/var/www/amurscans/amurscans-vps-fixed.tar.gz`, `/var/www/amurscans/amurscans-vps.tar.gz`, `/var/www/amurscans/zipFile.zip`, `/var/www/amurscans/cans -e`.
    - Cleaned npm cache: `npm cache clean --force`.
- **Result**: Disk usage in `/home/amurscans` reduced from 3.9GB to 713MB. `/var/www/amurscans` is now 628MB.

### 4. Verify the website is running correctly after cleanup
- **Action**: Verified the website functionality after cleanup.
- **Result**:
    - The Node.js application is still running and serving content (confirmed by `curl http://localhost:3000/api/sections/featured` returning 4 manga entries).
    - The `amurscans.service` is active and running.

### 5. Document all findings, issues, and actions taken, then push documentation to GitHub
- **Action**: This log is being updated and will be pushed to GitHub.
- **Issues Faced**: None during the cleanup process. The previous agent's documentation was very helpful in understanding the environment and identifying files for removal.

### 6. Extract and Upload Clean Website Zip
- **Action**: Created a clean archive of the website from `/var/www/amurscans` (excluding `node_modules` and `.git` directories) and uploaded it to the GitHub repository.
- **Details**:
    - Initially attempted to use `zip` on the VPS, but it was not installed. Switched to `tar -czf`.
    - Created `clean_current_website.tar.gz` in `/home/amurscans` on the VPS.
    - Downloaded `clean_current_website.tar.gz` to the sandbox.
    - Converted `clean_current_website.tar.gz` to `clean_current_website.zip` in the sandbox.
    - Uploaded `clean_current_website.zip` to the GitHub repository under the name `clean_current_website.zip`.
- **Result**: A clean, dependency-free archive of the website is now available in the GitHub repository.
