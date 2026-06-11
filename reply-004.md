# Reply #004 — Gutenberg Split Screen Editor / Meta Boxes Not Scrollable

**Date:** June 2026
**Category:** WordPress Editor / Gutenberg / UX
**Status:** ✅ Resolved
**Severity:** Low

---

## 1. Problem Description
User frustrated with Gutenberg block editor's split screen layout where meta boxes require manual pulling up. Editor no longer scrollable as it was previously. Editor completely unusable on mobile/phone. User has tried code snippets found online with no success and cannot find a plugin fix.

## 2. Environment
- WordPress Version: Recent (Gutenberg block editor)
- Device Issues: Not working on mobile phone at all
- Previous behavior: Editor was scrollable
- Current behavior: Split screen layout with fixed meta box panel

## 3. Root Cause
WordPress introduced a new split-screen editing mode in Gutenberg where the canvas (editing area) and settings panel are separated. This was a deliberate UX change by the Gutenberg team. The editor was not designed for mobile use — WordPress admin panel officially requires desktop browser. The meta boxes panel requires manual toggle because Gutenberg separates block settings from post settings.

## 4. Solution

**Option 1: Disable Gutenberg — use Classic Editor (Easiest)**
Install Classic Editor plugin from WordPress repository
Go to Settings → Writing → select Classic Editor as default
This restores the fully scrollable, mobile-friendly editor

**Option 2: Use Distraction Free mode in Gutenberg**
In Gutenberg editor → top right → click the three-dot menu (⋮)
Select "Fullscreen mode" → toggle off
This gives more space and removes the split layout

**Option 3: Fix meta boxes visibility**
In Gutenberg → top right → click three-dot menu (⋮)
Select "Preferences" → "Panels"
Enable the panels you need to always show

**Option 4: Use WordPress mobile app for phone editing**
Download WordPress app (iOS/Android)
This is purpose-built for mobile editing
Far better experience than browser on phone

**Option 5: Add this code to functions.php to disable split view**
```php
add_action( 'enqueue_block_editor_assets', function() {
    wp_add_inline_script(
        'wp-blocks',
        'wp.domReady( function() {
            wp.data.dispatch("core/edit-post").toggleFeature("fullscreenMode");
        });'
    );
});
```

## 5. Why This Happened
Gutenberg team redesigned the editor interface to separate content editing from settings management. This was intended to reduce visual clutter on large screens but created a worse experience on smaller screens and for users accustomed to the old layout. WordPress does not officially support admin panel on mobile browsers.

## 6. Resolution Status
✅ Classic Editor plugin immediately resolves all issues including mobile editing.
✅ Distraction Free mode helps with desktop split screen frustration.

## 7. Prevention Tips
- Use WordPress mobile app for editing on phone — do not use browser
- Classic Editor plugin is officially supported until at least 2025
- Before major WordPress updates, check Gutenberg changelog for UI changes
- Use staging site to test new editor versions before updating live site
