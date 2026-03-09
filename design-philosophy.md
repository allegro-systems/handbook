# Design Philosophy

_PHI · principles + style guide_

This document describes how Allegro's own products should feel and look. It is not a promise that Score ships every one of these ideas as a framework primitive.

## Product Standards

- Interfaces should feel immediate. Most interactions should acknowledge input within a fraction of a second.
- Navigation should stay shallow and legible. Users should not need deep hierarchies to understand where they are.
- Copy should be direct, active, and short. Explanatory UI is preferred over tours, overlays, and tutorial chrome.
- Motion should clarify state changes, not decorate them. If an animation can be removed without losing meaning, it probably should be.
- Hit targets must stay comfortable on touch devices. Small targets are a quality failure.
- Empty, loading, and error states should look intentional. They are part of the product, not fallback debris.
- Visual hierarchy should come from spacing, typography, and contrast before ornament.
- Forms should behave conservatively with user data. Loss of work should be rare and clearly signposted.
- Accessibility is a baseline quality requirement, especially for semantics, focus behaviour, and contrast.

## What We Avoid

- Autoplay and attention traps
- First-load modal interruptions
- Excessive tooltip dependency
- Deeply nested navigation for simple products
- Full-page loading spinners as the default answer to latency
- Decorative motion that obscures information or harms readability

## Relationship to Score

Score should make these outcomes easy where that aligns with the framework's core model, but the product standards live here because they are Allegro standards first and framework affordances second.

---

## Style Guide

### Typography

```
@import url('https://fonts.googleapis.com/css2?family=DM+Mono:ital,wght@0,300;0,400;0,500;1,300&family=Fraunces:ital,opsz,wght@0,9..144,300;0,9..144,600;1,9..144,300&display=swap');
```

| Role | Family | Weight | Notes |
|------|--------|--------|-------|
| Body / Code | `DM Mono` | 300, 400, 500, 300i | Monospace. Primary text and code blocks. |
| Display / Headings | `Fraunces` | 300, 600, 300i | Serif. Optical size 9–144. Headings, titles, section labels. |

- Body text: `font-family: 'DM Mono', monospace; font-size: 13px; line-height: 1.6;`
- Headings: `font-family: 'Fraunces', serif; font-weight: 300; letter-spacing: -0.5px;`
- Labels and tags: `font-size: 10–11px; letter-spacing: 0.08–0.12em; text-transform: uppercase;`

### Color Tokens

All colors are exposed as CSS custom properties on `:root`.

```css
:root {
  --bg: #0f0e0c;
  --surface: #1a1814;
  --surface2: #242018;
  --border: #2e2a22;
  --text: #e8e0d0;
  --text-dim: #7a7060;
  --text-dimmer: #3d3830;
  --accent: #c8a96e;
  --accent-dim: #7a6540;
  --global-tag: #6e9ec8;
  --page-tag: #c86e9e;
  --component-tag: #6ec88a;
  --code-bg: #13120f;
  --radius: 6px;
}
```

<table style="border-collapse:collapse;font-family:'DM Mono',monospace;font-size:13px;width:100%;background:#0f0e0c;color:#e8e0d0;border:1px solid #2e2a22;border-radius:6px;">
<tr style="border-bottom:1px solid #2e2a22;background:#1a1814;">
  <td style="padding:10px 14px;color:#7a7060;font-size:10px;letter-spacing:0.1em;text-transform:uppercase;">Swatch</td>
  <td style="padding:10px 14px;color:#7a7060;font-size:10px;letter-spacing:0.1em;text-transform:uppercase;">Token</td>
  <td style="padding:10px 14px;color:#7a7060;font-size:10px;letter-spacing:0.1em;text-transform:uppercase;">Hex</td>
  <td style="padding:10px 14px;color:#7a7060;font-size:10px;letter-spacing:0.1em;text-transform:uppercase;">Usage</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#0f0e0c;border:1px solid #2e2a22;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--bg</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#0f0e0c</td>
  <td style="padding:8px 14px;color:#7a7060;">Page background</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#1a1814;border:1px solid #2e2a22;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--surface</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#1a1814</td>
  <td style="padding:8px 14px;color:#7a7060;">Cards, pane headers, input backgrounds</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#242018;border:1px solid #2e2a22;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--surface2</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#242018</td>
  <td style="padding:8px 14px;color:#7a7060;">Elevated surfaces, demo bodies</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#2e2a22;border:1px solid #3d3830;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--border</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#2e2a22</td>
  <td style="padding:8px 14px;color:#7a7060;">Borders, dividers</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#e8e0d0;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--text</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#e8e0d0</td>
  <td style="padding:8px 14px;color:#7a7060;">Primary text</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#7a7060;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--text-dim</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#7a7060</td>
  <td style="padding:8px 14px;color:#7a7060;">Secondary text, labels</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#3d3830;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--text-dimmer</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#3d3830</td>
  <td style="padding:8px 14px;color:#7a7060;">Tertiary text, comments, placeholders</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#c8a96e;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--accent</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#c8a96e</td>
  <td style="padding:8px 14px;color:#7a7060;">Gold accent, active elements, prices</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#7a6540;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--accent-dim</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#7a6540</td>
  <td style="padding:8px 14px;color:#7a7060;">Muted accent, toggle tracks</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#6e9ec8;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--global-tag</code></td>
  <td style="padding:8px 14px;color:#6e9ec8;">#6e9ec8</td>
  <td style="padding:8px 14px;color:#7a7060;">Blue — application/global scope</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#c86e9e;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--page-tag</code></td>
  <td style="padding:8px 14px;color:#c86e9e;">#c86e9e</td>
  <td style="padding:8px 14px;color:#7a7060;">Pink — page scope</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#6ec88a;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--component-tag</code></td>
  <td style="padding:8px 14px;color:#6ec88a;">#6ec88a</td>
  <td style="padding:8px 14px;color:#7a7060;">Green — element/component scope</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:3px;background:#13120f;border:1px solid #2e2a22;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--code-bg</code></td>
  <td style="padding:8px 14px;color:#7a7060;">#13120f</td>
  <td style="padding:8px 14px;color:#7a7060;">Code block backgrounds</td>
