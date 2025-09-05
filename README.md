# Link Search Extension - Documentation

## Overview

Link Search is a Chrome extension that provides fast in-page link searching with advanced search modes and keyboard navigation. Users can quickly find and navigate to any link or button on a webpage using regular text search or smart word prefix matching, without scrolling or using the mouse.

## Current Features

### Core Functionality
- **In-page search overlay** - Appears centered at the top of any webpage
- **Real-time filtering** - Links are filtered as you type with multiple search modes
- **Visual highlighting** - Matching links are highlighted on the page
- **Keyboard navigation** - Navigate between results using multiple methods
- **Link preview** - Shows destination URL in bottom-left corner
- **Button support** - Can search and interact with buttons in addition to links
- **Smart word search** - Match word prefixes in sequence for faster navigation
- **Case-sensitive search** - Optional exact case matching

### Search Behavior
- **Configurable minimum characters** - Set via options page (1-10 characters, default: 1)
- **Smart word search** - Match word prefixes in sequence (e.g., "he w" finds "hello world")
- **Case-sensitive option** - Toggle between case-sensitive and case-insensitive search
- **Text-based search** - Searches only in visible link text, not URLs
- **Include/exclude buttons** - Configure button searching via options page
- **Instant results** - No delay, results appear immediately

### Navigation Options
1. **Arrow keys** - ↑/↓ to move between results
2. **Tab navigation** - Tab/Shift+Tab to move between results  
3. **Mouse navigation** - Click on ↑/↓ buttons in search bar
4. **Direct click** - Click on highlighted links (though not the primary use case)

### Activation Methods
- **Ctrl+Q** - Primary keyboard shortcut
- **Ctrl+Shift+Q** - Alternative keyboard shortcut
- **Extension icon click** - Click the extension icon in toolbar

## User Interface

### Search Overlay (Top Center)
```
[Search input field] [↑] [↓] [2/5]
```

Components:
- **Search input** - Text field for typing search query
- **Navigation buttons** - ↑/↓ buttons for mouse navigation
- **Counter** - Shows current selection and total results (e.g., "2/5")

*Note: The "Include buttons" option has been moved to the options page for a cleaner interface.*

### Link Preview (Bottom Left)
- Shows full URL for links
- Shows "[Button] button text" for buttons
- Only visible when a result is selected
- Automatically hides when no results

### Visual Highlighting
- **All matches** - Yellow background with orange border
- **Selected match** - Blue background with pulsing animation and scale effect
- **Auto-scroll** - Page automatically scrolls to selected element

## Technical Architecture

### File Structure
```
link-search-extension/
├── manifest.json          # Extension configuration (Manifest V3)
├── content.js             # Main functionality (modular architecture)  
├── content.css            # Styling for search overlay and highlighting
├── popup.html             # Popup when clicking extension icon
├── popup.js               # Popup functionality
├── background.js          # Background service worker
├── options.html           # Settings page UI
└── options.js             # Settings page functionality
```

### Code Architecture (content.js)

The main content script uses a modular namespace pattern with these modules:

#### Global State
- `LinkSearchState` - Central state object containing all runtime data

#### Core Modules
- **Settings** - Manages configuration loading/saving
- **UI** - Handles overlay creation and updates  
- **Scanner** - Scans page for searchable elements
- **Search** - Core search functionality and filtering
- **Highlighter** - Manages visual highlighting of elements
- **Navigation** - Keyboard and programmatic navigation
- **Actions** - Handles opening links/clicking buttons
- **App** - Main application controller

### Element Detection
- **Links**: `a[href]` elements with non-empty text
- **Buttons**: `button`, `input[type="button"]`, `input[type="submit"]`, `[role="button"]`
- **Text sources**: `textContent`, `value`, `aria-label` attributes
- **Filtering**: Only elements with visible text are included

