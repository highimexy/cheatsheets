# 💠 Chakra UI – Ściągawka

Komponentowa biblioteka UI z wbudowanym design systemem i dark mode.

---

## ⚙️ Setup (Chakra UI v3)

```bash
npm install @chakra-ui/react @emotion/react
```

```tsx
// app/layout.tsx lub main.tsx
import { ChakraProvider, defaultSystem } from "@chakra-ui/react";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html>
      <body>
        <ChakraProvider value={defaultSystem}>{children}</ChakraProvider>
      </body>
    </html>
  );
}
```

---

## 🔘 Button

```tsx
import { Button } from '@chakra-ui/react'

<Button>Domyślny</Button>
<Button colorPalette="blue">Niebieski</Button>
<Button colorPalette="red" variant="outline">Outline czerwony</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="surface">Surface</Button>
<Button size="sm">Mały</Button>
<Button size="lg">Duży</Button>
<Button loading loadingText="Zapisuję...">Zapisz</Button>
<Button disabled>Zablokowany</Button>
```

---

## 📝 Input & Form

```tsx
import { Field, Input, Textarea, NativeSelect } from '@chakra-ui/react'

<Field.Root invalid={!!errors.email}>
  <Field.Label>Email</Field.Label>
  <Input
    placeholder="jan@example.com"
    {...register('email')}
  />
  <Field.ErrorText>{errors.email?.message}</Field.ErrorText>
</Field.Root>

<Field.Root>
  <Field.Label>Rola</Field.Label>
  <NativeSelect.Root>
    <NativeSelect.Field {...register('role')}>
      <option value="admin">Admin</option>
      <option value="user">Użytkownik</option>
    </NativeSelect.Field>
    <NativeSelect.Indicator />
  </NativeSelect.Root>
</Field.Root>
```

---

## 🪟 Dialog (Modal)

```tsx
import {
  DialogRoot,
  DialogTrigger,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogBody,
  DialogFooter,
  DialogCloseTrigger,
} from "@chakra-ui/react";

<DialogRoot>
  <DialogTrigger asChild>
    <Button>Otwórz</Button>
  </DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Potwierdź akcję</DialogTitle>
    </DialogHeader>
    <DialogBody>Tej operacji nie można cofnąć.</DialogBody>
    <DialogFooter>
      <DialogCloseTrigger asChild>
        <Button variant="outline">Anuluj</Button>
      </DialogCloseTrigger>
      <Button colorPalette="red">Usuń</Button>
    </DialogFooter>
    <DialogCloseTrigger />
  </DialogContent>
</DialogRoot>;
```

---

## 🔔 Toaster

```tsx
import { Toaster, toaster } from "@chakra-ui/react";

// Dodaj do layoutu:
<Toaster />;

// Użycie:
toaster.success({
  title: "Zapisano!",
  description: "Zmiany zostały zapisane.",
});
toaster.error({ title: "Błąd", description: "Coś poszło nie tak." });
toaster.create({
  title: "Nowa wiadomość",
  type: "info",
  duration: 5000,
});
```

---

## 📊 Table

```tsx
import { Table } from "@chakra-ui/react";

<Table.Root>
  <Table.Header>
    <Table.Row>
      <Table.ColumnHeader>Imię</Table.ColumnHeader>
      <Table.ColumnHeader>Email</Table.ColumnHeader>
    </Table.Row>
  </Table.Header>
  <Table.Body>
    {users.map((user) => (
      <Table.Row key={user.id}>
        <Table.Cell>{user.name}</Table.Cell>
        <Table.Cell>{user.email}</Table.Cell>
      </Table.Row>
    ))}
  </Table.Body>
</Table.Root>;
```

---

## 🎨 System Props (Style Shorthand)

```tsx
// Spacing
<Box p={4} px={8} py={2} mt={4} mx="auto">

// Kolory
<Box bg="blue.500" color="white">
<Box bg="blue.500" _hover={{ bg: 'blue.600' }}>

// Sizing
<Box w="full" h="100vh" maxW="container.xl">

// Flex
<Flex align="center" justify="between" gap={4}>
<Stack direction="row" spacing={4}>    {/* HStack }
<Stack spacing={4}>                    {/* VStack */}

// Typografia
<Text fontSize="lg" fontWeight="bold" textAlign="center">
<Heading as="h1" size="2xl">

// Border
<Box border="1px solid" borderColor="gray.200" rounded="md">
```

---

## 🌙 Dark Mode

```tsx
import { ColorModeButton } from '@chakra-ui/react'

// Gotowy toggle button – dodaj gdziekolwiek
<ColorModeButton />

// Użycie w CSS/style props
<Box bg={{ base: 'white', _dark: 'gray.900' }}>
<Text color={{ base: 'gray.900', _dark: 'white' }}>
```

---

## 🔧 Custom Theme

```ts
// theme.ts
import { createSystem, defaultConfig, defineConfig } from '@chakra-ui/react'

const config = defineConfig({
  theme: {
    tokens: {
      colors: {
        brand: {
          500: { value: '#3b82f6' },
          600: { value: '#2563eb' },
        },
      },
      fonts: {
        heading: { value: 'Inter, sans-serif' },
        body: { value: 'Inter, sans-serif' },
      },
    },
  },
})

export const system = createSystem(defaultConfig, config)

// layout.tsx:
<ChakraProvider value={system}>
```

---

## ⚖️ Kiedy Chakra vs shadcn/ui?

|              | Chakra UI                   | shadcn/ui                           |
| ------------ | --------------------------- | ----------------------------------- |
| Styl pisania | Style props na komponentach | Klasy Tailwind w JSX                |
| Customizacja | Theme tokens                | Bezpośrednia edycja plików          |
| Bundle size  | Większy                     | Mniejszy (tylko to co dodasz)       |
| Dark mode    | Wbudowany + automatyczny    | Ręczna konfiguracja klas            |
| Dobre dla    | Szybkie prototypy, pełny DS | Projekty z Tailwind, pełna kontrola |

---

## 💡 Tips

- `Stack` = `Flex` z automatycznym `gap` – używaj zamiast ręcznego `Flex + gap`
- `_hover`, `_focus`, `_dark` to pseudo-props – nie potrzebujesz CSS
- `asChild` działa tak samo jak w shadcn/ui (Radix pattern)
- Dokumentacja: [chakra-ui.com](https://chakra-ui.com)