</tr>
<tr>
  <td style="padding:8px 14px;"><span style="display:inline-block;width:28px;height:16px;border-radius:6px;background:linear-gradient(90deg,#1a1814,#242018);border:1px solid #2e2a22;"></span></td>
  <td style="padding:8px 14px;color:#c8c0a8;"><code>--radius</code></td>
  <td style="padding:8px 14px;color:#7a7060;">6px</td>
  <td style="padding:8px 14px;color:#7a7060;">Default border radius</td>
</tr>
</table>

### Syntax Highlighting

Syntax highlighting follows a consistent palette across Swift source and compiled JS output.

<div style="background:#13120f;border:1px solid #2e2a22;border-radius:6px;overflow:hidden;font-family:'DM Mono',monospace;font-size:12px;line-height:1.7;">
<div style="background:#1a1814;padding:10px 16px;border-bottom:1px solid #2e2a22;display:flex;justify-content:space-between;">
  <span style="font-size:10px;letter-spacing:0.1em;text-transform:uppercase;color:#7a7060;">Syntax Roles</span>
  <span style="font-size:10px;color:#3d3830;letter-spacing:0.05em;">preview</span>
</div>
<pre style="padding:20px 16px;margin:0;color:#e8e0d0;"><code><span style="color:#c86e9e;">struct</span> <span style="color:#6e9ec8;">ShopPage</span>: <span style="color:#6e9ec8;">Page</span> {
    <span style="color:#c8a96e;">@State</span> <span style="color:#c86e9e;">var</span> <span style="color:#c8c0a8;">activeFilter</span>: <span style="color:#6e9ec8;">String</span> = <span style="color:#6ec88a;">"all"</span>
    <span style="color:#c86e9e;">let</span> <span style="color:#c8c0a8;">maxResults</span>: <span style="color:#6e9ec8;">Int</span> = <span style="color:#c8956e;">25</span>

    <span style="color:#3d3830;font-style:italic;">// filter products by category</span>
    <span style="color:#c8a96e;">@Action</span>
    <span style="color:#c86e9e;">func</span> <span style="color:#a09070;">setFilter</span>(<span style="color:#c8c0a8;">_</span> f: <span style="color:#6e9ec8;">String</span>) {
        activeFilter = f
    }
}</code></pre>
</div>

