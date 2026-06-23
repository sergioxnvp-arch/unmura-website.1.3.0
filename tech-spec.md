# Technical Specification — Fakultas Akuntansi dan Bisnis Digital

## Dependencies

### Production (package.json dependencies)
| Package | Version | Purpose |
|---------|---------|---------|
| react | ^19.0.0 | UI framework |
| react-dom | ^19.0.0 | DOM renderer |
| react-router-dom | ^7.0.0 | Client-side routing |
| framer-motion | ^12.0.0 | Animations, scroll reveal, transitions |
| lucide-react | ^0.460.0 | Icon library |
| date-fns | ^4.0.0 | Date formatting for calendar, schedule |
| @react-google-maps/api | ^2.20.0 | Google Maps embed (Location section) |
| class-variance-authority | ^0.7.0 | Component variant styling (shadcn dependency) |
| clsx | ^2.1.0 | Conditional classnames |
| tailwind-merge | ^2.6.0 | Tailwind class deduplication |

### Development
| Package | Version | Purpose |
|---------|---------|---------|
| vite | ^7.0.0 | Build tool |
| @vitejs/plugin-react | ^4.0.0 | React plugin for Vite |
| typescript | ^5.6.0 | Type safety |
| tailwindcss | ^3.4.0 | Utility CSS |
| postcss | ^8.4.0 | CSS processing |
| autoprefixer | ^10.4.0 | CSS vendor prefixes |
| @types/react | ^19.0.0 | React type definitions |
| @types/react-dom | ^19.0.0 | ReactDOM type definitions |

---

## Component Inventory

### shadcn/ui Components (Built-in)
| Component | Source | Customization |
|-----------|--------|---------------|
| Button | shadcn | Yellow accent variant, glow shadow |
| Card | shadcn | Rounded-2xl, custom shadow tokens |
| Input | shadcn | Yellow focus ring |
| Badge | shadcn | Blue, Yellow, Slate variants |
| Dialog | shadcn | For popup/modal content |
| Sheet | shadcn | Mobile navigation drawer |
| Separator | shadcn | Divider lines |
| ScrollArea | shadcn | Scrollable content areas |
| Table | shadcn | Schedule table base |
| Tabs | shadcn | Filter tabs for schedule |
| Select | shadcn | Filter dropdowns |
| Textarea | shadcn | Contact/PMB forms |
| Form | shadcn | Form validation wrapper |

### Custom Components

| Component | Purpose | File |
|-----------|---------|------|
| Header | Fixed nav with scroll shrink, glassmorphism | `sections/Header.tsx` |
| Footer | 4-column branded footer | `sections/Footer.tsx` |
| LoadingScreen | Cinematic loading animation | `sections/LoadingScreen.tsx` |
| WelcomePopup | Game-style character welcome modal | `sections/WelcomePopup.tsx` |
| HeroSection | Hero with campus background, CTA | `sections/HeroSection.tsx` |
| AboutSection | About, vision-mission, statistics | `sections/AboutSection.tsx` |
| ProdiSection | Program studi cards (Akuntansi, Bisnis Digital) | `sections/ProdiSection.tsx` |
| DosenSection | Lecturer profile grid | `sections/DosenSection.tsx` |
| JadwalSection | Interactive schedule table with filters | `sections/JadwalSection.tsx` |
| KalenderSection | Academic calendar events | `sections/KalenderSection.tsx` |
| BeritaSection | News cards + auto-popup | `sections/BeritaSection.tsx` |
| PMBSection | Registration info + form | `sections/PMBSection.tsx` |
| MapSection | Google Maps embed | `sections/MapSection.tsx` |
| KontakSection | Contact info + form | `sections/KontakSection.tsx` |
| GaleriSection | Image carousel/grid | `sections/GaleriSection.tsx` |
| SiakadSection | SIAKAD portal link section | `sections/SiakadSection.tsx` |
| SearchBar | Expandable search with results | `components/SearchBar.tsx` |
| ChatbotFAB | Floating chatbot button + panel | `components/ChatbotFAB.tsx` |
| ThemeToggle | Dark/light mode toggle | `components/ThemeToggle.tsx` |
| ToastSystem | Toast notification manager | `components/ToastSystem.tsx` |
| DarkModeProvider | Context for theme state | `providers/DarkModeProvider.tsx` |

---

## Animation Implementation Table

| Animation | Library | Implementation | Complexity |
|-----------|---------|---------------|------------|
| Cinematic loading screen | Framer Motion | AnimatePresence + motion.div with staggered children, progress bar width animation 0→100% over 2s | High |
| Game-style welcome popup | Framer Motion | Scale 0→1 + opacity, spring animation, character bounce. Overlay with backdrop blur | Medium |
| Scroll reveal (sections) | Framer Motion | whileInView + viewport, opacity 0→1, y: 30→0, staggerChildren 0.1s | Medium |
| Header scroll shrink | Framer Motion | useScroll + useTransform, height 80→64, background opacity transition | Medium |
| Hero parallax background | Framer Motion | useScroll + useTransform for subtle background movement | Medium |
| Hero text entrance | Framer Motion | Staggered fade-in + slideUp for title, subtitle, buttons | Medium |
| Card hover lift | CSS/Tailwind | hover:translate-y-[-4px] + hover:shadow-lg transition-all duration-300 | Low |
| Button hover glow | CSS/Tailwind | hover:shadow-[0_0_20px_rgba(251,191,36,0.4)] + hover:scale-[1.02] | Low |
| Link underline expand | CSS | Pseudo-element width 0→100% on hover, Yellow 400, 0.3s | Low |
| Image hover zoom | CSS | Container overflow-hidden, img hover:scale-105 transition | Low |
| Statistic count-up | Framer Motion | useInView trigger, animate number from 0 to target | Medium |
| Dark mode transition | CSS | transition-colors duration-300 on body/html | Low |
| Toast slide-in | Framer Motion | AnimatePresence, x: 100→0 in, x: 0→100 out | Low |
| Chatbot panel open | Framer Motion | Scale 0.8→1 + opacity 0→1 from bottom-right origin | Medium |
| Search bar expand | Framer Motion | Width animation, opacity for results dropdown | Medium |
| Gallery carousel | Framer Motion | Drag gesture with snap points, or use CSS scroll-snap | Medium |
| Smooth scroll | CSS + JS | scroll-behavior: smooth, scrollIntoView with offset for nav links | Low |

