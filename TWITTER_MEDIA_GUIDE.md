# Twitter Media DOM Guide

Quick reference for finding and manipulating Twitter media elements.

## Finding Media Elements

### Use Browser DevTools
1. Right-click on media → Inspect Element
2. Look for `data-testid` attributes (Twitter's stable selectors)
3. Note the parent structure - media is often deeply nested

### Search in Saved HTML
```bash
# Find all data-testid attributes
rg -o 'data-testid="[^"]*"' examples/*.html | sort -u

# Find specific media types
rg 'data-testid="tweetPhoto"' examples/*.html -B 5 -A 5
rg 'data-testid="videoPlayer"' examples/*.html -B 5 -A 5

# Find elements with background images
rg 'background-image: url' examples/*.html
```

## Media Element Structure

### Images (tweetPhoto)
```html
<a href="..." role="link">                          <!-- Link wrapper (captures clicks) -->
  <div ...>
    <div data-testid="tweetPhoto">                  <!-- Container selector -->
      <div style="background-image: url(...)"></div> <!-- Background version -->
      <img alt="Image" src="..." />                 <!-- Actual img element -->
    </div>
  </div>
</a>
```

**Key Points:**
- Target: `[data-testid="tweetPhoto"]`
- Images inside as: `img` tags AND `div[style*="background-image"]`
- Apply styles to **child elements**, not container (link wrapper blocks interaction)
- Hover on container: `[data-testid="tweetPhoto"]:hover img`

### Videos
```html
<div data-testid="videoPlayer">
  <video autoplay ...>
    <source src="..." />
  </video>
</div>
```

**Key Points:**
- Target: `[data-testid="videoPlayer"]` or `[data-testid="videoComponent"]`
- Videos autoplay by default - disable via JavaScript
- Can apply filters directly to container

### Profile Banners
```html
<a href="username/header_photo">
  <div ...>
    <img alt="" src="profile_banners/..." />
  </div>
</a>
```

**Key Points:**
- Target: `a[href$="/header_photo"]`
- Safe to style the `<a>` directly

### Link Preview Cards
```html
<div data-testid="card.wrapper">
  <div ...>
    <img ... />
  </div>
</div>
```

**Key Points:**
- Target: `[data-testid="card.wrapper"]`
- Contains images/videos from link previews

## Common Pitfalls

### ❌ Wrong: Blur on container when link wraps it
```css
[data-testid="tweetPhoto"] {
  filter: blur(20px); /* Doesn't work - link blocks hover */
}
```

### ✅ Correct: Blur on child elements
```css
[data-testid="tweetPhoto"] img,
[data-testid="tweetPhoto"] div[style*="background-image"] {
  filter: blur(20px);
}
[data-testid="tweetPhoto"]:hover img,
[data-testid="tweetPhoto"]:hover div[style*="background-image"] {
  filter: blur(0);
}
```

### ❌ Wrong: Using display:none loses interaction
```css
[data-testid="tweetPhoto"] {
  display: none; /* Kills click/hover events */
}
```

### ✅ Correct: Use filters to obscure
```css
[data-testid="tweetPhoto"] img {
  filter: blur(50px) brightness(0.2); /* Visible but unreadable */
}
```

## Testing Selectors

### Quick Test in Console
```javascript
// Count how many images exist
document.querySelectorAll('[data-testid="tweetPhoto"]').length

// Test blur on all images
document.querySelectorAll('[data-testid="tweetPhoto"] img').forEach(img => {
  img.style.filter = 'blur(50px) brightness(0.2)';
});

// Check if selector works
document.querySelector('[data-testid="videoPlayer"]')
```

### Verify in Extension
1. Make changes to `script.js`
2. Run `make all`
3. Reload extension in `about:debugging`
4. Hard refresh Twitter page (Cmd+Shift+R)
5. Check browser console for errors

## Filter Recommendations

### For White/Light Images
```css
filter: blur(50px) brightness(0.2) contrast(0.5);
```

### For Dark Images/Videos
```css
filter: blur(30px) brightness(0.5);
```

### Plus Dark Overlay
```css
element::before {
  content: "";
  position: absolute;
  top: 0; left: 0; right: 0; bottom: 0;
  background: var(--background-color, #000);
  opacity: 0.7;
  z-index: 1;
  pointer-events: none;
}
```

## Data Attributes Reference

Common `data-testid` values:
- `tweetPhoto` - Tweet images
- `videoPlayer`, `videoComponent` - Videos
- `card.wrapper` - Link preview cards
- `Tweet-User-Avatar` - Avatar containers
- `UserAvatar-Container-{username}` - User-specific avatars
