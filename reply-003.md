# Reply #003 — Adding Custom Classes to wp_nav_menu Programmatically

**Date:** June 2026
**Category:** WordPress Navigation / Custom Walker / Theme Development
**Status:** ✅ Resolved
**Severity:** Low

---

## 1. Problem Description
Developer wants to add custom CSS classes (dropdownitem, nav-dropdown, dropdown) to wp_nav_menu items programmatically to match a specific HTML structure with nested dropdown menus.

## 2. Environment
- Function: wp_nav_menu()
- Required Classes: dropdownitem, nav-dropdown, dropdown
- Structure: Nested multi-level dropdown navigation
- Method needed: Programmatic via custom Walker class

## 3. Root Cause
WordPress default wp_nav_menu() does not add custom classes to sub-menu wrappers or parent items automatically. A custom Walker class is required to override the default HTML output and inject custom classes at the correct points in the menu structure.

## 4. Solution

**Step 1:** Create a Custom Walker class in functions.php

```php
class Custom_Nav_Walker extends Walker_Nav_Menu {

    // Add custom class to parent <li> that has children
    function start_el( &$output, $item, $depth = 0, $args = array(), $id = 0 ) {
        $classes = empty( $item->classes ) ? array() : (array) $item->classes;
        
        // Add dropdownitem class if item has children
        if ( in_array( 'menu-item-has-children', $classes ) ) {
            $classes[] = 'dropdownitem';
        }
        
        $class_names = join( ' ', apply_filters( 'nav_menu_css_class', array_filter( $classes ), $item, $args ) );
        $class_names = ' class="item ' . esc_attr( $class_names ) . '"';
        
        $output .= '<li' . $class_names . '>';
        
        $atts = array();
        $atts['href'] = ! empty( $item->url ) ? $item->url : '';
        
        // Add nav-dropdown class to links that have children
        if ( in_array( 'menu-item-has-children', (array) $item->classes ) ) {
            $atts['class'] = 'nav-dropdown';
        }
        
        $atts = apply_filters( 'nav_menu_link_attributes', $atts, $item, $args );
        $attributes = '';
        foreach ( $atts as $attr => $value ) {
            if ( ! empty( $value ) ) {
                $attributes .= ' ' . $attr . '="' . esc_attr( $value ) . '"';
            }
        }
        
        $output .= '<a' . $attributes . '>' . $item->title . '</a>';
    }

    // Add custom dropdown div wrapper for sub-menus
    function start_lvl( &$output, $depth = 0, $args = array() ) {
        $output .= '<div class="dropdown"><ul>';
    }

    function end_lvl( &$output, $depth = 0, $args = array() ) {
        $output .= '</ul></div>';
    }
}
```

**Step 2:** Use the custom Walker in wp_nav_menu call

```php
wp_nav_menu( array(
    'theme_location' => 'primary',
    'menu_class'     => 'mnav m-lg-auto',
    'container'      => false,
    'walker'         => new Custom_Nav_Walker(),
) );
```

**Step 3:** This will output the exact structure requested:
```html
<ul class="mnav m-lg-auto">
  <li class="item"><a href="#">Home</a></li>
  <li class="item dropdownitem">
    <a href="#" class="nav-dropdown">Services</a>
    <div class="dropdown">
      <ul>
        <li class="item"><a href="#">Software</a></li>
      </ul>
    </div>
  </li>
</ul>
```

## 5. Why This Happened
WordPress Walker_Nav_Menu class controls HTML output of nav menus. Default output does not include custom classes or div wrappers. Custom Walker overrides only the specific methods needed (start_el, start_lvl, end_lvl) without rewriting the entire class.

## 6. Resolution Status
✅ Custom Walker class produces the exact HTML structure requested.

## 7. Prevention Tips
- Always extend Walker_Nav_Menu rather than rewriting from scratch
- Test navigation on mobile devices after implementing custom walkers
- Add CSS for the dropdown classes after implementing the walker
- Use child theme or custom plugin to avoid losing changes on theme update
