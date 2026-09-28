# VerseVault

## Design System

**Version:** 1.0
**Status:** Draft
**Related Documents:** `PRD.md`, `USER_FLOWS.md`

---

# 1. Design Philosophy

VerseVault is a platform centered around writing.

The interface should therefore feel like a meeting point between:

* A modern creative studio
* A premium digital magazine
* A personal writer's desk
* A calm social platform

The design should feel **intentional rather than decorative**.

Every visual element should serve one of three purposes:

1. Help users create.
2. Help users read.
3. Help users discover.

Visual polish must never compromise usability.

---

# 2. Brand Personality

VerseVault should feel:

* Literary
* Modern
* Intelligent
* Personal
* Creative
* Calm
* Immersive
* Premium
* Human

It should NOT feel:

* Corporate
* Childish
* Overly futuristic
* Generic SaaS
* Template-generated
* Excessively colorful
* Visually noisy

---

# 3. Brand Concept

## Name

**VerseVault**

The name combines:

**Verse** → writing, poetry, expression, stories.

**Vault** → a place where valuable things are stored and preserved.

The brand therefore represents:

> **A place where words are created, shared, and kept.**

---

# 4. Visual Identity Direction

The visual identity should revolve around:

### Words

Typography is one of the primary visual elements.

### Space

Generous whitespace should allow content to breathe.

### Depth

Depth should be created through subtle:

* Layers
* Borders
* Shadows
* Blur
* Motion

### Motion

Motion should make the interface feel alive without becoming distracting.

---

# 5. Color System

The color system should support both light and dark themes.

Colors must be defined as semantic tokens rather than hard-coded throughout the application.

---

## 5.1 Semantic Tokens

```css
--background
--foreground

--surface
--surface-elevated
--surface-muted

--primary
--primary-foreground

--secondary
--secondary-foreground

--accent
--accent-foreground

--muted
--muted-foreground

--border
--border-subtle

--success
--warning
--danger
--info

--focus
```

Components should consume semantic tokens rather than directly referencing raw color values.

---

# 6. Primary Color Direction

VerseVault should have a restrained primary palette.

Suggested direction:

### Primary

Deep ink-like blue/purple.

Purpose:

* Primary actions
* Links
* Active navigation
* Important UI states

### Accent

A warmer literary accent may be used sparingly.

Possible direction:

* Amber
* Soft gold
* Warm violet

The accent should never dominate the interface.

### Neutrals

The majority of the interface should use neutral tones.

This keeps the written content visually dominant.

---

# 7. Light Theme

The light theme should feel:

* Warm
* Clean
* Paper-like
* Spacious

Avoid pure white everywhere.

Possible conceptual hierarchy:

```text
Background
↓
Surface
↓
Elevated Surface
↓
Border
↓
Content
```

The exact color values should be finalized during implementation after visual testing.

---

# 8. Dark Theme

The dark theme should feel:

* Deep
* Immersive
* Comfortable for reading

Avoid pure black backgrounds wherever possible.

Use near-black surfaces with subtle tonal differences.

Example conceptual hierarchy:

```text
Deep Background
↓
Surface
↓
Elevated Surface
↓
Border
↓
Content
```

---

# 9. Typography

Typography is a central part of VerseVault's identity.

The system should use a combination of:

### Display / Editorial Typeface

A sophisticated serif or editorial font.

Used for:

* Hero headings
* Article titles
* Featured content
* Major section headings

### Interface Typeface

A clean sans-serif.

Used for:

* Navigation
* Buttons
* Forms
* Metadata
* Controls
* Dashboard

This creates the visual distinction:

```text
EDITORIAL CONTENT
        +
MODERN INTERFACE
```

---

# 10. Typography Scale

Initial scale:

```text
Display XL     72px
Display        56px
Heading XL     44px
Heading LG     36px
Heading MD     30px
Heading SM     24px

Body XL        20px
Body LG        18px
Body           16px
Body SM        14px

Caption        12px
```

These values should be fluid/responsive where appropriate.

---

# 11. Reading Typography

The reading experience requires special treatment.

Recommended principles:

* Comfortable line length
* Generous line height
* Strong paragraph separation
* Clear heading hierarchy
* Minimal interface distractions

Ideal reading width:

```text
approximately 60–75 characters per line
```

The exact width should be visually tested.

---

# 12. Font Weight

Suggested weights:

```text
Regular      400
Medium       500
Semibold     600
Bold         700
```

Heavy typography should be used selectively.

The interface should avoid making everything bold.

