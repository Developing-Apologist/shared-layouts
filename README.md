# Shared Layouts for Eleventy

This repository contains reusable layouts and components for Eleventy sites using Nunjucks templating. It's designed to be used as a Git submodule across multiple sites like `blog.developingapologist.com` and `talks.developingapologist.com`.

## Structure

```
shared-layouts/
├── includes/
│   ├── components/
│   │   └── navbar.njk          # Responsive navigation component
│   └── layouts/
│       └── base.njk            # Base layout template
├── README.md
└── .eleventyignore
```

## Installation as Git Submodule

To add this repository as a submodule to your Eleventy project:

```bash
# Add the submodule
git submodule add https://github.com/your-username/shared-layouts.git _includes/shared

# Initialize and update the submodule
git submodule update --init --recursive
```

## Configuration

### 1. Update Eleventy Configuration

In your `.eleventy.js` file, add the shared layouts directory to the includes path:

```javascript
module.exports = function(eleventyConfig) {
  // Add shared layouts to includes path
  eleventyConfig.addPassthroughCopy("_includes/shared/includes");
  
  return {
    dir: {
      input: ".",
      output: "_site",
      includes: "_includes",
      layouts: "_layouts"
    },
    templateFormats: ["njk", "md", "html"],
    nunjucksOptions: {
      // Add shared includes to the search path
      searchPaths: [
        "_includes",
        "_includes/shared/includes"
      ]
    }
  };
};
```

### 2. Configure Navigation Links

In your Eleventy data file (e.g., `_data/navigation.js`) or front matter, define your navigation links:

```javascript
// _data/navigation.js
module.exports = [
  {
    label: "Home",
    href: "/",
    active: false
  },
  {
    label: "About",
    href: "/about/",
    active: false
  },
  {
    label: "Blog",
    href: "https://blog.developingapologist.com",
    active: false
  },
  {
    label: "Presentations",
    href: "https://talks.developingapologist.com",
    active: false
  }
];
```

### 3. Using the Base Layout

In your page templates, extend the base layout:

```njk
---
layout: shared/layouts/base.njk
title: "My Page Title"
description: "Page description for SEO"
nav_links: navigation
site_title: "The Developing Apologist"
logo_image: "/assets/developing-apologist-logo-v2.png"
logo_alt: "The Developing Apologist"
show_wip_badge: true
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

### 4. Using the Navbar Component Directly

You can also include the navbar component directly in your own layouts:

```njk
{% include "components/navbar.njk" with { 
  nav_links: navigation,
  site_title: "The Developing Apologist",
  logo_image: "/assets/developing-apologist-logo-v2.png",
  logo_alt: "The Developing Apologist",
  show_wip_badge: true
} %}
```

## Navigation Configuration

The `nav_links` array should contain objects with the following properties:

- `label` (required): The text to display for the link
- `href` (required): The URL the link should point to
- `active` (optional): Boolean to indicate if this is the current page

Example:
```javascript
[
  {
    label: "Home",
    href: "/",
    active: true  // This link will be highlighted
  },
  {
    label: "About",
    href: "/about/",
    active: false
  }
]
```

## Navbar Configuration Options

The navbar component accepts several configuration options:

### Required Variables
- `nav_links`: Array of navigation link objects (see above)

### Optional Variables
- `site_title`: The site title displayed next to the logo (default: "Site Title")
- `logo_image`: Path to the logo image (if not provided, shows default SVG)
- `logo_alt`: Alt text for the logo image (default: "Site Logo")
- `show_wip_badge`: Boolean to show/hide the "Work in Progress" badge (default: false)

### Example Configuration
```njk
{% include "components/navbar.njk" with { 
  nav_links: navigation,
  site_title: "The Developing Apologist",
  logo_image: "/assets/developing-apologist-logo-v2.png",
  logo_alt: "The Developing Apologist",
  show_wip_badge: true
} %}
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

### Base Layout (`base.njk`)
- ✅ Includes the navbar component
- ✅ Tailwind CSS integration
- ✅ SEO meta tags support
- ✅ Open Graph tags
- ✅ Content block for page-specific content
- ✅ Footer with dynamic year
- ✅ Additional head and scripts blocks

## Customization

### Custom Color Scheme
The navbar uses custom CSS variables for colors. Make sure your site includes these CSS custom properties:

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
Set the `logo_image` and `logo_alt` variables when including the navbar:

```njk
{% include "components/navbar.njk" with { 
  nav_links: navigation,
  logo_image: "/images/your-logo.png",
  logo_alt: "Your Site Name"
} %}
```

### Customizing the Work in Progress Badge
To show the badge, set `show_wip_badge: true`. To hide it, either omit the variable or set it to `false`.

## Updating the Submodule

To update the shared layouts in your project:

```bash
# Update the submodule to the latest version
git submodule update --remote

# Commit the update
git add _includes/shared
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
