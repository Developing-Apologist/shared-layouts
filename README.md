# Shared Layouts for Eleventy

This repository contains reusable layouts and components for Eleventy sites using Nunjucks templating. It's designed to be used as a Git submodule across multiple sites like `developingapologist.com`, `blog.developingapologist.com` and `talks.developingapologist.com`.

## Structure

```
shared-layouts/
├── includes/
│   ├── components/
│   │   ├── navbar.njk          # Responsive navigation component
│   │   └── footer.njk          # Footer component
│   └── layouts/
│       ├── base.njk            # Unified base layout template
│       ├── site-configs.njk    # Site configuration macros
├── README.md
└── .eleventyignore
```

## Installation as Git Submodule

Add this repository as a submodule to your Eleventy project:

```bash
# Add the submodule
git submodule add https://github.com/developing-apologist/shared-layouts.git src/_includes/shared

# Initialize and update the submodule
git submodule update --init --recursive
```

## Configuration

### 1. Update Eleventy Configuration

In your `.eleventy.js` file:

```javascript
const { DateTime } = require("luxon");

module.exports = function(eleventyConfig) {
  // Add Luxon date filter for footer component
  eleventyConfig.addFilter("date", function(date, format) {
    return DateTime.fromJSDate(date).toFormat(format);
  });
  
  // Copy static assets
  eleventyConfig.addPassthroughCopy("src/assets");
  eleventyConfig.addPassthroughCopy("src/css");
  eleventyConfig.addPassthroughCopy("CNAME");
  eleventyConfig.addPassthroughCopy("_redirects");
  
  // Add shared layouts to includes path
  eleventyConfig.addPassthroughCopy("src/_includes/shared/includes");

  // Watch CSS files for changes
  eleventyConfig.addWatchTarget("src/css/");

  return {
    dir: {
      input: "src",
      output: "_site",
      includes: "_includes",
      layouts: "_includes"
    },
    templateFormats: ["njk", "md", "html"],
    markdownTemplateEngine: "njk",
    htmlTemplateEngine: "njk",
    dataTemplateEngine: "njk",
    nunjucksOptions: {
      // Add shared includes to the search path
      searchPaths: [
        "src/_includes",
        "src/_includes/shared/includes"
      ]
    }
  };
};
```

### 2. Install Required Plugin

The footer component uses date formatting. Install Luxon directly:

```bash
npm install luxon
```

Then add it to your `.eleventy.js`:

```javascript
const { DateTime } = require("luxon");

module.exports = function(eleventyConfig) {
  // Add Luxon date filter
  eleventyConfig.addFilter("date", function(date, format) {
    return DateTime.fromJSDate(date).toFormat(format);
  });
  
  // ... rest of your configuration
};
```

**Note:** Luxon provides the date formatting functionality. The `{{ "now" | date("yyyy") }}` filter in the footer component will automatically update the copyright year.

## Unified Base Layout

The `base.njk` layout provides a unified, data-driven approach for all your sites. It uses a `site_data` object to configure navigation, footer, and metadata.

### Features

- **Data-driven configuration**: Uses a `site_data` object to configure navigation, footer, and metadata
- **Flexible metadata**: Supports both page-level and site-level metadata
- **Component-based**: Uses shared navbar and footer components

### Usage

Set up your `site_data` object in your page or layout:

```njk
{% set site_data = {
    logo_image: "/assets/developing-apologist-logo-v2.png",
    logo_alt: "The Developing Apologist",
    site_title: "The Developing Apologist",
    show_wip_badge: true,
    nav_links: [
        { href: "/", label: "Home", active: page.url == "/" },
        { href: "/about/", label: "About", active: page.url == "/about/" },
        { href: "https://blog.developingapologist.com", label: "Blog", active: false },
        { href: "https://talks.developingapologist.com", label: "Presentations", active: false }
    ],
    site_description: "Equipping Christian software developers to defend their faith through logical, systematic apologetics that bridges faith and reason.",
    quick_links_title: "Quick Links",
    show_quick_links: true,
    show_resources: false
} %}

{% extends "shared/layouts/base.njk" %}
```

### Site Data Configuration

The `site_data` object supports the following properties:

#### Required Properties
- `site_title`: The main site title
- `nav_links`: Array of navigation links

#### Optional Properties
- `logo_image`: Path to the logo image
- `logo_alt`: Alt text for the logo
- `show_wip_badge`: Whether to show the "Work in Progress" badge
- `site_description`: Site description for meta tags
- `quick_links_title`: Title for the quick links section
- `show_quick_links`: Whether to show quick links in footer
- `show_resources`: Whether to show resources in footer
- `resources`: Array of resource links
- `quick_links`: Array of quick links for footer
- `social_links`: Array of social media links

### Page-Level Overrides

You can override site-level settings with page-level variables:

```njk
---
title: "Custom Page Title"
description: "Custom page description"
og_image: "/custom-og-image.jpg"
---

{% extends "shared/layouts/base.njk" %}
```