---

# 13. Spacing System

Use a consistent spacing scale.

Base unit:

```text
4px
```

Suggested tokens:

```text
4
8
12
16
20
24
32
40
48
64
80
96
128
```

Components should use these tokens rather than arbitrary spacing.

---

# 14. Layout System

VerseVault should use a responsive container system.

Conceptually:

```text
Mobile
100% width
+
small horizontal padding

Tablet
centered container

Desktop
centered container
+
maximum content width
```

Different page types may use different maximum widths.

For example:

### Dashboard

Wider.

### Reading page

Narrower.

### Landing page

Large visual width.

---

# 15. Grid

The primary layout should use a flexible grid.

Common structures:

```text
1 column
2 columns
3 columns
4 columns
```

Cards should automatically adapt to available space.

Avoid fixed layouts that break at intermediate screen sizes.

---

# 16. Border Radius

VerseVault should use moderate rounding.

Suggested:

```text
sm     6px
md     10px
lg     14px
xl     20px
2xl    28px
full   9999px
```

Not every component should use rounded corners.

Editorial content may intentionally use sharper geometry.

---

# 17. Borders

Borders should be subtle.

Use borders primarily for:

* Separation
* Input fields
* Cards
* Navigation
* Containers

Avoid excessive borders.

Prefer:

```text
subtle border
+
spacing
```

over:

```text
thick border
+
shadow
+
background
```

---

# 18. Shadows

Shadows should communicate elevation.

Suggested levels:

```text
none
sm
md
lg
xl
```

Avoid heavy shadows.

Dark mode should use elevation primarily through tonal differences and subtle borders rather than large black shadows.

---

# 19. Surface System

Components should use a small number of surface levels.

```text
Background
Surface
Surface Elevated
Surface Interactive
```

This prevents every card from looking like a separate floating object.

---

# 20. Buttons

Buttons should be visually clear and tactile.

Types:

### Primary

Used for the main action.

Examples:

```text
Start Writing
Publish
Save
Create Account
```

### Secondary

Used for supporting actions.

### Ghost

Used for low-emphasis actions.

### Destructive

Used for actions such as:

```text
Delete
Remove
Report
```

---

# 21. Button Behavior

Buttons should provide clear interaction feedback.

States:

```text
Default
Hover
Pressed
Focus
Disabled
Loading
```

Loading buttons should communicate that an operation is in progress.

Example:

```text
Publishing...
```

rather than silently freezing the interface.

---

# 22. Inputs

Inputs should prioritize clarity.

Required states:

```text
Default
Focus
Filled
Error
Disabled
Success
```

Labels should remain accessible.

Placeholder text must not replace labels.

---

# 23. Cards

Cards should be used primarily for:

* Works
* Writers
* Discovery
* Dashboard summaries

A card should have a clear hierarchy.

Example:

```text
┌───────────────────────────────┐
│          Cover Image           │
├───────────────────────────────┤
│ Category                      │
│                               │
│ Work Title                    │
│ Short description...           │
│                               │
│ Author       · 5 min read     │
│ ♡ 120        🔖               │
└───────────────────────────────┘
```

Cards should not contain unnecessary decoration.

---

# 24. Work Card Variants

VerseVault may have:

### Standard Card

For general discovery.

### Featured Card

For highlighted content.

### Compact Card

For feeds and sidebars.

### List Item

For dashboards and search results.

All variants must share the same visual language.

---

# 25. Author Identity

Author information should be consistent.

Author component:

```text
Avatar
Display Name
Username
Optional metadata
```

Avatar sizes:

```text
xs
sm
md
lg
xl
```

---

# 26. Navigation

Navigation should remain minimal.

Primary actions should always be easy to find.

Desktop:

```text
Logo
Explore
Following
Search
Write
Profile
```

Mobile:

```text
Home
Explore
Write
Library
Profile
```

---

# 27. Header

The header should support:

* Transparent variant
* Solid variant
* Sticky variant

The landing page may use a more immersive header.

Application pages should prioritize usability.

---

# 28. Hero Section

The landing page hero should communicate VerseVault's purpose immediately.

Possible hierarchy:

```text
Small eyebrow
      ↓
Large editorial heading
      ↓
Short description
      ↓
Primary CTA
      ↓
Secondary action
```

Hero animation should be subtle.

---

# 29. Editorial Design

VerseVault should occasionally break from conventional SaaS layouts.

Examples:

* Large typography
* Asymmetric layouts
* Editorial image placement
* Generous whitespace
* Oversized headings
* Layered content