### Storage & Settings
- **All settings** - Stored in chrome.storage.sync (syncs across devices)
- **Settings page** - Accessible via right-click menu or extension details
- **Available options**:
  - Minimum characters to search (1-10, default: 1)
  - Smart word search (default: off)
  - Include buttons in search (default: on)
  - Case-sensitive search (default: off)

## User Experience Flow

### Typical Usage
1. User presses **Ctrl+Q** on any webpage
2. Search overlay appears centered at top of page
3. User types part of a link text (e.g., "contact" or "con us" with smart search)
4. Matching links are highlighted on the page
5. First match is automatically selected (blue highlight)
6. URL preview appears in bottom-left corner
7. User navigates with **↑/↓** or **Tab/Shift+Tab**
8. User presses **Enter** to open selected link
9. Extension closes and navigates to the link

### Advanced Usage
- **Smart word search** - Enable in options to search by word prefixes ("con us" finds "contact us")
- **Case-sensitive search** - Enable in options for exact case matching
- **Button searching** - Configure in options to include/exclude buttons from search
- **Minimum characters** - Set higher threshold in options to reduce noise
- **External links** - Open in new tab automatically
- **Internal links** - Open in same tab

## Extension Permissions

```json
"permissions": [
  "activeTab",       # Access to current tab for content injection
  "storage",         # For saving user preferences  
  "contextMenus"     # For right-click options menu
]
```

## Browser Compatibility

- **Chrome** - Full support (Manifest V3)
- **Edge** - Should work (Chromium-based)
- **Firefox** - Would need Manifest V2 version

## Current Limitations

1. **Single tab operation** - Only works on currently active tab
2. **Text-only search** - Cannot search by URL or CSS selectors
3. **No search history** - Each search session starts fresh
4. **No regex support** - Only simple string matching and word prefix matching
5. **No custom shortcuts** - Fixed keyboard shortcuts only
6. **Sequential word matching** - Smart word search requires words in order

## Performance Considerations

- **Lazy loading** - Elements are scanned only when search is activated
- **Efficient filtering** - Uses native array methods for fast filtering
- **Minimal DOM impact** - Only adds CSS classes for highlighting
- **Memory management** - State is reset when overlay is closed

## Security Features

- **CSP compliant** - No inline scripts (Manifest V3 requirement)
- **Minimal permissions** - Only requests necessary permissions
- **No external requests** - All functionality is local
- **No data collection** - Settings stored locally only

## Development Notes

### Key Technical Decisions
- **Namespace pattern** - Avoids global scope pollution
- **No `this` context** - Explicit namespace calls for reliability
- **CSS-only highlighting** - No complex DOM manipulation
- **Event delegation** - Efficient event handling
- **Graceful fallbacks** - Works even if some features fail

### Extension Lifecycle
1. **Installation** - Manifest is processed, permissions requested
2. **Page load** - Content script injected into each page
3. **Activation** - User triggers search (Ctrl+Q or icon click)
4. **Runtime** - Search overlay active, responsive to user input
5. **Deactivation** - Overlay closed, page returns to normal state

### CSS Specificity Strategy
- High z-index values (999999) to appear above page content
- `!important` declarations for critical styling
- Unique class names to avoid conflicts
- Scoped selectors to prevent style bleeding

## Future Enhancement Opportunities

### Potential Features
- Fuzzy search support
- Search history/favorites  
- Custom keyboard shortcuts
- Multi-tab search
- URL-based searching
- Regular expression support
- Theme customization
- Search analytics/metrics
- Non-sequential word matching for smart search
- Highlight matching portions within text

### Technical Improvements
- Improve readability for dark pages.
- Performance optimization for large pages
- Internationalization (i18n)

## Troubleshooting

### Common Issues
- **Extension not working** - Check if content script is injected
- **Keyboard shortcuts not working** - Check for conflicts with other extensions
- **Settings not saving** - Verify chrome.storage permissions
- **Links not highlighting** - Check CSS injection and z-index values
- **Preview not showing** - Verify DOM element creation and positioning
