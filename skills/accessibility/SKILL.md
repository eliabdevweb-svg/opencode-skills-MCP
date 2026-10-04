---
name: accessibility
description: "Accessibilite web WCAG 2.1 AA, HTML semantique, ARIA, navigation clavier, screen reader, design inclusif. Use when reviewing or implementing accessibility, ARIA patterns, keyboard navigation, or WCAG compliance."
---

# Accessibility (a11y) Skill

## Overview
Expert in implementing WCAG 2.1 AA compliance, semantic HTML, ARIA patterns, keyboard navigation, screen reader optimization, and inclusive design principles.

## WCAG 2.1 Principles (POUR)

### 1. Perceivable
**Text Alternatives**
```html
<!-- Bad -->
<img src="chart.png">

<!-- Good -->
<img src="chart.png" alt="Sales increased 25% from Q1 to Q2 2026">

<!-- Decorative images -->
<img src="decoration.png" alt="" role="presentation">
```

**Color & Contrast**
- Minimum contrast ratio: 4.5:1 for normal text, 3:1 for large text
- Never convey information through color alone
- Use patterns, labels, or icons as secondary indicators

```css
/* Good: High contrast */
.text-primary { color: #1a1a1a; } /* On white: 16.75:1 ratio */
.text-secondary { color: #595959; } /* On white: 7.0:1 ratio */

/* Bad: Low contrast */
.text-light { color: #999999; } /* On white: 2.85:1 ratio - fails */
```

**Content Adaptable**
- Use semantic headings (h1-h6) in order
- Ensure content reflows at 320px width (no horizontal scroll)
- Respect user's `prefers-reduced-motion`

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### 2. Operable
**Keyboard Navigation**
```html
<!-- Focusable elements must have visible focus -->
<style>
  :focus-visible {
    outline: 3px solid #005fcc;
    outline-offset: 2px;
  }

  /* Don't remove focus for mouse users */
  :focus:not(:focus-visible) {
    outline: none;
  }
</style>

<!-- Skip navigation link -->
<a href="#main-content" class="skip-link">
  Skip to main content
</a>

<main id="main-content" tabindex="-1">
  <!-- Content -->
</main>
```

**Interactive Elements**
```html
<!-- Custom button with proper semantics -->
<button
  type="button"
  aria-label="Close dialog"
  aria-expanded="false"
  onclick="closeDialog()"
>
  <svg aria-hidden="true" focusable="false">
    <path d="M18 6L6 18M6 6l12 12" />
  </svg>
</button>

<!-- Tabs pattern -->
<div role="tablist" aria-label="Settings">
  <button role="tab" aria-selected="true" aria-controls="panel-1" id="tab-1">
    General
  </button>
  <button role="tab" aria-selected="false" aria-controls="panel-2" id="tab-2" tabindex="-1">
    Security
  </button>
</div>

<div role="tabpanel" id="panel-1" aria-labelledby="tab-1">
  General settings content
</div>
```

### 3. Understandable
**Labels & Instructions**
```html
<!-- Every input needs a label -->
<label for="email">Email address</label>
<input
  type="email"
  id="email"
  name="email"
  aria-required="true"
  aria-describedby="email-help email-error"
>
<span id="email-help">We'll never share your email</span>
<span id="email-error" role="alert" aria-live="assertive">
  <!-- Error message appears here -->
</span>

<!-- Grouped inputs -->
<fieldset>
  <legend>Notification preferences</legend>
  <label>
    <input type="checkbox" name="notifications" value="email"> Email
  </label>
  <label>
    <input type="checkbox" name="notifications" value="sms"> SMS
  </label>
</fieldset>
```

**Error Prevention**
- Confirm destructive actions
- Allow users to review before submitting
- Provide clear error messages with recovery instructions

### 4. Robust
**Valid HTML**
```html
<!-- Use landmark roles -->
<header role="banner">...</header>
<nav role="navigation" aria-label="Main">...</nav>
<main role="main">...</main>
<aside role="complementary">...</aside>
<footer role="contentinfo">...</footer>

<!-- Live regions for dynamic content -->
<div aria-live="polite" aria-atomic="true">
  <!-- Status updates announced to screen readers -->
</div>

<div role="status" aria-live="polite">
  3 items in cart
</div>
```

## Common Patterns

### Modal Dialog
```html
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="dialog-title"
  aria-describedby="dialog-desc"
>
  <h2 id="dialog-title">Confirm Delete</h2>
  <p id="dialog-desc">This action cannot be undone.</p>
  <button autofocus>Cancel</button>
  <button>Delete</button>
</div>
```

### Alert
```html
<div role="alert" aria-live="assertive">
  <strong>Error:</strong> Please fix the following errors:
  <ul>
    <li>Email is required</li>
  </ul>
</div>
```

### Loading States
```html
<div aria-busy="true" aria-live="polite">
  <span class="spinner" aria-hidden="true"></span>
  Loading content...
</div>
```

## Testing Tools
- **Automated**: axe-core, Lighthouse, WAVE
- **Manual**: Keyboard-only navigation, screen reader testing (NVDA, VoiceOver)
- **Browser Extensions**: axe DevTools, WAVE Evaluation Tool

## Checklist
- [ ] All images have appropriate alt text
- [ ] Color contrast meets 4.5:1 ratio
- [ ] All functionality works via keyboard
- [ ] Focus order is logical
- [ ] Focus indicators are visible
- [ ] Form inputs have associated labels
- [ ] Error messages are announced to screen readers
- [ ] ARIA attributes used correctly
- [ ] Page has proper heading hierarchy
- [ ] Skip navigation link present
