# Modern UI/UX Patterns (2025-2026)

## Source Projects

This skill is based on the most starred open-source UI libraries (verified May 2026):

| Library | Stars | Focus | URL |
|---------|-------|-------|-----|
| shadcn/ui | 114k | Core functional components | https://ui.shadcn.com |
| Headless UI | 28k | Unstyled accessible primitives | https://headlessui.com |
| Magic UI | 21k | Animated/visual effects | https://magicui.design |
| HyperUI | 12k | Pre-built Tailwind sections | https://www.hyperui.dev |
| tweakcn | 9.8k | Customizable shadcn variants | https://tweakcn.com |
| react-native-reusables | 8.3k | shadcn for React Native | https://reusables.foundersclub.com |
| 21st | 5.2k | Modern component patterns | https://21st.dev |

---

## 1. Core Functional Components (shadcn/ui pattern)

**When to use:** Any interactive UI element - forms, dialogs, navigation, data display.

### Essential Components (shadcn/ui inventory):
- **Accordion** - Collapsible content sections
- **Alert / Alert Dialog** - Notifications and confirmations
- **Avatar** - User profile images
- **Badge** - Status indicators, tags, labels
- **Breadcrumb** - Navigation hierarchy
- **Button** - Primary actions (with variants: default, destructive, outline, secondary, ghost, link)
- **Calendar** - Date selection
- **Card** - Content containers with header, content, footer
- **Checkbox / Radio Group** - Selection controls
- **Combobox** - Search + select hybrid
- **Command** - Command palette (⌘K pattern)
- **Context Menu / Dropdown Menu** - Action menus
- **Dialog / Sheet** - Modal overlays and side panels
- **Form** - Validated input groups (with react-hook-form + zod)
- **Hover Card** - Rich tooltips
- **Input** - Text fields, search bars
- **Label** - Form field labels
- **Menubar / Navigation Menu** - Application navigation
- **Pagination** - Data list navigation
- **Popover** - Floating content panels
- **Progress** - Loading indicators, progress bars
- **Select** - Dropdown selection
- **Separator** - Visual dividers
- **Skeleton** - Loading placeholders
- **Slider** - Range selection
- **Switch / Toggle** - Binary state controls
- **Table** - Data grids with sorting, filtering
- **Tabs** - Content organization
- **Textarea** - Multi-line input
- **Toast** - Transient notifications (sonner)
- **Tooltip** - Hover explanations

### Pattern: Component Composition
```tsx
// Card pattern - shadcn/ui
<Card>
  <CardHeader>
    <CardTitle>Heading</CardTitle>
    <CardDescription>Description</CardDescription>
  </CardHeader>
  <CardContent>Content here</CardContent>
  <CardFooter>Actions</CardFooter>
</Card>
```

### Pattern: Form with Validation
```tsx
// Form pattern - shadcn/ui + react-hook-form + zod
const form = useForm<z.infer<typeof schema>>({ resolver: zodResolver(schema) });
<FormField control={form.control} name="email" render={({ field }) => (
  <FormItem>
    <FormLabel>Email</FormLabel>
    <FormControl><Input placeholder="you@example.com" {...field} /></FormControl>
    <FormMessage />
  </FormItem>
)} />
```

### Pattern: Data Table
```tsx
// Table pattern - tanstack table + shadcn
<Table>
  <TableHeader><TableRow><TableHead>Column</TableHead></TableRow></TableHeader>
  <TableBody><TableRow><TableCell>Data</TableCell></TableRow></TableBody>
</Table>
```

### Color Palette Reference
- **Primary**: hsl(222.2 47.4% 11.2%) - Dark blue-gray
- **Destructive**: hsl(0 72% 51%) - Red
- **Muted**: hsl(210 40% 96%) - Light gray
- **Accent**: hsl(210 40% 96%) - Subtle highlight
- **Border**: hsl(214.3 31.8% 91.4%) - Subtle divider

