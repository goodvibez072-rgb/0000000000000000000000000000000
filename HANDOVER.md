# Handover Document - June 8, 2026

## Project Status: FULLY FUNCTIONAL

The AmourScans website on VPS (185.146.232.253) has been restored to full functionality. All major issues reported have been resolved.

### Resolved Issues:
1. **Database Connectivity & Content:**
   - Verified that the SQLite database is correctly located at `/var/www/amurscans/data/database.db`.
   - Restored visibility of all 4 series and their chapters by updating database flags.
   - Fixed corrupted JSON data in the `genres` column.
   - Updated timestamps so all content appears as 'New'.

2. **Authentication & Admin Access:**
   - **Login/Signup Fixed:** Resolved a critical application crash caused by a rate limiting misconfiguration (`trust proxy` setting).
   - **CSRF & Sessions:** Verified that CSRF tokens and session cookies are correctly issued and handled.
   - **Limits Increased:** Increased authentication rate limits to prevent accidental lockouts during administrative work.

3. **Manga & Chapter Functionality:**
   - **Chapter Access:** Set all chapters to 'free' access to ensure they are readable by all users.
   - **Image Serving:** Verified that chapter images are correctly served from the local storage path.

4. **Site Branding & Configuration:**
   - Updated the site name to `AmourScans` in the settings table.
   - Optimized Nginx configuration for better performance and session stability.

### Access Credentials:
- **Admin Username:** `goodvibez072`
- **Admin Email:** `goodvibez072@gmail.com`

### Technical Notes for Next Developer:
- The application uses a local SQLite database for persistence. Do not move this to an ephemeral location.
- Nginx is configured as a reverse proxy to the Node.js app running on port 3000.
- The `trust proxy` setting in `server/index.ts` is set to `loopback` to work correctly with Nginx and `express-rate-limit`.
- All logs can be viewed using `journalctl -u amurscans`.

## Final Verification:
- [x] Homepage loads with all manga series.
- [x] Admin login works.
- [x] User signup works.
- [x] Manga chapters are accessible and images load.
- [x] All API endpoints are stable.
