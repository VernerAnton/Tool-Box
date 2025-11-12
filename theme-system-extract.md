# Auto Dark/Light Theme System - Complete Implementation

This is a complete, self-contained theme system extracted from the ToolBox application. It supports automatic detection of system preferences and manual toggling.

## Features
- 🌓 Auto-detects system theme preference
- 🔄 Three modes: System (auto), Light, Dark
- ⚡ No flash on page load
- 💾 Remembers user preference
- 🎯 Responds to system theme changes in real-time

## 1. HTML Structure

```html
<!-- Add this in your <head> BEFORE any other scripts -->
<script>
    // Immediately apply saved theme to prevent flash
    (function() {
        const saved = localStorage.getItem('themePreference') || 'system';
        const isDark = saved === 'dark' || (saved === 'system' && window.matchMedia('(prefers-color-scheme: dark)').matches);
        if (isDark) {
            document.documentElement.style.background = '#1a1a1a';
        }
    })();
</script>

<!-- Add to <body> -->
<body class="loading">
    <!-- Your theme toggle button -->
    <button onclick="cycleTheme()" id="theme-btn" aria-label="Toggle theme"></button>

    <!-- Your content here -->
</body>
```

## 2. CSS Styling

```css
/* Loading state to prevent flash */
body.loading {
    visibility: hidden;
}

/* Smooth transitions */
body {
    transition: background 0.3s ease, color 0.3s ease;
}

/* Light mode styling */
body.light-mode {
    background: linear-gradient(to bottom, #f4f1ea 0%, #ebe7db 100%);
    color: #2a2a2a;
}

/* Dark mode styling */
body.dark-mode {
    background: linear-gradient(to bottom, #1a1a1a 0%, #0d0d0d 100%);
    color: #e0e0e0;
}

/* Example: Style elements differently per theme */
.light-mode .card {
    background: #fefdfb;
    border-color: #2a2a2a;
    box-shadow: 4px 4px 0px #2a2a2a;
    color: #2a2a2a;
}

.dark-mode .card {
    background: #2a2a2a;
    border-color: #e0e0e0;
    box-shadow: 4px 4px 0px #e0e0e0;
    color: #e0e0e0;
}

/* Button styling (optional) */
#theme-btn {
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    padding: 0.25rem;
    transition: transform 0.2s;
}

#theme-btn:hover {
    transform: scale(1.1);
}
```

## 3. JavaScript - Complete Theme System

```javascript
// Theme Management System
const body = document.body;
const themeBtn = document.getElementById('theme-btn');
const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');

// Apply theme based on preference
function applyTheme(preference) {
    const isSystemDark = mediaQuery.matches;
    let finalTheme;

    if (preference === 'system') {
        finalTheme = isSystemDark ? 'dark' : 'light';
    } else {
        finalTheme = preference;
    }

    body.classList.remove('dark-mode', 'light-mode', 'loading');
    body.classList.add(finalTheme + '-mode');

    updateButton(preference, finalTheme);
}

// Update button icon based on current theme
function updateButton(preference, actualTheme) {
    if (preference === 'system') {
        themeBtn.textContent = '🌓';
        themeBtn.title = `Auto (${actualTheme})`;
    } else if (actualTheme === 'dark') {
        themeBtn.textContent = '☀️';
        themeBtn.title = 'Switch to system';
    } else {
        themeBtn.textContent = '🌙';
        themeBtn.title = 'Switch to dark';
    }
}

// Cycle through theme modes: system -> light -> dark -> system
function cycleTheme() {
    const current = localStorage.getItem('themePreference') || 'system';
    let next;

    if (current === 'system') {
        next = 'light';
    } else if (current === 'light') {
        next = 'dark';
    } else {
        next = 'system';
    }

    localStorage.setItem('themePreference', next);
    applyTheme(next);
}

// Listen for system theme changes and update if in auto mode
mediaQuery.addEventListener('change', () => {
    const pref = localStorage.getItem('themePreference') || 'system';
    if (pref === 'system') {
        applyTheme('system');
    }
});

// Initialize on page load
document.addEventListener('DOMContentLoaded', () => {
    const savedPref = localStorage.getItem('themePreference') || 'system';
    applyTheme(savedPref);
});

// Apply immediately to reduce flash (runs before DOMContentLoaded)
const immediatePref = localStorage.getItem('themePreference') || 'system';
const isImmediateDark = immediatePref === 'dark' ||
    (immediatePref === 'system' && mediaQuery.matches);

body.classList.add(isImmediateDark ? 'dark-mode' : 'light-mode');
```

