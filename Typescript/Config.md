# ⚙️ TypeScript – Konfiguracja

Gotowe konfiguracje `tsconfig.json` dla różnych środowisk.

---

## 🏗️ Bazowy `tsconfig.json` (Next.js / React)

```json
{
  "compilerOptions": {
    /* Wersja i moduły */
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "Bundler",

    /* Strict mode – ZAWSZE włączony */
    "strict": true,
    "noUncheckedIndexedAccess": true, // arr[0] jest T | undefined
    "noImplicitReturns": true, // wszystkie ścieżki muszą zwracać wartość
    "noFallthroughCasesInSwitch": true,

    /* Path aliases */
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"],
      "@lib/*": ["./src/lib/*"]
    },

    /* Interop */
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "resolveJsonModule": true,
    "isolatedModules": true,

    /* Output */
    "noEmit": true, // Next.js/Vite sami budują
    "jsx": "preserve",

    /* Incremental compilation */
    "incremental": true,
    "tsBuildInfoFile": ".next/cache/tsbuildinfo"
  },
  "include": ["**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules", ".next", "dist"]
}
```

---

## 🖥️ Node.js / Backend

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022"],
    "module": "CommonJS",
    "moduleResolution": "Node",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

---

## 🌐 Vite + React

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "Bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

---

## 🔑 Kluczowe Opcje – Objaśnienia

| Opcja                           | Co robi                                                    |
| ------------------------------- | ---------------------------------------------------------- |
| `"strict": true`                | Włącza 8 flag naraz (strictNullChecks, noImplicitAny itd.) |
| `"noUncheckedIndexedAccess"`    | `arr[0]` zwraca `T \| undefined` zamiast `T`               |
| `"moduleResolution": "Bundler"` | Dla Vite/esbuild/Next – nie wymaga rozszerzeń `.js`        |
| `"isolatedModules"`             | Każdy plik to moduł – wymagane przez Babel/SWC             |
| `"paths"`                       | Aliasy do importów zamiast `../../components`              |
| `"skipLibCheck"`                | Pomija sprawdzanie typów w `node_modules` – szybszy build  |

---

## 🛠️ Polecenia

```bash
# Sprawdź typy bez budowania
npx tsc --noEmit

# Watch mode dla typechecking
npx tsc --noEmit --watch

# Wyświetl aktywną konfigurację (po wszystkich extendsach)
npx tsc --showConfig

# Sprawdź wersję
npx tsc --version
```

---

## 💡 Tips

- Zawsze miej `"strict": true` – ból teraz, spokój jutro
- `"noUncheckedIndexedAccess"` znajdzie wiele runtime bugów podczas kompilacji
- Używaj `paths` dla aliasów – koniec z `../../../../utils`
- W monorepo: bazowy `tsconfig.base.json` + osobne `tsconfig.json` w każdym pakiecie
