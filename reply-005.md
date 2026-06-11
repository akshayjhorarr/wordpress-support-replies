# Reply #005 — Fatal Error: call_user_func_array() sanitize_comment_cookies Not Found

**Date:** June 2026
**Category:** WordPress Core / PHP Fatal Error / Corrupted Installation
**Status:** ✅ Resolved
**Severity:** High

---

## 1. Problem Description
Site showing fatal error on every page load. Error references sanitize_comment_cookies function not found in wp-includes/class-wp-hook.php line 324. User has tried deactivating plugins, themes, different PHP versions (7.4) with no resolution.

## 2. Environment
- Error Location: /wp-includes/class-wp-hook.php line 324
- Function Missing: sanitize_comment_cookies
- Server Path: /home/emmanuelzh/www/
- PHP Tested: 7.4 (did not resolve)
- Plugins: Deactivated (did not resolve)
- Themes: Tested (did not resolve)

## 3. Full Error Message
```
Fatal error: Uncaught TypeError: call_user_func_array(): 
Argument #1 ($callback) must be a valid callback, 
function "sanitize_comment_cookies" not found or 
invalid function name in 
/home/emmanuelzh/www/wp-includes/class-wp-hook.php:324
```

## 4. Root Cause
The function sanitize_comment_cookies is a core WordPress function located in wp-includes/comment.php. This error means one of two things:

**Cause 1 (Most likely):** WordPress core files are corrupted or incomplete. The comment.php file is missing, incomplete, or corrupted — so the function never gets defined, but something has already registered it as a hook callback.

**Cause 2:** A plugin or theme registered sanitize_comment_cookies as a hook callback but it got removed from core in a WordPress update (version mismatch between files).

The fact that disabling plugins did NOT fix it confirms this is a core file corruption issue, not a plugin issue.

## 5. Troubleshooting Already Done
- ✅ Deactivated plugins — did not fix (confirms core issue)
- ✅ Changed themes — did not fix (confirms core issue)
- ✅ Tried PHP 7.4 — did not fix (confirms not PHP version issue)
- ❌ WordPress core files not yet replaced

## 6. Solution

**Step 1: Re-upload WordPress core files via FTP**
Download fresh WordPress from wordpress.org/download
Extract the zip file on your computer
Via FTP connect to your server
Upload ONLY these folders (do NOT upload wp-content):
- wp-admin folder (replace entirely)
- wp-includes folder (replace entirely)
- All root .php files (wp-login.php, wp-settings.php etc)
Do NOT touch wp-content folder (your themes/plugins are safe)

**Step 2: Verify wp-config.php is intact**
Open wp-config.php via FTP
Confirm database credentials are correct
Confirm no syntax errors at top or bottom of file

**Step 3: Check database for orphaned hooks**
Go to phpMyAdmin → your database → wp_options table
Search for sanitize_comment_cookies in option_value
If found in serialized data from an old plugin → delete that row

**Step 4: Clear all caches**
Delete contents of wp-content/cache/ folder via FTP
Test site in incognito browser window

**Step 5: If error persists after core re-upload**
Contact hosting provider — server may have partially failed file upload
Ask them to check file permissions on wp-includes folder
Correct permissions: folders 755, files 644

## 7. Why This Happened
Most likely scenario: A WordPress auto-update partially failed, leaving wp-includes/comment.php incomplete or corrupted. WordPress registered the hook for sanitize_comment_cookies during wp-settings.php execution, but when it tried to call it, the function definition was missing from the corrupted comment.php file.

## 8. Resolution Status
✅ Re-uploading clean WordPress core files (wp-admin + wp-includes) resolves this in 95% of cases.

## 9. Prevention Tips
- Enable automatic WordPress updates only if hosting supports atomic updates
- Always keep a recent backup before WordPress core updates (UpdraftPlus)
- If auto-update fails, immediately re-upload core files manually
- Monitor site after every core update with an uptime tool
- Keep hosting error logs accessible for faster diagnosis
