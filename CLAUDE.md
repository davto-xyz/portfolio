# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is David Torres' portfolio website built with Astro 5.12.8, TypeScript, and Tailwind CSS v4. The portfolio showcases his work as a Full Stack Developer with experience in companies like Pikolinos, Roly, Vectalia, and educational platforms. The site is primarily in Spanish with modern, clean design aesthetic.

## Development Commands

- `npm install` - Install dependencies
- `npm run dev` - Start development server at localhost:4321
- `npm run build` - Build production site to ./dist/
- `npm run preview` - Preview build locally before deploying
- `npm run astro ...` - Run Astro CLI commands like `astro add`, `astro check`

**Note**: No linting or testing scripts are currently configured in this project.

## Architecture Overview

### Core Technology Stack
- **Astro 5.12.8**: Static site generator with component islands architecture, configured with TypeScript strict mode and MDX integration
- **Tailwind CSS v4**: PostCSS integration with custom font-stretch utilities and gold color theme (`#F6A60D`)
- **Inter Font**: Professional typography loaded from @fontsource with weights 400-900
- **TypeScript**: Strict configuration extending `astro/tsconfigs/strict`
- **Astro Content Collections**: Type-safe content management with Zod schemas for projects, skills, and about sections
- **Astro Icon**: Icon system with Lucide icons integration
- **Flowbite**: UI component library integration

### Component System Architecture

The codebase follows Astro's component-based architecture with these key patterns:

#### Layout Structure
- **Main Layout** (`src/layouts/Layout.astro`): Spanish language site wrapper with NavBar, slot content, and Footer
- **Global Styles** (`src/styles/global.css`): Inter font imports, Tailwind CSS, and custom gold color utilities

#### Navigation System
- **NavBar Component** (`src/components/NavBar.astro`): Uses `Astro.url.pathname` for active state detection with TypeScript interfaces
- **Active State Logic**: Automatically highlights current page based on URL matching with gold accent underline
- **Responsive Design**: Hidden mobile menu with hamburger button (functionality not implemented)
- **Brand Logo**: Uses Image component with logo.png and hover scale effects

#### Project Display System
- **ProjectsGrid Component** (`src/components/ProjectsGrid.astro`): Complex grid layout with TypeScript interfaces
- **Grid Layout**: CSS Grid with `lg:grid-cols-[2fr_1fr_1fr]` for asymmetric responsive design
- **Project Interface**: Structured data with title, description, image, tag, technologies array, and isMainProject flag

### Page Architecture & Content Strategy

The site follows a clear content separation strategy:

- **Home (`/`)**: Hero section with ProjectsGrid showcase
- **About (`/about`)**: Personal philosophy and values
- **Projects (`/projects`)**: Portfolio AND professional experience timeline
- **Skills (`/skills`)**: Technical skills by category
- **Services (`/services`)**: Service offerings
- **Contact (`/contact`)**: Contact information

### Design System Implementation

#### Typography System
- **Headers**: `font-stretch-ultra-expanded font-black` with gold accent dots (`.`)
- **Custom Font-Stretch**: Tailwind config extends with ultra-condensed to ultra-expanded values
- **Font Loading**: @fontsource/inter with specific weight imports (400-900)

#### Color System
- **Primary Gold**: `#F6A60D` defined in both Tailwind config (`custom-gold`, `gold.500`) and CSS custom properties
- **Color Utilities**: Custom CSS classes in global.css for gold backgrounds, text, borders, and shadows with opacity variants
- **Theme Consistency**: Gold used for accents, CTAs, active states, and project tags throughout
- **Animation System**: Custom entrance animations with reduced motion support

#### Component Patterns
- **Card Design**: White backgrounds with `rounded-2xl`, shadow effects, and hover transforms
- **Button System**: Gold backgrounds with hover states and scale animations
- **Grid System**: Responsive layouts using CSS Grid with span controls

### Asset and Content Management

#### File Structure
- **Components**: Multiple Astro components in `src/components/` following PascalCase naming conventions
- **Pages**: Clear routing structure with home, about, projects, skills, services, and contact pages
- **Assets**: Images in `src/assets/images/` and static files in `public/`
- **Styling**: Centralized in `src/styles/global.css` with Tailwind v4 integration and custom animations
- **Scripts**: Client-side JavaScript in `src/scripts/` (e.g., smoothScroll.js)

#### Content Collections Structure

The project uses Astro Content Collections for type-safe content management with four main collections:

**Projects Collection** (`src/content/projects/`):
```typescript
schema: z.object({
  title: z.string(),
  name: z.string(),
  description: z.string(),
  image: z.string(),
  alt: z.string(),
  tag: z.string(),
  technologies: z.array(z.string()).optional(),
  isMainProject: z.boolean().optional(),
  order: z.number().optional(),
})
```