However, experimentation should never damage navigation or usability.

---

# 30. Reading Page Design

The reading page should be one of the most restrained interfaces.

Structure:

```text
Category

Title

Excerpt

Author
Publication information

Cover Image

────────────────────

Article Content

────────────────────

Interaction

Author Card

Related Works
```

The reader should spend most of their attention on the writing.

---

# 31. Writing Editor Design

The editor should feel like a digital writing desk.

Possible structure:

```text
┌─────────────────────────────────────────┐
│ Back        Draft Saved       Publish   │
├─────────────────────────────────────────┤
│                                         │
│ Title                                   │
│                                         │
│ Start writing...                        │
│                                         │
│                                         │
│                                         │
└─────────────────────────────────────────┘
```

The editor should minimize unnecessary controls.

Advanced controls can appear contextually.

---

# 32. Editor States

The editor must communicate:

```text
Unsaved
Saving
Saved
Error
Published
```

Example:

```text
Saving...
Saved just now
```

This builds user confidence.

---

# 33. Dashboard Design

The dashboard should prioritize information hierarchy.

Structure:

```text
Greeting
   ↓
Quick actions
   ↓
Statistics
   ↓
Recent drafts
   ↓
Recent publications
```

Avoid turning the dashboard into an analytics-heavy enterprise interface.

---

# 34. Search Interface

Search should feel lightweight.

Possible structure:

```text
Search VerseVault...

[ Works ] [ Writers ] [ Tags ]

Results
```

Search results should prioritize relevance and readability.

---

# 35. Empty States

Empty states should be visually calm and actionable.

Structure:

```text
Illustration / Icon

Short message

Helpful explanation

Primary action
```

Avoid unnecessary illustrations if they distract from the purpose.

---

# 36. Loading States

Use skeletons for content-heavy interfaces.

Skeletons should roughly represent:

* Text blocks
* Images
* Cards
* Avatar shapes

Avoid generic full-screen spinners for normal page loading.

Use spinners primarily for short actions.

---

# 37. Toast Notifications

Toasts may be used for:

* Saved successfully
* Published successfully
* Bookmark added
* Error occurred

They should not be the only method of communicating critical information.

---

# 38. Modal Dialogs

Use modals for:

* Confirmation
* Destructive actions
* Important focused interactions

Avoid putting entire workflows inside unnecessary modals.

---

# 39. Icons

Icons should come from a consistent icon library.

Icons must:

* Have consistent stroke/weight
* Have appropriate size
* Include accessible labels when necessary

Do not use emojis as primary interface icons.

---

# 40. Motion Philosophy

Motion is a core part of VerseVault's experience.

However:

> **Motion should communicate, not decorate.**

Animations should answer at least one of:

* What changed?
* Where did this come from?
* What should I focus on?
* Did my action succeed?
* What can I interact with?

---

# 41. Motion Library

VerseVault will use **Motion for React** for interface animation.

Animations should be centralized into reusable patterns rather than manually recreated throughout pages.

Potential motion primitives:

```text
FadeIn
FadeUp
FadeDown
ScaleIn
SlideIn
PageTransition
StaggerContainer
StaggerItem
SharedLayout
ModalTransition
ToastTransition
```

---

# 42. Motion Timing

Suggested baseline:

```text
Micro interaction     120–180ms
Small component       180–250ms
Standard transition   250–350ms
Large transition      350–500ms
Hero animation        500–900ms
```

Animations should generally use smooth easing rather than linear motion.

---

# 43. Motion Hierarchy

Not every element should animate.

Priority:

```text
Page-level
    ↓
Major section
    ↓
Component
    ↓
Micro interaction
```

If everything moves simultaneously, nothing feels important.

---

# 44. Page Transitions

Page transitions should be:

* Short
* Smooth
* Subtle

They should never make navigation feel slow.

Avoid long cinematic transitions between ordinary application pages.

---

# 45. Staggered Animation

Staggering may be used for:

* Hero content
* Work grids
* Search results
* Navigation menus

Example:

```text
Heading
  ↓ 80ms
Description
  ↓ 80ms
CTA
```

The delay should remain subtle.

---

# 46. Scroll Motion

Scroll-triggered animation may be used on marketing pages.

Examples:

* Section reveals
* Image movement
* Text entrance
* Parallax-like effects

Avoid excessive scroll hijacking.

Normal browser scrolling must remain intact.

---

# 47. Hover Motion

Hover states may include:

* Slight elevation
* Subtle translation
* Border transition
* Image scaling
* Color transition

Hover animation should never communicate information that is unavailable to touch users.

---

# 48. Button Motion

Buttons may use:

* Subtle scale
* Icon movement
* Background transitions

Pressed states should feel tactile.

Avoid exaggerated bouncing.

---

# 49. Reduced Motion

The application must respect:

```text
prefers-reduced-motion
```

When reduced motion is enabled:

* Remove unnecessary movement.
* Reduce transition distances.
* Avoid parallax.
* Avoid large entrance animations.
* Preserve functional state changes.

---

# 50. Animation Performance

Animations should prioritize GPU-friendly properties.

Prefer:

```text
transform
opacity
```

Avoid animating expensive layout properties unnecessarily.

Motion must not cause:

* Layout instability
* Noticeable frame drops
* Excessive CPU usage
* Input lag

---

# 51. Responsive Motion

Motion should adapt to device context.

For example:

Desktop:

```text
Large entrance animation
```

Mobile:

```text
Smaller / simpler entrance animation
```

Mobile performance should take priority.

---

# 52. Accessibility

All animated interactions must remain usable without animation.

Focus states must remain visible.

Keyboard users must receive equivalent interaction feedback.

---

# 53. Responsive Breakpoints

Use a small number of meaningful breakpoints.

Conceptual system:

```text
Mobile
Tablet
Desktop
Large Desktop
```

Exact pixel values should be centralized in the styling system.

Components should primarily respond to available space rather than relying exclusively on global breakpoints.

---

# 54. Mobile Design Philosophy

Mobile is a first-class experience.

The mobile interface should not be treated as:

> Desktop UI but smaller.

Instead:

```text
Desktop
Complex navigation
Multiple columns
Hover interactions

Mobile
Focused navigation
Single-column content
Touch interactions
Simplified controls
```

---

# 55. Accessibility Color Rules

Never communicate state using color alone.

For example:

Bad:

```text
Red = error
Green = success
```

Better:

```text
Icon
+
Text
+
Color
```

---

# 56. Content Hierarchy

Across VerseVault, visual hierarchy should generally follow:

```text
Primary content
     ↓
Secondary information
     ↓
Metadata
     ↓
Actions
```

The writing itself should always remain visually dominant on reading pages.

---

# 57. Design Token Architecture

Design tokens should be centralized.

Conceptually:

```text
tokens/
│
├── colors
├── typography
├── spacing
├── radius
├── shadows
├── breakpoints
└── motion
```

Components should consume these tokens.

Avoid scattering visual constants across components.

---

# 58. Component Architecture

The UI should eventually contain reusable components such as:

```text
Button
Input
Avatar
Badge
Card
Modal
Toast
Dropdown
Tabs
Tooltip
Skeleton
Pagination
WorkCard
AuthorCard
Comment
Editor
Navbar
Footer
Search
```

Higher-level components should be composed from lower-level primitives.

---

# 59. Component States

Every interactive component should consider:

```text
Default
Hover
Focus
Pressed
Disabled
Loading
Error
Success
```

Not every component needs every state, but applicable states must be designed.

---

# 60. Design Consistency Rules

Do not introduce:

* Random font sizes
* Random colors
* Random corner radii
* One-off shadows
* Arbitrary spacing
* Unnecessary animations
* Inconsistent icon styles

If a new visual pattern is needed repeatedly, promote it into the design system.

---

# 61. Design Review Checklist

Before considering a page complete:

### Visual

* Is hierarchy clear?
* Is spacing consistent?
* Are typography choices intentional?
* Are colors semantic?
* Does the page feel like VerseVault?

### UX

* Is the primary action obvious?
* Are loading states present?
* Are error states present?
* Are empty states useful?

### Responsive

* Mobile
* Tablet
* Desktop

### Accessibility

* Keyboard
* Focus
* Contrast
* Labels
* Reduced motion

### Performance

* Image optimization
* Animation performance
* No unnecessary client-side work

---

# 62. Design North Star

Every design decision should ultimately answer:

> **Does this make VerseVault a better place to write, read, or discover?**

If not, it probably doesn't belong.

---

# 63. Next Stage

After the design system is established, the next technical planning document is:

```text
ARCHITECTURE.md
```

This document will define:

```text
Frontend
     ↓
API
     ↓
Backend
     ↓
Authentication
     ↓
MongoDB
     ↓
External Services
     ↓
Deployment
```

Only after that should we finalize:

```text
DATABASE.md
API.md
```

**End of Design System**