## 4. How It Works

### Storage
- Stores user preference in `localStorage` as `themePreference`
- Values: `'system'`, `'light'`, or `'dark'`

### Detection
- Uses `window.matchMedia('(prefers-color-scheme: dark)')` to detect system preference
- Listens for real-time changes to system theme

### Cycle Behavior
1. **System (auto)** 🌓 - Automatically follows OS theme
2. **Light** 🌙 - Forces light mode
3. **Dark** ☀️ - Forces dark mode
4. (Back to System)

### Flash Prevention
The code runs in THREE stages to prevent flash:
1. **Inline `<script>` in `<head>`** - Sets background immediately
2. **Immediate execution** - Adds mode class before page visible
3. **DOMContentLoaded** - Full initialization with button updates

## 5. Customization Guide

### Change Colors
Replace the gradient and color values in the CSS:
```css
body.light-mode {
    background: YOUR_LIGHT_GRADIENT;
    color: YOUR_LIGHT_TEXT_COLOR;
}

body.dark-mode {
    background: YOUR_DARK_GRADIENT;
    color: YOUR_DARK_TEXT_COLOR;
}
```

### Change Button Icons
Edit the `updateButton()` function:
```javascript
function updateButton(preference, actualTheme) {
    if (preference === 'system') {
        themeBtn.textContent = 'YOUR_AUTO_ICON';
        themeBtn.title = `Auto (${actualTheme})`;
    } else if (actualTheme === 'dark') {
        themeBtn.textContent = 'YOUR_LIGHT_ICON';
        themeBtn.title = 'Switch to light';
    } else {
        themeBtn.textContent = 'YOUR_DARK_ICON';
        themeBtn.title = 'Switch to dark';
    }
}
```

### Change Cycle Order
Edit the `cycleTheme()` function to change the sequence:
```javascript
// Example: light -> dark -> light (no system mode)
function cycleTheme() {
    const current = localStorage.getItem('themePreference') || 'light';
    const next = current === 'light' ? 'dark' : 'light';
    localStorage.setItem('themePreference', next);
    applyTheme(next);
}
```

### Add More Themed Elements
Add `.light-mode` and `.dark-mode` variants for any element:
```css
.light-mode .your-element {
    /* light mode styles */
}

.dark-mode .your-element {
    /* dark mode styles */
}
```

## 6. Integration Checklist

- [ ] Add inline script in `<head>` section
- [ ] Add `class="loading"` to `<body>` tag
- [ ] Add theme button with `id="theme-btn"` and `onclick="cycleTheme()"`
- [ ] Copy all CSS theme styles
- [ ] Copy all JavaScript functions (before closing `</body>`)
- [ ] Customize colors and icons to match your aesthetic
- [ ] Test all three modes: system, light, dark
- [ ] Test system theme changes while in auto mode
- [ ] Verify no flash on page load

## 7. Browser Compatibility

- ✅ Chrome/Edge 76+
- ✅ Firefox 67+
- ✅ Safari 12.1+
- ✅ Opera 63+

Uses `window.matchMedia('(prefers-color-scheme: dark)')` which is widely supported.

## 8. Dependencies

**None!** This is pure vanilla JavaScript with no external dependencies.