**Skills Collection** (`src/content/skills/`):
```typescript
schema: z.object({
  category: z.string(),
  description: z.string(),
  subcategories: z.array(z.object({
    title: z.string(),
    skills: z.array(z.object({
      name: z.string(),
      icon: z.string(),
      iconType: z.enum(['url', 'emoji']).optional().default('emoji'),
      color: z.string().optional(),
    }))
  })).optional(),
  skills: z.array(z.object({
    name: z.string(),
    icon: z.string(),
    iconType: z.enum(['url', 'emoji']).optional().default('emoji'),
    color: z.string().optional(),
  })).optional(),
  order: z.number().optional(),
})
```

**About Collection** (`src/content/about/`):
```typescript
schema: z.object({
  title: z.string(),
  subtitle: z.string().optional(),
  description: z.string(),
  highlights: z.array(z.string()).optional(),
})
```

**Experience Collection** (`src/content/experience/`):
```typescript
schema: z.object({
  company: z.string(),
  roles: z.array(z.string()),
  period: z.string(),
  tasks: z.array(z.string()),
  technologies: z.array(z.string()),
  type: z.enum(['full-time', 'freelance', 'contract']),
  order: z.number().optional(),
})
```

Content is written in MDX format allowing rich content with embedded components. Content files are stored in their respective collection directories (`src/content/projects/`, `src/content/skills/`, `src/content/about/`, `src/content/experience/`) and accessed via `getCollection()` API.

## Important Implementation Details

### Grid Layout System
The ProjectsGrid uses a sophisticated CSS Grid layout with `lg:grid-cols-[2fr_1fr_1fr]` creating an asymmetric 3-column layout where the first column is twice the width. Projects with `isMainProject: true` span 2 rows using `lg:row-span-2`.

### Content Access Pattern
All content collections are accessed using Astro's `getCollection()` API with automatic sorting by `order` field or alphabetically by title. Components handle optional properties gracefully with TypeScript interfaces.

### Animation System

The site uses a custom CSS-based animation system with smooth scrolling:

**Smooth Scrolling Implementation** (`src/scripts/smoothScroll.js`):
- **Home Page Only**: Smooth scroll functionality limited to the home page (`/`)
- **Navigation Links**: Only handles anchor links within nav elements
- **Native Smooth Scroll**: Uses `scrollIntoView({ behavior: 'smooth' })` for better performance
- **Astro Integration**: Handles both initial page load and SPA navigation via `astro:page-load` event

**CSS Animation System** (`src/styles/global.css`):
- **Entrance Animations**: Four types - `text-entrance-left`, `text-entrance-bottom`, `text-entrance-fade`, `text-entrance-right`
- **Animation Delays**: Custom delay classes (`.delay-200`, `.delay-400`, `.delay-600`, `.delay-800`)
- **Mobile Menu**: Dedicated animations for mobile menu overlay and slide-in effects
- **Accessibility**: All animations disabled with `@media (prefers-reduced-motion: reduce)`

### Layout Systems

**Bento Grid System** (`src/styles/global.css`):
- **Main Bento Grid**: 12-column responsive grid with auto-sizing rows
- **Grid Classes**: `.bento-large`, `.bento-medium-wide`, `.bento-medium-tall`, `.bento-small`
- **Experience Grid**: Specialized grid for experience section with different sizing patterns
- **Responsive Breakpoints**: Adaptive layouts for mobile (span 12/6), tablet (span 8/6/4), desktop (span 6/4/3)

### Performance Optimizations
- Images use lazy loading and async decoding
- Font loading optimized with specific weight imports (400, 700, 900)
- Native smooth scrolling for better performance
- Reduced motion support: All animations disabled with `@media (prefers-reduced-motion: reduce)`
- CSS-based animations avoid JavaScript overhead

## Key Development Patterns

### Content-Driven Architecture
- All content is managed through Astro Content Collections in `src/content/config.ts`
- Content files are MDX format, allowing rich content with embedded components
- Collections support ordering via `order` frontmatter field for custom sorting
- Components use `getCollection()` for type-safe content access with automatic validation

### Component Prop Interfaces
Components define TypeScript interfaces for props (e.g., `ProjectsGrid` has `Props` interface with `limit` and `className`)
- Optional props have default values destructured in component frontmatter
- Consistent naming: PascalCase for components, camelCase for props

### Grid System Implementation
The `ProjectsGrid` uses CSS Grid with sophisticated responsive layouts:
- Desktop: `lg:grid-cols-[2fr_1fr_1fr]` creates asymmetric 3-column layout
- Main projects: `isMainProject: true` spans 2 rows with `lg:row-span-2`
- Bento Grid: Uses the custom bento grid system with `.bento-*` classes for flexible layouts

### Navigation State Management
NavBar component uses `Astro.url.pathname` for active state detection:
- Active links get gold accent underline with conditional CSS classes
- Hover states use scale transforms and color transitions
- Mobile navigation structure exists but functionality not implemented

### Styling Architecture
- **Global Styles**: Centralized in `src/styles/global.css` with Tailwind imports
- **Custom Gold Colors**: Defined both in Tailwind config and CSS custom properties
- **Animation Classes**: Custom entrance animations with reduced motion support
- **Font System**: Inter font with specific weights (400, 700, 900) loaded via @fontsource