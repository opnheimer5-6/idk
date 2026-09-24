# C++ — Lexer / moje notatki

---

## Biblioteki

```cpp
#include <iostream>
```

=> Dodaje operacje wejścia i wyjścia.

```cpp
#include <string>
```

=> Dodaje obsługę `std::string`.

```cpp
#include <fstream>
```

=> Dodaje obsługę plików.

```cpp
#include <cctype>
```

=> Dodaje funkcje sprawdzające znaki, np. `isspace()`, `isdigit()`.

---

## `main`

```cpp
int main(){
    // kod programu
}
```

=> Tutaj zaczyna się wykonywanie programu.

---

## `std::cout`

```cpp
std::cout << "Hello";
```

=> Wypisuje coś na ekran.

---

## `std::cin`

```cpp
std::cin >> x;
```

=> Wczytuje dane od użytkownika.

---

# Czytanie pliku

Przykładowy plik `plik.txt`:

```text
int x = 5;
```

Otwieramy go:

```cpp
std::ifstream vdr("plik.txt");
```

=> `vdr` reprezentuje otwarty plik.

---

## `char`

```cpp
char c;
```

=> Przechowuje **jeden znak**.

Np.:

```text
c = 'i'
```

a nie całe:

```text
"int"
```

---

## `std::string`

```cpp
std::string word;
```

=> Przechowuje tekst / wiele znaków.

Możemy budować słowo znak po znaku:

```cpp
word.push_back(c);
```

Jeżeli:

```text
c = 'i'
```

=> `word`:

```text
i
```

Potem:

```text
c = 'n'
```

=> `word`:

```text
in
```

Potem:

```text
c = 't'
```

=> `word`:

```text
int
```

---

# `vdr.get(c)`

```cpp
while (vdr.get(c)){
    ...
}
```

=> Wczytuje **jeden kolejny znak z pliku** i zapisuje go do `c`.

`while`:

=> Powtarzaj, dopóki `vdr.get(c)` może pobrać kolejny znak.

Czyli dla:

```text
int x = 5;
```

program widzi kolejno:

```text
i
n
t
[spacja]
x
[spacja]
=
[spacja]
5
;
```

---

# `isspace(c)`

```cpp
if (isspace(c)){
    ...
}
```

=> Sprawdza, czy `c` jest znakiem białym, np. spacją, końcem linii albo tabulatorem.

W naszym lexerze:

```cpp
if (isspace(c)){
    std::cout << word << "\n";
    word.clear();
    continue;
}
```

=> Jeżeli znaleźliśmy spację:

1. `word` jest już gotowym fragmentem.
2. Wypisz `word`.
3. Wyczyść `word`.
4. Przejdź do następnego znaku.

---

# `word.clear()`

```cpp
word.clear();
```

=> Czyści cały `string`.

Np.:

```text
word = "int"
```

=>

```text
word = ""
```

---

# `continue`

```cpp
continue;
```

=> **Pomiń resztę aktualnego obrotu pętli i przejdź do następnego.**

W naszym lexerze:

```cpp
if (isspace(c)){
    word.clear();
    continue;
}
```

=> Nie wykonuj już:

```cpp
word.push_back(c);
```

dla spacji.

---

# `word == "int"`

```cpp
if (word == "int"){
    std::cout << "TYPE";
}
```

=> Sprawdza, czy cały `word` jest dokładnie równy `"int"`.

Czyli:

```text
int => TYPE
```

Dla:

```text
x
```

warunek jest fałszywy.

---

# Rozpoznawanie nazwy

```cpp
if (word == "x"){
    std::cout << "NAME";
}
```

=> Jeżeli `word` jest `"x"`, traktujemy go jako nazwę.

Na tym etapie jest to oczywiście bardzo proste rozpoznawanie.

---

# `for`

```cpp
for (int i = 0; i < word.length(); i++){
    ...
}
```

=> Przechodzi po wszystkich znakach znajdujących się w `word`.

Dla:

```text
word = "123"
```

mamy:

```text
word[0] => '1'
word[1] => '2'
word[2] => '3'
```

---

# `word[i]`

```cpp
word[i]
```

=> Pobiera jeden znak ze stringa.

Przykład:

```cpp
std::string word = "int";

word[0] => 'i'
word[1] => 'n'
word[2] => 't'
```

---

# `isdigit()`

```cpp
isdigit(word[i])
```

=> Sprawdza, czy **konkretny znak** jest cyfrą.

Przykład:

```text
isdigit('5') => prawda
isdigit('x') => fałsz
```

W praktyce funkcja zwraca wartość liczbową:

```text
0       => nie jest cyfrą
!= 0    => jest cyfrą
```

---

# `bool`

```cpp
bool IsNumber = true;
```

=> Zmienna logiczna. Może przechowywać:

```text
true
false
```

Pomysł przy rozpoznawaniu liczby:

```text
IsNumber = true
        =>
sprawdzamy wszystkie znaki
        =>
jeżeli znajdziemy znak, który nie jest cyfrą
        =>
IsNumber = false
```

Czyli dla:

```text
123
```

```text
1 => cyfra
2 => cyfra
3 => cyfra
        =>
IsNumber = true
```

A dla:

```text
12x
```

```text
1 => cyfra
2 => cyfra
x => NIE cyfra
        =>
IsNumber = false
```

---

# `;`

W naszym lexerze:

```cpp
if (c == ';'){
    std::cout << word << "\n";
    std::cout << ";" << std::endl;
    word.clear();
    continue;
}
```

=> `;` traktujemy jako osobny token.

Czyli:

```text
int x = 5;
```

staje się:

```text
int
x
=
5
;
```

---

# Aktualny lexer

Na tym etapie potrafimy już:

```text
plik.txt
    =>
int x = 5;
    =>
czytanie znaków
    =>
budowanie word
    =>
rozpoznawanie części tokenów
```

Przykładowy aktualny kod:

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <cctype>

int main(void){
    std::ifstream vdr("plik.txt");

    char c;
    std::string word;

    while (vdr.get(c)){
        if (isspace(c)){
            std::cout << word << "\n";

            if (word == "int"){
                std::cout << "TYPE" << std::endl;
            }

            if (word == "x"){
                std::cout << "NAME" << "\n";
            }

            word.clear();
            continue;
        }

        if (c == ';'){
            std::cout << word << "\n";
            std::cout << ";" << std::endl;

            word.clear();
            continue;
        }

        word.push_back(c);
    }

    if (!word.empty()){
        std::cout << word << std::endl;
    }
}
```

Dla:

```text
int x = 5;
```

otrzymujemy:

```text
int
TYPE
x
NAME
=
5
;
```

---

# Co dalej?

Nie robimy od razu całego kompilatora.

```text
Lexer
  =>
Tokeny
  =>
Parser
  =>
Struktura programu
  =>
Codegen
  =>
ASM
```

Aktualnie jesteśmy tutaj:

```text
Lexer
  =>
rozpoznawanie tokenów
```

Następny mały krok:

```text
"5"
 =>
NUMBER
```

a później:

```text
"="
 =>
OPERATOR
```

i dopiero potem zaczniemy przechowywać tokeny zamiast tylko je wypisywać.