<table style="border-collapse:collapse;font-family:'DM Mono',monospace;font-size:12px;width:100%;margin-top:12px;background:#0f0e0c;color:#e8e0d0;border:1px solid #2e2a22;border-radius:6px;">
<tr style="border-bottom:1px solid #2e2a22;background:#1a1814;">
  <td style="padding:8px 14px;color:#7a7060;font-size:10px;letter-spacing:0.1em;text-transform:uppercase;">Role</td>
  <td style="padding:8px 14px;color:#7a7060;font-size:10px;letter-spacing:0.1em;text-transform:uppercase;">Preview</td>
  <td style="padding:8px 14px;color:#7a7060;font-size:10px;letter-spacing:0.1em;text-transform:uppercase;">Hex</td>
  <td style="padding:8px 14px;color:#7a7060;font-size:10px;letter-spacing:0.1em;text-transform:uppercase;">Class</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:6px 14px;color:#7a7060;">Keyword</td>
  <td style="padding:6px 14px;color:#c86e9e;">struct var func let guard return</td>
  <td style="padding:6px 14px;color:#7a7060;">#c86e9e</td>
  <td style="padding:6px 14px;color:#3d3830;">.kw</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:6px 14px;color:#7a7060;">Type / Tag</td>
  <td style="padding:6px 14px;color:#6e9ec8;">String Int Page Bool Application</td>
  <td style="padding:6px 14px;color:#7a7060;">#6e9ec8</td>
  <td style="padding:6px 14px;color:#3d3830;">.type</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:6px 14px;color:#7a7060;">Macro / Decorator</td>
  <td style="padding:6px 14px;color:#c8a96e;">@State @Computed @Action @main</td>
  <td style="padding:6px 14px;color:#7a7060;">#c8a96e</td>
  <td style="padding:6px 14px;color:#3d3830;">.macro</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:6px 14px;color:#7a7060;">String / Value</td>
  <td style="padding:6px 14px;color:#6ec88a;">"all" "signal-polyfill" "shop"</td>
  <td style="padding:6px 14px;color:#7a7060;">#6ec88a</td>
  <td style="padding:6px 14px;color:#3d3830;">.str</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:6px 14px;color:#7a7060;">Number</td>
  <td style="padding:6px 14px;color:#c8956e;">0 10 25 52</td>
  <td style="padding:6px 14px;color:#7a7060;">#c8956e</td>
  <td style="padding:6px 14px;color:#3d3830;">.num</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:6px 14px;color:#7a7060;">Comment</td>
  <td style="padding:6px 14px;color:#3d3830;font-style:italic;">// filter products by category</td>
  <td style="padding:6px 14px;color:#7a7060;">#3d3830</td>
  <td style="padding:6px 14px;color:#3d3830;">.comment</td>
</tr>
<tr style="border-bottom:1px solid #2e2a22;">
  <td style="padding:6px 14px;color:#7a7060;">Function call</td>
  <td style="padding:6px 14px;color:#a09070;">setFilter toggleTheme signIn</td>
  <td style="padding:6px 14px;color:#7a7060;">#a09070</td>
  <td style="padding:6px 14px;color:#3d3830;">.fn</td>
</tr>
<tr>
  <td style="padding:6px 14px;color:#7a7060;">Property</td>
  <td style="padding:6px 14px;color:#c8c0a8;">activeFilter isDarkMode count</td>
  <td style="padding:6px 14px;color:#7a7060;">#c8c0a8</td>
  <td style="padding:6px 14px;color:#3d3830;">.prop</td>
</tr>
</table>

CSS class reference for JS, HTML, and CSS syntax:

```css
.js-kw { color: #c86e9e; }   .js-cls { color: #6e9ec8; }   .js-fn { color: #c8a96e; }
.js-str { color: #6ec88a; }  .js-num { color: #c8956e; }   .js-comment { color: #3d3830; font-style: italic; }
.js-prop { color: #c8c0a8; }
.html-tag { color: #6e9ec8; }  .html-attr { color: #c8a96e; }  .html-val { color: #6ec88a; }
.css-prop { color: #c8c0a8; }  .css-val { color: #6ec88a; }
```

### Scope Color Language

The three signal state scopes use a consistent color language throughout documentation and tooling:

<div style="display:flex;gap:24px;flex-wrap:wrap;padding:16px 0;">
  <div style="display:flex;align-items:center;gap:8px;font-family:'DM Mono',monospace;font-size:11px;color:#7a7060;letter-spacing:0.08em;text-transform:uppercase;">
    <span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:#6e9ec8;"></span> Application state
  </div>
  <div style="display:flex;align-items:center;gap:8px;font-family:'DM Mono',monospace;font-size:11px;color:#7a7060;letter-spacing:0.08em;text-transform:uppercase;">
    <span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:#c86e9e;"></span> Page state
  </div>
  <div style="display:flex;align-items:center;gap:8px;font-family:'DM Mono',monospace;font-size:11px;color:#7a7060;letter-spacing:0.08em;text-transform:uppercase;">
    <span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:#6ec88a;"></span> Element state
  </div>
</div>

| Scope | Color | Token | Hex |
|-------|-------|-------|-----|
| Application (global) | Blue | `--global-tag` | `#6e9ec8` |
| Page | Pink | `--page-tag` | `#c86e9e` |
| Element (component) | Green | `--component-tag` | `#6ec88a` |

These colors appear in scope tags, legend dots, demo indicators, and signal graph readouts.
