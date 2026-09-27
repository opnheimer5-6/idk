# 🧰 Biblioteka `<utility>` w C++

> Podstawowe narzędzia biblioteki standardowej C++

---

## 📖 Spis treści
- [std::move](#stdmove)
- [std::forward](#stdforward)
- [std::swap](#stdswap)
- [std::make_pair](#stdmake_pair)
- [std::get](#stdget)
- [std::exchange](#stdexchange)
- [std::as_const](#stdas_const)
- [std::declval](#stddeclval)

---

## 🔀 `std::move`

**Opis:** Rzutuje argument na referencję do r-wartości (rvalue reference), umożliwiając przenoszenie zasobów zamiast ich kopiowania.

**Od:** C++11  
**Złożoność:** O(1)  
**Cel:** Przenoszenie zamiast kopiowania

⚠️ **Uwaga:** `std::move` **nie przenosi** — tylko sygnalizuje kompilatorowi, że obiekt może zostać przeniesiony.

**Przykład:**

    std::string str = "tekst";
    std::vector<std::string> vec;
    vec.push_back(std::move(str));

---

## ➡️ `std::forward`

**Opis:** Idealne przekazywanie (perfect forwarding) — zachowuje kategorię wartości argumentu (lvalue/rvalue) podczas przekazywania do innej funkcji.

**Od:** C++11  
**Cel:** Zachowanie lvalue/rvalue w szablonach

**Przykład:**

    template <class T>
    void wrapper(T&& arg) {
        foo(std::forward<T>(arg));
    }

---

## 🔁 `std::swap`

**Opis:** Zamienia wartości dwóch obiektów.

**Od:** C++98  
**Złożoność:** Zależy od typu

**Przykład:**

    int a = 5, b = 10;
    std::swap(a, b);  // a = 10, b = 5

---

## 👥 `std::make_pair`

**Opis:** Tworzy obiekt `std::pair` z automatyczną dedukcją typów.

**Od:** C++98

**Przykład:**

    auto p = std::make_pair(42, "odpowiedź");

---

## 🎯 `std::get`

**Opis:** Uzyskuje dostęp do elementu `std::pair` lub `std::tuple`.

**Od:** C++11

**Przykład:**

    std::pair<int, std::string> p{1, "jeden"};
    int a = std::get<0>(p);        // 1
    std::string b = std::get<1>(p); // "jeden"

---

## 🔄 `std::exchange`

**Opis:** Przypisuje nową wartość i zwraca poprzednią.

**Od:** C++14

**Przykład:**

    int x = 10;
    int old = std::exchange(x, 20);  // old = 10, x = 20

---

## 🔒 `std::as_const`

**Opis:** Zwraca referencję `const` do argumentu.

**Od:** C++17

**Przykład:**

    std::vector<int> v{1, 2, 3};
    auto& cv = std::as_const(v);

---

## 🧪 `std::declval`

**Opis:** Uzyskuje referencję do typu bez tworzenia obiektu. Używane w `decltype` i `sizeof`.

**Od:** C++11

**Przykład:**

    using T = decltype(std::declval<Foo>().bar());

---

## 🏗️ Klasy i struktury

| Nazwa | Opis | Od |
|-------|------|-----|
| `std::pair` | Przechowuje dwa obiekty jako parę | C++98 |
| `std::tuple_size` | Liczba elementów | C++11 |
| `std::tuple_element` | Typ elementu na indeksie | C++11 |
| `std::integer_sequence` | Sekwencja liczb w kompilacji | C++14 |

---

## 📊 Wersje C++

| Funkcja | C++98 | C++11 | C++14 | C++17 |
|---------|:-----:|:-----:|:-----:|:-----:|
| `std::swap` | ✅ | ✅ | ✅ | ✅ |
| `std::move` | ❌ | ✅ | ✅ | ✅ |
| `std::forward` | ❌ | ✅ | ✅ | ✅ |
| `std::exchange` | ❌ | ❌ | ✅ | ✅ |
| `std::as_const` | ❌ | ❌ | ❌ | ✅ |

---

## 🔗 Zobacz też

- [cppreference — `<utility>`](https://en.cppreference.com/w/cpp/header/utility)
- [ISO C++](https://isocpp.org/)
