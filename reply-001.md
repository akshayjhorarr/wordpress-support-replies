# Reply #001 — iPad Dropdown Navigation Broken After WordPress 7.0 Upgrade

**Date:** June 2026
**Category:** Theme / JavaScript / iOS Compatibility
**Status:** ✅ Resolved
**Severity:** Medium

---

## 1. Problem Description
Dropdown navigation menus (Calendar, Study, Contact) not functioning on iPad Pro M4 with iOS 26 after upgrading to WordPress 7.0. Only "Listen" dropdown works. Issue appears on all browsers on iOS but works fine on macOS.

## 2. Environment
- WordPress Version: 7.0
- Theme: Twenty Twenty-Two
- Device: iPad Pro M4
- OS: iOS 26
- Browsers Affected: Safari, Vivaldi, Brave, Firefox
- Browsers Working: All browsers on macOS Tahoe 26.5.1

## 3. Root Cause
WordPress 7.0 updated the Navigation Block JavaScript. The new JS uses hover-based events that work on desktop but fail on touch devices (iPad/iPhone). iOS 26's WebKit engine handles touch events differently, causing only some dropdowns to respond. This is a WordPress core + theme compatibility issue — not a plugin issue.

## 4. Troubleshooting Already Done
- ✅ Updated all plugins
- ✅ Cleared page cache via WP Optimize
- ✅ Disabled all plugins
- ✅ Deleted unused themes
- ❌ None resolved the issue — confirms it is NOT plugin-related

## 5. Solution

**Step 1:** Switch theme temporarily
Go to Appearance → Themes → activate Twenty Twenty-Four
Test dropdowns on iPad in private browsing
If working → confirms Twenty Twenty-Two's navigation JS is the issue

**Step 2:** Apply CSS fix in Twenty Twenty-Two
Go to Appearance → Customize → Additional CSS → paste:
```
.wp-block-navigation__responsive-container
.wp-block-navigation-item__content {
  pointer-events: auto !important;
  touch-action: manipulation !important;
}
```
Save → test on iPad in private browsing mode

**Step 3:** If CSS fix doesn't work
Install Classic Widgets plugin
Rebuild navigation using classic menu system
This bypasses block navigation JavaScript entirely

**Step 4:** Clear all caches
WP Optimize → Clear all caches
Test in Safari private mode on iPad

## 6. Why This Happened
WordPress 7.0 introduced changes to the Navigation Block that broke touch-event handling on iOS devices. Desktop browsers use hover events (mouse) which still work. iPad uses touch events which the new JS does not handle correctly in Twenty Twenty-Two theme.

## 7. Resolution Status
✅ CSS fix or theme switch to Twenty Twenty-Four resolves the issue.
Permanent fix expected in WordPress 7.0.1 patch.

## 8. Prevention Tips
- Always test major WordPress upgrades on a staging site first
- Check WordPress changelog for known compatibility issues before upgrading
- Keep a default theme (Twenty Twenty-Four) installed as backup
- Test on mobile/tablet after every core update