---

## 2. Animated & Visual Effects (Magic UI pattern)

**When to use:** Landing pages, hero sections, feature showcases, marketing content, dashboard highlights.

### Hero & Layout Effects
- **Bento Grid** - Asymmetric grid layouts (Apple-style feature showcase)
- **Hero Video Dialog** - Video hero with modal playback
- **Dock** - macOS-style expanding dock navigation
- **Animated List** - Staggered list animations
- **Marquee** - Infinite scrolling logos/testimonials

### Particle & Background Effects
- **Particles** - Interactive particle backgrounds
- **Ripple** - Water ripple click effects
- **Orbiting Circles** - Circular orbiting animations
- **Globe** - 3D animated globe (WebGL)
- **Aurora Text** - Northern lights text effect
- **Neon Gradient Card** - Glowing card borders
- **Retro Grid** - Perspective grid background
- **Waves** - Animated wave backgrounds

### Text Effects
- **Animated Gradient Text** - Shifting gradient text
- **Blur In / Blur Fade** - Blur reveal animations
- **Typing Animation** - Terminal-style typing
- **Number Ticker** - Animated number counting
- **Letter Pull Up** - Letter-by-letter reveal
- **Word Pull Up** - Word-by-word reveal
- **Text Hover Effect** - Hover text transformation
- **Morphing Text** - Text shape morphing
- **Sparkle Text** - Glowing sparkle effect

### Interactive Effects
- **Animated Beam** - Connection lines between elements
- **Grid Pattern** - Dot/grid background patterns
- **Magic Card** - 3D hover tilt effect
- **Flip Text** - Slot machine text animation
- **Scroll Based Velocity** - Speed-based scroll animation
- **Testimonials** - Animated testimonial carousel
- **App Screen** - Phone mockup with animation

### Loading States
- **Shimmer Button** - Shimmer effect on buttons
- **Border Beam** - Animated border glow
- **Line Shadow Text** - Text with moving shadow
- **Loader 3D** - 3D loading animation

### Pattern: Bento Grid
```tsx
// Bento grid layout - Magic UI
<div className="grid grid-cols-1 md:grid-cols-3 gap-4">
  <BentoGrid className="col-span-2">Feature 1</BentoGrid>
  <BentoGrid>Feature 2</BentoGrid>
  <BentoGrid>Feature 3</BentoGrid>
  <BentoGrid className="col-span-2">Feature 4</BentoGrid>
</div>
```

### Pattern: Animated Section
```tsx
// Blur fade in on scroll - Magic UI
<BlurFade delay={0.25} inView>
  <h2>Section Title</h2>
</BlurFade>
```

### Pattern: Marquee
```tsx
// Infinite scroll logos - Magic UI
<Marquee pauseOnHover className="[--duration:20s]">
  {logos.map((logo) => <img key={logo} src={logo} />)}
</Marquee>
```

---

## 3. Pre-built Sections (HyperUI pattern)

**When to use:** Marketing pages, blog layouts, e-commerce, admin dashboards, quick page assembly.

### Marketing Sections
- **Call to Action** - Hero banners, conversion prompts
- **Features** - Feature grids, benefit lists
- **Hero** - Landing page headers
- **Pricing** - Pricing tables, plan comparison
- **Team** - Team member grids
- **Testimonials** - Social proof sections
- **Newsletter** - Email signup sections
- **Statistics** - Number/metric displays

### Application UI
- **Application UI** - Dashboard layouts, admin panels
- **Ecommerce** - Product cards, carts, checkout
- **Blog** - Post lists, article layouts
- **Headers / Footers** - Navigation and footer sections

### Neobrutalism Style
- Bold borders, vibrant colors, shadow effects
- Trendy aesthetic for modern startups
- High contrast, raw aesthetic

---

## 4. 2025-2026 UI Trends (Synthesized from all sources)

