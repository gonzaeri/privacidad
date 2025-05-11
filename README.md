## Topcoder Showcase Carousel

### 🚀 Getting Started

#### 1. Install & Run

Make sure you have **Node.js v18+** installed. Then, from the root folder:

```bash
npm install
npm run dev
```

Once the dev server is running, open your browser at:

👉 `http://localhost:3000`

---

### 📁 Project Structure

```
src/
├── app
│   ├── favicon.ico
│   ├── globals.scss
│   ├── layout.js
│   ├── page.js             # Main entry point
│   └── page.module.css
├── components
│   ├── Carousel
│   │   ├── Carousel.module.scss
│   │   ├── EmblaCustomers.jsx
│   │   ├── EmblaIndustry.jsx
│   │   ├── EmblaProjects.jsx
│   │   └── Slide.jsx
│   └── Shared
│       └── TitleSection.jsx
├── data
│   └── mockData.js
└── utils
    └── helpers.js  # contains generateParabolaClipPath
```

You can remove any unused files (like `TitleSection.jsx`) if not referenced anywhere in your layout.

---

### 📦 Libraries Used

* [`embla-carousel-react`](https://www.embla-carousel.com/)
* [`framer-motion`](https://www.framer.com/motion/)
* [`react`](https://reactjs.org/)
* [`sass`](https://sass-lang.com/) for modular and responsive styling

---

### 🧩 Description

This project implements a dynamic multi-carousel interface in React (Next.js), using `embla-carousel` for infinite scrolling, `framer-motion` for animated transitions, and custom `clip-path` logic for parabolic visual effects. The user flow follows a hierarchical structure:

```
Industry → Customer → Projects
```

Each level triggers the display of the next, and the components are updated dynamically with animated highlight states and layout stability across interactions.

---

### Features

* ✅ Infinite, centered carousels powered by `embla-carousel`
* ✅ Custom parabolic top and bottom clipping (`clip-path: polygon(...)`)
* ✅ Responsive design with dynamic layout and clipping based on window size
* ✅ Scroll and resize synchronization with smooth updates
* ✅ Header text switches based on `selectedCustomer` presence
* ✅ Full deselection logic when switching industries
* ✅ No layout shift when switching header content
* ✅ AnimatePresence-based fade-ins for conditional carousels

---

### Component Overview

* `EmblaIndustry`: Main carousel displaying industry options
* `EmblaCustomers`: Triggered by industry, shows associated customers
* `EmblaProjects`: Triggered by customer, shows associated projects
* `generateParabolaClipPath`: Helper function to generate dynamic polygon clip paths
* `page.js`: Orchestrates shared state and dynamic layout

---

### Key Props & Usage

```jsx
<EmblaIndustry
  selectedIndustry={selectedIndustry}
  onIndustrySelect={handleIndustrySelect}
  onCondensedHeightReady={setCondensedHeight}
/>

<EmblaCustomers
  customers={selectedIndustry.customers}
  height={condensedHeight * 1.2}
  onCustomerSelect={setSelectedCustomer}
  selectedCustomer={selectedCustomer}
/>

<EmblaProjects
  projects={selectedCustomer.projects}
  height={condensedHeight * 1.2}
/>

---

### 🎥 Demo Video

A test video is included in the `/docs` folder for demonstration purposes.