## Site Configuration Macros

The `site-configs.njk` file contains Nunjucks macros that provide ready-to-use configurations for each of your sites.

### What are Macros?

Nunjucks macros are reusable template functions. In this case, they serve as **configuration templates** that you can either copy directly or import and use programmatically.

### Available Macros

#### `website_config()`
Configuration for the main website with about page and external links.

#### `blog_config()`
Configuration for the blog site with RSS feed and tag/category resources.

#### `talks_config()`
Configuration for the talks/presentations site with social links and talk-specific quick links.

### How to Use the Macros

**Option 1: Import and Call**
```njk
{% from "shared/layouts/site-configs.njk" import website_config %}
{% set site_data = website_config() %}
{% extends "shared/layouts/base.njk" %}
```

**Option 2: Copy the Configuration**
```njk
{% set site_data = {
    logo_image: "/assets/developing-apologist-logo-v2.png",
    site_title: "The Developing Apologist",
    nav_links: [...]
} %}
{% extends "shared/layouts/base.njk" %}
```

### Key Differences Between Site Configurations

| Site | Active Nav | Resources | Quick Links | Social Links |
|------|------------|-----------|-------------|--------------|
| **Website** | Home/About | ❌ | Basic | ❌ |
| **Blog** | Blog | ✅ RSS/Tags/Categories | Basic | ❌ |
| **Talks** | Presentations | ❌ | Talk-specific | ✅ GitHub/Twitter |

## Legacy Component Usage

### Using Individual Components

You can still use the navbar and footer components individually if needed:

#### Navbar Component (`navbar.njk`)

The navbar component accepts these variables:

##### Required Variables
- `nav_links`: Array of navigation link objects

##### Optional Variables
- `site_title`: The site title displayed next to the logo (default: "Site Title")
- `logo_image`: Path to the logo image (if not provided, shows default SVG)
- `logo_alt`: Alt text for the logo image (default: "Site Logo")
- `show_wip_badge`: Boolean to show/hide the "Work in Progress" badge (default: false)

##### Navigation Link Structure
Each link object should contain:
- `label` (required): The text to display for the link
- `href` (required): The URL the link should point to
- `active` (optional): Boolean to indicate if this is the current page

Example:
```javascript
[
  {
    label: "Home",
    href: "/",
    active: page.url == "/"  // Dynamic active state detection
  },
  {
    label: "About",
    href: "/about/",
    active: page.url == "/about/"
  },
  {
    label: "External Link",
    href: "https://example.com",
    active: false  // External links should be false
  }
]
```

#### Footer Component (`footer.njk`)

The footer component accepts these variables:

##### Optional Variables
- `site_title`: The site title displayed next to the logo (default: "Site Title")
- `logo_image`: Path to the logo image (if not provided, shows default SVG)
- `logo_alt`: Alt text for the logo image (default: "Site Logo")
- `site_description`: Description text displayed under the logo
- `show_wip_badge`: Boolean to show/hide the "Work in Progress" badge (default: false)
- `nav_links`: Array of navigation links (used for quick links section)
- `show_quick_links`: Boolean to show/hide the quick links section (default: true)
- `quick_links_title`: Title for the quick links section (default: "Quick Links")
- `resources`: Array of resource link objects
- `show_resources`: Boolean to show/hide the resources section (default: true)
- `resources_title`: Title for the resources section (default: "Resources")
- `copyright_text`: Custom copyright text (default: uses site_title)

**Note:** The footer uses `{{ "now" | date("yyyy") }}` for the copyright year, which requires Luxon to be installed and configured as a date filter.

##### Link Structure
The `resources` array should contain objects with:
- `label` (required): The text to display for the link
- `href` (required): The URL the link should point to

Example:
```javascript
{
  resources: [
    { href: "https://blog.example.com", label: "Developer Blog" },
    { href: "https://talks.example.com", label: "Presentations" }
  ]
}
```

**Note:** Quick links automatically use the same `nav_links` data as the navbar component.

### Using Both Components Together

You can configure both navbar and footer with the same data structure:

```njk
<!-- Navigation and Footer Configuration -->
{% set site_data = {
    logo_image: "/assets/developing-apologist-logo-v2.png",
    logo_alt: "The Developing Apologist",
    site_title: "The Developing Apologist",
    site_description: "Equipping Christian software developers to defend their faith through logical, systematic apologetics that bridges faith and reason.",
    show_wip_badge: true,
    nav_links: [
        { href: "/", label: "Home", active: page.url == "/" },
        { href: "/about/", label: "About", active: page.url == "/about/" },
        { href: "https://blog.developingapologist.com", label: "Blog", active: false },
        { href: "https://talks.developingapologist.com", label: "Presentations", active: false }
    ],
    resources: [
        { href: "https://blog.developingapologist.com", label: "Developer Apologetics Blog" },
        { href: "https://talks.developingapologist.com", label: "Technical Presentations" }
    ],
    quick_links_title: "Quick Links",
    resources_title: "Resources",
    show_quick_links: true,
    show_resources: true
} %}

<!-- Set variables for components -->
{% set logo_image = site_data.logo_image %}
{% set logo_alt = site_data.logo_alt %}
{% set site_title = site_data.site_title %}
{% set site_description = site_data.site_description %}
{% set show_wip_badge = site_data.show_wip_badge %}
{% set nav_links = site_data.nav_links %}
{% set resources = site_data.resources %}
{% set quick_links_title = site_data.quick_links_title %}
{% set resources_title = site_data.resources_title %}
{% set show_quick_links = site_data.show_quick_links %}
{% set show_resources = site_data.show_resources %}

<!-- Include components -->
{% include "shared/includes/components/navbar.njk" %}
{% include "shared/includes/components/footer.njk" %}
```

## Features

### Navbar Component (`navbar.njk`)
- ✅ Responsive design with mobile hamburger menu
- ✅ Custom color scheme (logo-steel, logo-circuit, logo-orange, etc.)
- ✅ Configurable logo and site title
- ✅ Optional "Work in Progress" badge
- ✅ Active link highlighting
- ✅ Accessible with proper ARIA attributes
- ✅ Smooth transitions and hover effects
- ✅ Sticky positioning with z-index

### Footer Component (`footer.njk`)
- ✅ Responsive grid layout
- ✅ Brand section with logo and description
- ✅ Configurable quick links section
- ✅ Configurable resources section
- ✅ Optional "Work in Progress" badge
- ✅ Dynamic copyright year
- ✅ Consistent styling with navbar

### Base Layout (`base.njk`)
- ✅ Unified data-driven configuration
- ✅ Includes both navbar and footer components
- ✅ Tailwind CSS integration
- ✅ SEO meta tags support
- ✅ Open Graph tags
- ✅ Content block for page-specific content
- ✅ Additional head and scripts blocks

## Migration Guide

### From Website Layout
1. Replace the hardcoded `site_data` object with your configuration
2. Update the layout path to use the shared layout
3. Remove any duplicate navbar/footer includes

### From Blog/Talks Layout
1. Create a `site_data` object with your site configuration
2. Update the layout path to use the shared layout
3. Move hardcoded navigation to the `nav_links` array

## Customization

### Custom Color Scheme
Both components use custom CSS variables for colors. Make sure your site includes these CSS custom properties:

```css
:root {
  --color-logo-steel: #2d3748;
  --color-logo-circuit: #4a5568;
  --color-logo-orange: #ed8936;
  --color-logo-blue: #3182ce;
  --color-logo-glow: #63b3ed;
  --color-vs-fg: #d4d4d4;
}
```

Or define them in your Tailwind config:

```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        'logo-steel': '#2d3748',
        'logo-circuit': '#4a5568',
        'logo-orange': '#ed8936',
        'logo-blue': '#3182ce',
        'logo-glow': '#63b3ed',
        'vs-fg': '#d4d4d4',
      }
    }
  }
}
```

### Replacing the Logo
Update the `logo_image` and `logo_alt` in your site_data:

```njk
{% set site_data = {
    logo_image: "/images/your-logo.png",
    logo_alt: "Your Site Name",
    // ... other options
} %}
```

### Customizing the Work in Progress Badge
To show the badge, set `show_wip_badge: true` in your site_data. To hide it, set it to `false`.

## Updating the Submodule

To update the shared layouts in your project:

```bash
# Update the submodule to the latest version
git submodule update --remote

# Commit the update
git add src/_includes/shared
git commit -m "Update shared layouts submodule"
```

## Contributing

To contribute to the shared layouts:

1. Fork this repository
2. Make your changes
3. Test in a local Eleventy project
4. Submit a pull request

## License

This project is licensed under the MIT License.

## Cursor Prompt for Implementation

Here's a ready-to-use Cursor prompt to implement these shared layouts in your Eleventy project:

```
I want to implement shared layouts for my Eleventy site using a Git submodule. Here's what I need:

1. Add the shared-layouts repository as a Git submodule at `src/_includes/shared`
2. Install Luxon for date formatting: `npm install luxon`
3. Update my .eleventy.js configuration to include Luxon date filter and shared layouts
4. Create a site_data configuration in my layout that includes:
   - Logo image and alt text
   - Site title and description
   - Navigation links with dynamic active state detection
   - Resources links for the footer
   - Work in progress badge toggle
   - Customizable section titles and visibility

The shared layouts include:
- A unified base layout with data-driven configuration
- Site configuration macros for easy setup
- A responsive navbar component with mobile hamburger menu
- A footer component with brand section, quick links (using nav data), and resources
- Custom color scheme using logo-steel, logo-circuit, logo-orange, etc.

Please help me implement this step by step, starting with the Git submodule setup and ending with a working configuration that matches the documentation at https://github.com/developing-apologist/shared-layouts
```