### Glass Morphism
- Frosted glass backgrounds (backdrop-filter: blur)
- Subtle transparency with layered depth
- Soft shadows, light borders

### Bento Grid Layouts
- Asymmetric card grids (popularized by Apple, Linear)
- Variable card sizes for visual hierarchy
- Clean spacing, rounded corners

### Micro-interactions
- Hover effects with subtle transforms
- Button press animations (scale down 0.98)
- Loading states with skeleton screens
- Smooth transitions (150-300ms)

### Dark Mode First
- Dark theme as default, light as variant
- Careful contrast ratios (WCAG AA)
- Color system based on HSL variables

### Animated Gradients
- Shifting background gradients
- Text with gradient fills
- Subtle motion, not distracting

### Command Palette UI
- ⌘K / Ctrl+K pattern for navigation
- Search-first interface
- Fuzzy matching, keyboard navigation

### Skeleton Loading States
- Content-shaped placeholders
- Shimmer animation effect
- Better UX than spinners

---

## 5. UX Best Practices

### Accessibility
- ARIA labels on interactive elements
- Keyboard navigation support (Tab, Enter, Escape)
- Color contrast ratio ≥ 4.5:1 (WCAG AA)
- Focus indicators visible
- Screen reader compatible

### Responsive Design
- Mobile-first breakpoints: sm(640), md(768), lg(1024), xl(1280), 2xl(1536)
- Touch targets ≥ 44px
- Fluid typography with clamp()
- Container max-widths for readability

### Performance
- Lazy load below-fold images/components
- Code split heavy animated components
- Use CSS transforms over layout properties
- Prefer will-change for animated elements
- Debounce scroll/resize handlers

### User Feedback
- Every action has visual feedback
- Loading states for async operations
- Error states with recovery options
- Success confirmations for destructive actions
- Toast notifications for system events

### Form UX
- Inline validation on blur
- Clear error messages near fields
- Auto-focus first field
- Preserve form state on navigation
- Show password toggle
- Input masks for formatted fields

---

## Implementation Notes for RankBloom/The Company

### For Marketing/Blog Pages:
- Use **Magic UI** patterns: bento grids, marquee, blur fade, animated gradients
- Hero sections: animated gradient text + bento feature grid + marquee logos
- Blog cards: skeleton loading + hover effects + reading time badges
- Testimonials: marquee scroll + star ratings

### For Dashboard/Admin:
- Use **shadcn/ui** patterns: data tables, dialogs, forms, toast notifications
- Navigation: sidebar + command palette (⌘K)
- Data display: cards with skeleton loading, charts
- Forms: validated with zod + react-hook-form pattern
- Status: badge + color-coded indicators

### For Mobile (React Native):
- Use **react-native-reusables** patterns (shadcn equivalent for RN)
- Navigation: bottom tabs + stack
- Components: adapted from shadcn patterns with RN primitives

### Color System (CSS Variables)
```css
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  --card: 0 0% 100%;
  --card-foreground: 222.2 84% 4.9%;
  --primary: 222.2 47.4% 11.2%;
  --primary-foreground: 210 40% 98%;
  --muted: 210 40% 96.1%;
  --muted-foreground: 215.4 16.3% 46.9%;
  --border: 214.3 31.8% 91.4%;
  --ring: 222.2 84% 4.9%;
  --radius: 0.5rem;
}
```

---

## Quick Reference: Which Library for What?

| Need | Library | Why |
|------|---------|-----|
| Form inputs, dialogs, tables | shadcn/ui | Most complete, accessible, production-ready |
| Animated hero sections | Magic UI | Best-in-class visual effects |
| Marketing page sections | HyperUI | Ready-made Tailwind blocks |
| Unstyled components | Headless UI | Tailwind Labs, accessible, customizable |
| React Native components | react-native-reusables | shadcn pattern for mobile |
| Quick component variants | tweakcn | Pre-styled shadcn variations |
| Component inspiration | 21st.dev | Curated component showcase |
