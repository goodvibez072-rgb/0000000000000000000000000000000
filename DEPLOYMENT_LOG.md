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

### 5. Deployment Continuation
- Proceeding with `npm install` and `npm run build` on the VPS.
