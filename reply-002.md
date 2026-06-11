# Reply #002 — fetchpriority="high" Being Removed from Featured Image

**Date:** June 2026
**Category:** WordPress Core / Image Attributes / Performance
**Status:** ✅ Resolved
**Severity:** Medium

---

## 1. Problem Description
Developer adding fetchpriority="high" to featured image via wp_get_attachment_image_attributes filter. The attribute gets removed from final HTML even though loading="eager" and data-test="YES" from the same filter are retained correctly.

## 2. Environment
- Filter Used: wp_get_attachment_image_attributes
- Priority: 999
- Theme: Astra Pro
- Plugin: WP Rocket
- Other custom attributes: Working correctly
- Only fetchpriority: Being stripped

## 3. Root Cause
WordPress core itself sanitizes the fetchpriority attribute after wp_get_attachment_image_attributes runs. Specifically, WordPress uses wp_img_tag_add_loading_optimization_attrs() function which controls fetchpriority internally. It intentionally removes or overrides fetchpriority set via filters to maintain its own loading optimization logic. This is a WordPress core behavior introduced in WP 6.3+.

## 4. Troubleshooting Already Done
- ✅ Contacted Astra Pro support — not the cause
- ✅ Contacted WP Rocket support — not the cause
- ✅ Tested with custom attribute name (fetchpriority-test) — also removed
- ❌ wp_get_attachment_image_attributes filter alone is not sufficient

## 5. Solution

**Step 1:** Use wp_get_attachment_image filter instead
This filter runs after WordPress core optimization and gives access to the final HTML string:

```php
add_filter( 'wp_get_attachment_image', function( $html, $attachment_id ) {
    if ( has_post_thumbnail() && get_post_thumbnail_id() === $attachment_id ) {
        $html = str_replace(
            'fetchpriority="low"',
            'fetchpriority="high"',
            $html
        );
        if ( strpos( $html, 'fetchpriority' ) === false ) {
            $html = str_replace(
                '<img ',
                '<img fetchpriority="high" ',
                $html
            );
        }
    }
    return $html;
}, 10, 2 );
```

**Step 2:** Or disable WordPress core optimization for featured image
```php
add_filter( 'wp_img_tag_add_loading_optimization_attrs', function( $value, $image ) {
    if ( strpos( $image, 'wp-post-image' ) !== false ) {
        return false;
    }
    return $value;
}, 10, 2 );
```
Then your original filter will work correctly.

**Step 3:** Clear WP Rocket cache after applying fix
WP Rocket → Dashboard → Clear Cache
Test in incognito window and inspect element to verify fetchpriority="high" appears

## 6. Why This Happened
WordPress 6.3 introduced automated loading optimization that controls fetchpriority internally. It intentionally overrides manually set values to prevent conflicts. wp_get_attachment_image_attributes runs before this optimization, so values set there get overwritten.

## 7. Resolution Status
✅ Using wp_get_attachment_image filter (runs after core optimization) resolves the issue permanently.

## 8. Prevention Tips
- Check WordPress developer docs when attributes are being stripped unexpectedly
- WordPress 6.3+ manages fetchpriority automatically — understand this before overriding
- Always test performance attribute changes with browser DevTools → Network tab
- Clear all caches after making filter changes
