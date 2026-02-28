# 🎨 Tailwind CSS – Ściągawka

Utility-first CSS – klasy zamiast pisania CSS od zera.

---

## 📐 Layout & Flexbox

```html
<!-- Flex row, wyśrodkowany -->
<div class="flex items-center justify-between gap-4">
  <!-- Flex column -->
  <div class="flex flex-col items-center gap-2">
    <!-- Flex wrap + responsywność -->
    <div class="flex flex-wrap md:flex-nowrap gap-4">
      <!-- Justify opcje -->
      justify-start | justify-center | justify-between | justify-around |
      justify-end

      <!-- Align opcje -->
      items-start | items-center | items-end | items-stretch
    </div>
  </div>
</div>
```

---

## 🔲 Grid

```html
<!-- 3 kolumny, responsywne -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <!-- Span kolumn -->
  <div class="col-span-2">
    <!-- Auto-fill z minimalną szerokością -->
    <div
      class="grid grid-cols-[repeat(auto-fill,minmax(250px,1fr))] gap-4"
    ></div>
  </div>
</div>
```

---

## 📏 Spacing (Margin & Padding)

```
Skala: 1=4px, 2=8px, 4=16px, 6=24px, 8=32px, 12=48px, 16=64px

p-4        → padding: 16px (wszystkie strony)
px-4 py-2  → padding-left/right: 16px, padding-top/bottom: 8px
mt-4       → margin-top: 16px
mx-auto    → margin-left/right: auto (centrowanie)
space-x-4  → gap między dziećmi w osi X
```

---

## 🎨 Kolory & Tło

```html
<!-- Tekst -->
<p class="text-gray-900 dark:text-gray-100">
  <!-- Tło -->
</p>

<div class="bg-white dark:bg-gray-900">
  <div class="bg-blue-500 hover:bg-blue-600">
    <!-- Gradient -->
    <div class="bg-gradient-to-r from-blue-500 to-purple-600">
      <!-- Opacity -->
      <div class="bg-black/50"><!-- bg-black z opacity 50% --></div>
    </div>
  </div>
</div>
```

---

## ✍️ Typografia

```html
<!-- Rozmiary -->
text-xs | text-sm | text-base | text-lg | text-xl | text-2xl | text-4xl

<!-- Wagi -->
font-light | font-normal | font-medium | font-semibold | font-bold

<!-- Wyrównanie -->
text-left | text-center | text-right

<!-- Inne -->
<p class="leading-relaxed tracking-wide uppercase truncate line-clamp-2"></p>
```

---

## 🔲 Borders & Rounded

```html
<!-- Border -->
<div class="border border-gray-200">
  <div class="border-2 border-blue-500">
    <div class="border-b border-gray-100">
      <!-- tylko dół -->

      <!-- Zaokrąglenia -->
      rounded-sm | rounded | rounded-md | rounded-lg | rounded-xl | rounded-2xl
      | rounded-full

      <!-- Ring (focus) -->
      <input
        class="focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
      />
    </div>
  </div>
</div>
```

---

## 📦 Sizing

```html
<!-- Szerokość -->
w-full | w-auto | w-1/2 | w-1/3 | w-64 (256px) | w-screen | w-fit

<!-- Wysokość -->
h-full | h-screen | h-auto | h-16 (64px) | min-h-screen

<!-- Max/Min -->
max-w-sm | max-w-md | max-w-lg | max-w-xl | max-w-2xl | max-w-screen-xl
```

---

## 🎭 Stany (Hover, Focus, Active)

```html
<button
  class="
  bg-blue-500
  hover:bg-blue-600
  active:bg-blue-700
  focus:outline-none focus:ring-2 focus:ring-blue-500
  disabled:opacity-50 disabled:cursor-not-allowed
  transition-colors duration-200
"
>
  Kliknij
</button>
```

---

## 📱 Responsywność (Mobile First)

```
Breakpointy:
sm:  ≥ 640px
md:  ≥ 768px
lg:  ≥ 1024px
xl:  ≥ 1280px
2xl: ≥ 1536px
```

```html
<!-- Ukryj na mobile, pokaż od md -->
<div class="hidden md:block">
  <!-- Inna liczba kolumn na różnych ekranach -->
  <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4">
    <!-- Różny padding -->
    <div class="p-4 md:p-8 lg:p-12"></div>
  </div>
</div>
```

---

## 🌙 Dark Mode

```html
<!-- W tailwind.config.js: darkMode: 'class' -->
<div class="bg-white text-gray-900 dark:bg-gray-900 dark:text-white"></div>
```

```ts
// Przełącznik
document.documentElement.classList.toggle("dark");
```

---

## 🔧 Arbitrary Values

```html
<!-- Dokładna wartość poza skalą -->
<div class="w-[342px] h-[calc(100vh-64px)] bg-[#1a1a2e] text-[13px]">
  <!-- Arbitrary variant -->
  <div class="[&:nth-child(3)]:bg-blue-500"></div>
</div>
```

---

## ⚙️ Konfiguracja `tailwind.config.ts`

```ts
import type { Config } from "tailwindcss";

export default {
  content: ["./src/**/*.{ts,tsx}"],
  darkMode: "class",
  theme: {
    extend: {
      colors: {
        primary: {
          50: "#eff6ff",
          500: "#3b82f6",
          900: "#1e3a5f",
        },
      },
      fontFamily: {
        sans: ["Inter", "sans-serif"],
      },
      animation: {
        "fade-in": "fadeIn 0.3s ease-in-out",
      },
    },
  },
  plugins: [],
} satisfies Config;
```

---

## 💡 Tips & Aliasy

```ts
// cn() helper – łączenie klas z warunkami (clsx + tailwind-merge)
// npm install clsx tailwind-merge
import { clsx } from 'clsx'
import { twMerge } from 'tailwind-merge'

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}

// Użycie:
<div className={cn('base-class', isActive && 'active-class', className)}>
```

- Zainstaluj **Tailwind CSS IntelliSense** w VS Code – autocomplete klas
- Używaj `@apply` oszczędnie – tylko dla bardzo powtarzalnych wzorców
- `prettier-plugin-tailwindcss` – automatyczne sortowanie klas
