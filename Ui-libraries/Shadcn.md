# 🧩 shadcn/ui – Ściągawka

Biblioteka komponentów "copy-paste" zbudowana na Radix UI + Tailwind CSS.

---

## ⚙️ Setup

```bash
# Inicjalizacja w projekcie Next.js / Vite
npx shadcn@latest init

# Dodawanie pojedynczych komponentów
npx shadcn@latest add button
npx shadcn@latest add dialog
npx shadcn@latest add form input label

# Lista wszystkich dostępnych komponentów
npx shadcn@latest add
```

> Komponenty lądują w `src/components/ui/` – są Twoje, możesz je modyfikować!

---

## 🔘 Button

```tsx
import { Button } from '@/components/ui/button'

<Button>Domyślny</Button>
<Button variant="destructive">Usuń</Button>
<Button variant="outline">Outline</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="link">Link</Button>
<Button size="sm">Mały</Button>
<Button size="lg">Duży</Button>
<Button disabled>Zablokowany</Button>
<Button isLoading>Ładowanie...</Button>  {/* jeśli rozszerzysz */}

<!-- Z ikoną (np. Lucide) -->
import { Trash2 } from 'lucide-react'
<Button variant="destructive" size="sm">
  <Trash2 className="mr-2 h-4 w-4" />
  Usuń
</Button>
```

---

## 📋 Form (React Hook Form + Zod)

```tsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";

const schema = z.object({
  email: z.string().email("Nieprawidłowy email"),
  password: z.string().min(8, "Minimum 8 znaków"),
});

type FormData = z.infer<typeof schema>;

export function LoginForm() {
  const form = useForm<FormData>({ resolver: zodResolver(schema) });

  const onSubmit = (data: FormData) => console.log(data);

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
        <FormField
          control={form.control}
          name="email"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input placeholder="jan@example.com" {...field} />
              </FormControl>
              <FormMessage /> {/* błędy z Zod automatycznie */}
            </FormItem>
          )}
        />
        <Button type="submit">Zaloguj</Button>
      </form>
    </Form>
  );
}
```

---

## 🪟 Dialog (Modal)

```tsx
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog";

<Dialog>
  <DialogTrigger asChild>
    <Button>Otwórz modal</Button>
  </DialogTrigger>
  <DialogContent className="sm:max-w-[425px]">
    <DialogHeader>
      <DialogTitle>Potwierdź akcję</DialogTitle>
      <DialogDescription>Tej operacji nie można cofnąć.</DialogDescription>
    </DialogHeader>
    <div className="py-4">Treść modala</div>
    <DialogFooter>
      <Button variant="outline">Anuluj</Button>
      <Button variant="destructive">Usuń</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>;
```

---

## 📊 Table

```tsx
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";

<Table>
  <TableHeader>
    <TableRow>
      <TableHead>Imię</TableHead>
      <TableHead>Email</TableHead>
      <TableHead className="text-right">Akcje</TableHead>
    </TableRow>
  </TableHeader>
  <TableBody>
    {users.map((user) => (
      <TableRow key={user.id}>
        <TableCell className="font-medium">{user.name}</TableCell>
        <TableCell>{user.email}</TableCell>
        <TableCell className="text-right">
          <Button variant="ghost" size="sm">
            Edytuj
          </Button>
        </TableCell>
      </TableRow>
    ))}
  </TableBody>
</Table>;
```

---

## 🔔 Toast / Sonner

```tsx
// Shadcn używa Sonner jako toastera
import { toast } from "sonner";

// Dodaj do layout.tsx:
import { Toaster } from "@/components/ui/sonner";
<Toaster richColors position="top-right" />;

// Użycie wszędzie:
toast.success("Zapisano zmiany");
toast.error("Coś poszło nie tak");
toast.warning("Sprawdź dane");
toast.promise(saveUser(), {
  loading: "Zapisywanie...",
  success: "Użytkownik zapisany",
  error: "Błąd zapisu",
});
```

---

## 📋 Select

```tsx
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";

<Select onValueChange={(value) => setRole(value)} defaultValue="user">
  <SelectTrigger className="w-[180px]">
    <SelectValue placeholder="Wybierz rolę" />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="admin">Admin</SelectItem>
    <SelectItem value="moderator">Moderator</SelectItem>
    <SelectItem value="user">Użytkownik</SelectItem>
  </SelectContent>
</Select>;
```

---

## 🃏 Card

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

<Card>
  <CardHeader>
    <CardTitle>Tytuł karty</CardTitle>
    <CardDescription>Opis karty</CardDescription>
  </CardHeader>
  <CardContent>
    <p>Treść karty</p>
  </CardContent>
  <CardFooter className="flex justify-between">
    <Button variant="outline">Anuluj</Button>
    <Button>Zapisz</Button>
  </CardFooter>
</Card>;
```

---

## 💡 Tips

- `asChild` na `DialogTrigger` / `TooltipTrigger` – przekazuje props do dziecka zamiast tworzyć wrapper `<button>`
- Komponenty możesz dowolnie modyfikować w `src/components/ui/` – to Twój kod
- `cn()` helper jest już w `src/lib/utils.ts` po inicjalizacji
- Wszystkie komponenty mają wbudowaną dostępność (ARIA) z Radix UI
- Strona z komponentami: [ui.shadcn.com](https://ui.shadcn.com)
