# Font Hierarchy Implementation

This document outlines the updated font hierarchy implemented in Open WebUI - all fonts have been changed to Times Roman.

## Font Categories

### 1. Primary UI Font - Times Roman
**Font Family:** `'Times New Roman', 'Times', serif`
**Usage:**
- Chat messages
- Navigation
- Sidebar
- User names
- Timestamps
**Reason:** Classic serif font with excellent readability and professional appearance.
**CSS Class:** `.font-primary`
**Tailwind Config:** `font-primary`

### 2. Secondary UI Font - Times Roman
**Font Family:** `'Times New Roman', 'Times', serif`
**Usage:**
- Buttons
- Labels
- Form inputs
- Dropdowns
**Reason:** Consistent with primary font for unified appearance.
**CSS Class:** `.font-secondary`
**Tailwind Config:** `font-secondary`

### 3. Code Font - Times Roman
**Font Family:** `'Times New Roman', 'Times', serif`
**Usage:**
- Code blocks
- System messages
- Logs
- Developer mode
**Reason:** Unified font experience across all UI elements.
**CSS Class:** `.font-code`
**Tailwind Config:** `font-mono`

### 4. Branding Font - Times Roman
**Font Family:** `'Times New Roman', 'Times', serif`
**Usage:**
- App logo
- Splash screen
- Headers or taglines
**Reason:** Consistent branding with Times Roman throughout the application.
**CSS Class:** `.font-branding`
**Tailwind Config:** `font-branding`

### 5. Supporting Font - Times Roman
**Font Family:** `'Times New Roman', 'Times', serif`
**Usage:**
- Tooltips
- Modal text
- Help sections
- Secondary content
**Reason:** Unified font experience for all supporting text elements.
**CSS Class:** `.font-supporting`
**Tailwind Config:** `font-supporting`

## Implementation Details

### Files Modified:
1. **tailwind.config.js** - Updated all font family configurations to Times Roman
2. **src/app.css** - Updated all font utility classes to Times Roman
3. **src/tailwind.css** - Updated base font configuration to Times Roman
4. **src/lib/utils/index.ts** - Updated inline font references to Times Roman

### Font Loading:
- **Times Roman**: Uses system font `'Times New Roman'` with fallback to `'Times'` and generic `serif`
- No external font loading required as Times Roman is a standard system font

### Usage Examples:

#### In HTML/Svelte:
```html
<!-- Primary UI Font -->
<div class="font-primary">Chat message content</div>

<!-- Secondary UI Font -->
<button class="font-secondary">Submit</button>

<!-- Code Font -->
<pre class="font-code">console.log('Hello World');</pre>

<!-- Branding Font -->
<h1 class="font-branding">Open WebUI</h1>

<!-- Supporting Font -->
<div class="font-supporting">Tooltip text</div>
```

#### In Tailwind Classes:
```html
<div class="font-primary text-sm">Primary UI text</div>
<div class="font-secondary text-base">Secondary UI text</div>
<div class="font-mono text-xs">Code text</div>
<div class="font-branding text-2xl">Branding text</div>
<div class="font-supporting text-xs">Supporting text</div>
```

### Default Behavior:
- The base `html` and `body` elements use Times Roman
- All `code` elements automatically use Times Roman
- Tiptap editor code blocks use Times Roman
- All font utility classes use Times Roman for consistent appearance

### Fallback Strategy:
All font families use the same fallback chain:
- **Times New Roman** → Times → serif

This ensures consistent Times Roman appearance across all browsers and systems.
