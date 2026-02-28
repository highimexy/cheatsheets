# 🔌 Golang – Interfejsy

Interfejsy w Go – implicit implementation, composition i wzorce.

---

## 🏗️ Podstawy

```go
// Definicja interfejsu
type Animal interface {
    Sound() string
    Move() string
}

// Implementacja – IMPLICIT (nie trzeba deklarować "implements")
type Dog struct{ Name string }

func (d Dog) Sound() string { return "Woof" }
func (d Dog) Move() string  { return "run" }

type Bird struct{}

func (b Bird) Sound() string { return "Tweet" }
func (b Bird) Move() string  { return "fly" }

// Użycie
func describe(a Animal) {
    fmt.Printf("Says %s and can %s\n", a.Sound(), a.Move())
}

describe(Dog{Name: "Rex"})   // ✅ Dog implementuje Animal
describe(Bird{})              // ✅ Bird implementuje Animal
```

---

## 🔍 Interface Satisfaction – Sprawdzenie w Compile Time

```go
// Wymuszenie implementacji – jeśli Dog nie implementuje Animal → błąd kompilacji
var _ Animal = Dog{}             // blank identifier
var _ Animal = (*Dog)(nil)       // pointer receiver
```

---

## 📦 Composition – Składanie Interfejsów

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

// Złożony interfejs
type ReadWriter interface {
    Reader
    Writer
}

// Własny przykład
type Repository interface {
    Finder
    Creator
    Deleter
}

type Finder interface {
    FindByID(ctx context.Context, id string) (*User, error)
    FindAll(ctx context.Context) ([]*User, error)
}

type Creator interface {
    Create(ctx context.Context, user *User) error
}
```

---

## 🏛️ Wzorzec Repository (Clean Architecture)

```go
// 1. Zdefiniuj interfejs w warstwie domenowej
type UserRepository interface {
    FindByID(ctx context.Context, id string) (*User, error)
    FindByEmail(ctx context.Context, email string) (*User, error)
    Create(ctx context.Context, user *User) error
    Update(ctx context.Context, user *User) error
    Delete(ctx context.Context, id string) error
}

// 2. Implementacja z Postgresem
type postgresUserRepository struct {
    db *sql.DB
}

func NewUserRepository(db *sql.DB) UserRepository {
    return &postgresUserRepository{db: db}
}

func (r *postgresUserRepository) FindByID(ctx context.Context, id string) (*User, error) {
    user := &User{}
    err := r.db.QueryRowContext(ctx, "SELECT * FROM users WHERE id = $1", id).
        Scan(&user.ID, &user.Email, &user.Name)
    if errors.Is(err, sql.ErrNoRows) {
        return nil, ErrNotFound
    }
    return user, err
}

// 3. Serwis zależy od interfejsu (nie od implementacji)
type UserService struct {
    repo UserRepository         // ← interfejs, nie *postgresUserRepository
}

func NewUserService(repo UserRepository) *UserService {
    return &UserService{repo: repo}
}

// 4. W testach – łatwe mockowanie
type mockUserRepository struct {
    users map[string]*User
}

func (m *mockUserRepository) FindByID(_ context.Context, id string) (*User, error) {
    user, ok := m.users[id]
    if !ok {
        return nil, ErrNotFound
    }
    return user, nil
}
// ... implementuj resztę metod
```

---

## 🤫 Interface{} i any

```go
// any = interface{} (Go 1.18+)
func printAnything(v any) {
    fmt.Println(v)
}

// Type assertion – wyciągnij konkretny typ
var i any = "hello"

s, ok := i.(string)             // bezpieczna (ok=false zamiast paniki)
if !ok {
    fmt.Println("nie string")
}

s := i.(string)                 // niebezpieczna – panic jeśli zły typ

// Type switch
func describe(i any) {
    switch v := i.(type) {
    case int:
        fmt.Printf("int: %d\n", v)
    case string:
        fmt.Printf("string: %q\n", v)
    case bool:
        fmt.Printf("bool: %t\n", v)
    case []int:
        fmt.Printf("[]int z %d elementów\n", len(v))
    default:
        fmt.Printf("nieznany typ: %T\n", v)
    }
}
```

---

## 🎭 Stringer – fmt.Stringer

```go
// Implementacja Stringer pozwala kontrolować format %v i %s
type User struct {
    ID    string
    Name  string
    Email string
}

func (u User) String() string {
    return fmt.Sprintf("User(%s, %s)", u.ID, u.Name)
}

user := User{ID: "1", Name: "Jan", Email: "jan@example.com"}
fmt.Println(user)    // User(1, Jan)  ← wywołuje String() automatycznie
```

---

## ❌ Interfejs Error

```go
// Wbudowany interfejs error
type error interface {
    Error() string
}

// Własny błąd z kontekstem
type NotFoundError struct {
    Resource string
    ID       string
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("%s with id %s not found", e.Resource, e.ID)
}

// Użycie
func findUser(id string) (*User, error) {
    if id == "" {
        return nil, &NotFoundError{Resource: "user", ID: id}
    }
    ...
}

// Sprawdzanie
var notFound *NotFoundError
if errors.As(err, &notFound) {
    fmt.Println("Nie znaleziono:", notFound.Resource)
}
```

---

## 🔧 Functional Options Pattern

```go
type Server struct {
    host    string
    port    int
    timeout time.Duration
}

type Option func(*Server)

func WithPort(port int) Option {
    return func(s *Server) { s.port = port }
}

func WithTimeout(t time.Duration) Option {
    return func(s *Server) { s.timeout = t }
}

func NewServer(host string, opts ...Option) *Server {
    s := &Server{host: host, port: 8080, timeout: 30 * time.Second}
    for _, opt := range opts {
        opt(s)
    }
    return s
}

// Elastyczne użycie
srv := NewServer("localhost",
    WithPort(9000),
    WithTimeout(60*time.Second),
)
```

---

## 💡 Tips

- **Małe interfejsy** – Go preferuje interfejsy z 1-3 metodami (`io.Reader`, `io.Writer`)
- **Definiuj interfejsy tam gdzie są używane** (consumer), nie tam gdzie są implementowane
- **Nie zwracaj interfejsów z konstruktorów** jeśli nie musisz – zwracaj konkrety, przyjmuj interfejsy
- Zasada: _"Accept interfaces, return structs"_
- Interfejs z zerowymi metodami (`interface{}` / `any`) akceptuje wszystko – używaj rozważnie
