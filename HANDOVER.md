# Handover Document - Manga Website Restoration

## Project Status: FULLY FUNCTIONAL

All reported issues have been resolved, and the website is now correctly serving manga data and allowing administrative access on the FlokiNet VPS.

## Fixes Implemented

### 1. Manga Display Restoration
- **Problem**: The database used boolean values for section flags (`is_featured`, `is_trending`, etc.), while the code expected the string `"true"`.
- **Fix**: Modified `server/storage.ts` to handle both string (`"true"`, `"1"`) and boolean (`true`) values using Drizzle's `or` and `sql` helpers.
- **Result**: Manga now appear in all sections (Featured, Trending, Popular Today, Latest Updates, Pinned).

### 2. Authentication & CSRF Fix
- **Problem**: Users were getting "Forbidden: invalid csrf token" errors, and the server failed to recognize authenticated sessions.
- **Fix**: 
  - Updated `trust proxy` to `true` in `server/index.ts` and `server/replitAuth.ts` to correctly identify HTTPS requests behind the Nginx reverse proxy.
  - Changed CSRF cookie `sameSite` setting to `"lax"` in `server/routes.ts` to ensure tokens are correctly sent across requests.
- **Result**: Authentication and CSRF protection are now working correctly.

### 3. Admin Account Recovery
- **Problem**: User was unable to log in to the admin account.
- **Fix**: Created and executed a custom recovery script (`scripts/fix-admin.ts`) that reset the admin account with the requested credentials.
- **Credentials**:
  - **Email**: goodvibez072@gmail.com
  - **Password**: Manga@Site2024!Secure99
  - **Role**: owner (full administrative access)

## Deployment & Maintenance

### Restarting the Service
The application is managed by systemd. To restart or check status:
```bash
sudo systemctl restart amurscans
sudo systemctl status amurscans
```

### Viewing Logs
To view real-time application logs:
```bash
sudo journalctl -u amurscans -f
```

### Rebuilding the Application
If you make code changes, you must rebuild the project:
```bash
cd /var/www/amurscans
npm run build
sudo systemctl restart amurscans
```

## Technical Summary
- **VPS IP**: 185.146.232.253
- **Port**: 3000 (Internal), 443 (External via Nginx)
- **Database**: SQLite (`/var/www/amurscans/data/database.db`)
- **Node.js**: v20+

---
**Manus Agent**
*Restoration completed on June 8, 2026*
