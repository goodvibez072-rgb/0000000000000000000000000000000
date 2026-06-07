# AmourScans VPS Deployment Log

This document tracks the deployment process, issues encountered, and solutions applied during the migration to the FlokiNET VPS.

## Deployment Details
- **Date**: 2026-06-07
- **Target VPS**: 185.146.232.253
- **SSH User**: amurscans
- **GitHub Repository**: `goodvibez072-rgb/0000000000000000000000000000000`

---

## 🛠️ Progress Log

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
- **SSL**: SSL is managed on the VPS via Let's Encrypt, which is compatible with Cloudflare's 'Full' or 'Full (Strict)' SSL settings.
- **System Health**: The application is running stably, serving API requests and static content without errors.

