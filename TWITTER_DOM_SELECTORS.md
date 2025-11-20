# Twitter DOM Selectors Documentation

This document describes how to identify and hide various elements in Twitter's DOM structure based on analysis of actual Twitter pages.

## Avatars

### Main Avatar Containers
- `[data-testid="Tweet-User-Avatar"]` - Container for user avatars in tweets
- `[data-testid^="UserAvatar-Container-"]` - User-specific avatar containers (includes username)
  - Example: `data-testid="UserAvatar-Container-badlogicgames"`
  - Example: `data-testid="UserAvatar-Container-randall_balestr"`

### Avatar Images
Avatars are typically `<img>` elements with:
- `src` attributes containing `pbs.twimg.com/profile_images/`
- Located within avatar container divs

### To Hide All Avatars:
```css
[data-testid="Tweet-User-Avatar"],
[data-testid^="UserAvatar-Container-"] {
  display: none !important;
}
```

**Note**: Hiding avatar containers will prevent hover profile cards from appearing. This is expected behavior when hiding avatars.

## Media Content

### Photos
- `[data-testid="tweetPhoto"]` - Container for tweet photos/images
- Contains `<img>` tags with `src` pointing to `pbs.twimg.com/media/`

### Videos
- `[data-testid="videoPlayer"]` - Video player container
- `[data-testid="videoComponent"]` - Video component wrapper

### Cards (Link Previews)
- `[data-testid="card.wrapper"]` - Card container for link previews with media
- `[data-testid="card.layoutLarge.media"]` - Large card layout with media
- `[data-testid="card.layoutSmall.media"]` - Small card layout with media

### To Hide All Media:
```css
[data-testid="tweetPhoto"],
[data-testid="videoPlayer"],
[data-testid="videoComponent"],
[data-testid="card.wrapper"] {
  display: none !important;
}
```

## Testing

To test selectors, save an actual Twitter page HTML and search for these data-testid attributes:

```bash
rg 'data-testid="Tweet-User-Avatar"' examples/*.html
rg 'data-testid="tweetPhoto"' examples/*.html
rg 'data-testid="UserAvatar-Container-' examples/*.html
```

## Notes

- Twitter uses `data-testid` attributes extensively for internal testing
- These selectors are more stable than CSS classes which are often minified/obfuscated
- The DOM structure is heavily nested with `css-175oi2r` utility classes
- Selectors starting with `^=` match attributes that start with the given value
- All hiding should use `!important` to override inline styles