---

## State & Logic

### Dark Mode
- `DarkModeProvider` wraps app with React Context
- State: `theme: 'light' | 'dark'`
- On toggle: set class on `<html>`, persist to localStorage
- All components use Tailwind `dark:` prefix

### Loading Screen
- State: `isLoading: boolean` in App.tsx
- On mount: set loading true, simulate 2.5s load (setTimeout)
- After timeout: set loading false, trigger WelcomePopup

### Welcome Popup
- State: `showWelcome: boolean`
- Show only on first visit: check `localStorage.getItem('welcomeShown')`
- On close: set flag in localStorage
- Animation: spring scale + opacity

### Search Bar
- State: `isOpen: boolean`, `query: string`, `results: SearchResult[]`
- Filter mock data (dosen, berita, prodi) by query
- Group results by category
- Close on Escape key or outside click

### Chatbot
- State: `isOpen: boolean`, `messages: Message[]`, `input: string`
- Rule-based responses using keyword matching
- Toggle open/close FAB button

### Toast System
- Global toast queue: `toasts: Toast[]` with add/remove
- Auto-dismiss after 5s with timer
- AnimatePresence for enter/exit animations

### Schedule Table (Jadwal)
- State: `selectedSemester: string`, `selectedDay: string | 'all'`
- Filter schedule data by semester and day
- Tab navigation for days, Select for semester

---

## Data Architecture

All data stored in static JSON files under `src/data/`:

| File | Content |
|------|---------|
| `dosen.json` | Lecturer profiles (name, title, email, photo, phone) |
| `berita.json` | News articles (title, date, excerpt, image, category) |
| `jadwal.json` | Course schedules (course, day, time, room, semester, lecturer) |
| `kalender.json` | Academic calendar events (title, date, type: uts/uas/libur/seminar) |
| `galeri.json` | Gallery images (src, alt, caption) |

### TypeScript Interfaces
```typescript
// types/index.ts
interface Dosen { id: number; name: string; title: string; email: string; phone: string; photo: string; }
interface Berita { id: number; title: string; date: string; excerpt: string; image: string; category: string; }
interface JadwalItem { id: number; course: string; day: string; time: string; room: string; semester: number; lecturer: string; }
interface KalenderEvent { id: number; title: string; date: string; type: 'uts' | 'uas' | 'libur' | 'seminar' | 'lainnya'; }
interface GalleryItem { id: number; src: string; alt: string; caption: string; }
```

---

## Routing

Single-page application with smooth-scroll sections. Route: `/`

Navigation links scroll to section IDs:
- Beranda → `#hero`
- Tentang → `#about`
- Program Studi → `#prodi`
- Dosen → `#dosen`
- Jadwal → `#jadwal`
- Berita → `#berita`

---

## Project Structure

```
/mnt/agents/output/app/
├── public/
│   └── images/          # Generated image assets
├── src/
│   ├── sections/        # Page sections (17 sections)
│   ├── components/      # Shared components (SearchBar, ChatbotFAB, etc.)
│   ├── components/ui/   # shadcn/ui components
│   ├── providers/       # Context providers (DarkModeProvider)
│   ├── data/            # Static JSON data files
│   ├── types/           # TypeScript interfaces
│   ├── hooks/           # Custom hooks (useScrollReveal, etc.)
│   ├── App.tsx          # Root component with all sections
│   ├── main.tsx         # Entry point
│   └── index.css        # Global styles + Tailwind
├── index.html
├── vite.config.ts
├── tailwind.config.js
├── tsconfig.json
└── package.json
```

---

## Responsive Strategy

| Breakpoint | Layout |
|------------|--------|
| Mobile (<640px) | Single column, stacked, hamburger nav, reduced padding |
| Tablet (640-1024px) | 2-column grids, partial nav, medium padding |
| Desktop (>1024px) | Full layout, all columns, max-width 1200px centered |

Tailwind responsive prefixes: `sm:`, `md:`, `lg:`, `xl:`

---

## Performance Considerations

1. **Images**: All images generated once, stored in `public/images/`, referenced by filename
2. **Lazy loading**: Sections below fold use `whileInView` from Framer Motion (auto lazy-loads)
3. **Animations**: Use `transform` and `opacity` only (GPU accelerated)
4. **Bundle**: Vite handles tree-shaking, code splitting natural with section components
5. **No external API calls**: All data is local JSON, zero network latency
