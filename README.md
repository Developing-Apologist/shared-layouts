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
│       └── base.njk            # Base layout template
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
module.exports = function(eleventyConfig) {
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

### 2. Using the Navbar Component

Use this pattern in your layout files for automatic active state detection:

```njk
<!-- Navigation -->
{% set nav_data = {
    logo_image: "/assets/developing-apologist-logo-v2.png",
    logo_alt: "The Developing Apologist",
    site_title: "The Developing Apologist",
    show_wip_badge: true,
    nav_links: [
        { href: "/", label: "Home", active: page.url == "/" },
        { href: "/about/", label: "About", active: page.url == "/about/" },
        { href: "https://blog.developingapologist.com", label: "Blog", active: false },
        { href: "https://talks.developingapologist.com", label: "Presentations", active: false }
    ]
} %}

{% set logo_image = nav_data.logo_image %}
{% set logo_alt = nav_data.logo_alt %}
{% set site_title = nav_data.site_title %}
{% set show_wip_badge = nav_data.show_wip_badge %}
{% set nav_links = nav_data.nav_links %}
{% include "shared/includes/components/navbar.njk" %}
```

### 3. Using the Base Layout

In your page templates:

```njk
---
layout: shared/layouts/base.njk
title: "My Page Title"
description: "Page description for SEO"
---

{% block content %}
<div class="max-w-4xl mx-auto px-4 py-8">
  <h1 class="text-3xl font-bold text-gray-900 mb-6">
    Welcome to My Site
  </h1>
  
  <p class="text-gray-600">
    This is my page content.
  </p>
</div>
{% endblock %}
```

## Component Configuration

### Navbar Component (`navbar.njk`)

The navbar component accepts these variables:

#### Required Variables
- `nav_links`: Array of navigation link objects

#### Optional Variables
- `site_title`: The site title displayed next to the logo (default: "Site Title")
- `logo_image`: Path to the logo image (if not provided, shows default SVG)
- `logo_alt`: Alt text for the logo image (default: "Site Logo")
- `show_wip_badge`: Boolean to show/hide the "Work in Progress" badge (default: false)

#### Navigation Link Structure
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

### Footer Component (`footer.njk`)

The footer component accepts these variables:

#### Optional Variables
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

#### Link Structure
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

## Using Both Components Together

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
- ✅ Includes both navbar and footer components
- ✅ Tailwind CSS integration
- ✅ SEO meta tags support
- ✅ Open Graph tags
- ✅ Content block for page-specific content
- ✅ Additional head and scripts blocks

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