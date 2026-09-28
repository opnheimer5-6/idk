# Glush

## Rozdział 1 — Narodziny języka

Zanim napiszemy pierwszy kawałek kodu naszego kompilatora, zatrzymajmy się na chwilę.

Nie chcę zaczynać od:

```go
func main() {
    // 300 linii kodu
}
```

bo wtedy bardzo szybko można dojść do momentu, w którym kod działa, ale właściwie nie wiadomo dlaczego.

Zrobimy to trochę inaczej.

Najpierw będziemy **budować Glusha razem**.

Będziemy podejmować decyzje, sprawdzać, czy mają sens, czasami coś uprościmy, czasami później zmienimy. Jeśli jakaś decyzja okaże się kiepska, nie będziemy jej ukrywać. W prawdziwych projektach też nie dostajemy od początku idealnej architektury.

Nasz cel jest prosty:

> **Stworzyć własny język programowania i przy okazji naprawdę zrozumieć, jak działa jego kompilator.**

Pierwszym językiem będzie **Glush**.

---

## 1.1. Czym właściwie ma być Glush?

Glush ma być językiem wygodnym w użyciu.

Nie chcemy kopiować Go, C czy C++.

Możemy się od nich inspirować, ale Glush ma mieć własny charakter.

Jedno z naszych podstawowych założeń brzmi:

> **Jeżeli kompilator może bezpiecznie pomóc programiście, nie musi zmuszać go do wykonywania całej pracy ręcznie.**

Dlatego Glush będzie miał między innymi automatyczne zarządzanie pamięcią przez garbage collector.

Jednocześnie nie chcemy języka, w którym wszystko dzieje się magicznie i programista nie ma pojęcia, co właściwie robi jego kod.

Glush będzie więc:

* statycznie typowany,
* wygodny,
* stosunkowo bezpieczny,
* kompilowany do bytecode,
* wykonywany przez własną maszynę wirtualną,
* wyposażony w garbage collector.

Na początku może się to wydawać sporą liczbą rzeczy.

Spokojnie.

Nie będziemy implementować ich wszystkich naraz.

---

## 1.2. Jak będzie działał Glush?

Docelowo droga naszego programu będzie wyglądała mniej więcej tak:

```text
program.gl
    |
    v
  Lexer
    |
    v
  Parser
    |
    v
   AST
    |
    v
Bytecode
    |
    v
    VM
    |
    v
 wykonanie
```

Każdy z tych elementów będzie miał swoje zadanie.

I właśnie dlatego nie zaczniemy od VM ani od garbage collectora.

Najpierw musimy nauczyć komputer **czytać nasz język**.

Ale zanim do tego przejdziemy, warto zrozumieć, dlaczego w ogóle wybraliśmy bytecode.

---

## 1.3. Dlaczego bytecode?

Załóżmy, że napiszemy:

```text
print(10 + 20);
```

Człowiek patrzy na to i od razu mniej więcej wie, co ma się wydarzyć:

1. obliczyć `10 + 20`,
2. otrzymać `30`,
3. przekazać `30` do `print`.

Komputer nie dostaje jednak takiego „zrozumiałego dla człowieka” polecenia.

Musimy więc przetłumaczyć nasz program na coś, co będzie mogło zostać wykonane.

Mamy tutaj kilka możliwości.

Jedną z nich byłoby tłumaczenie Glusha bezpośrednio na kod procesora, na przykład x86-64.

Ale wtedy już na początku musielibyśmy zajmować się rzeczami takimi jak:

* rejestry,
* stos procesora,
* calling convention,
* ABI,
* instrukcje x86-64,
* generowanie assembly.

To wszystko przyda nam się później.

**W Clushu.**

Glush ma nam najpierw pokazać inną drogę.

Zamiast od razu rozmawiać z procesorem, stworzymy własną małą maszynę.

---

## 1.4. Nasza maszyna wirtualna

Wyobraźmy sobie bardzo prostą maszynę.

Potrafi ona wykonywać instrukcje takie jak:

```text
PUSH 10
PUSH 20
ADD
PRINT
```

Co to oznacza?

`PUSH 10` mówi:

> „Połóż 10 na stosie.”

Potem:

```text
PUSH 20
```

daje nam:

```text
20
10
```

Następnie:

```text
ADD
```

zdejmuje obie wartości, dodaje je i odkłada wynik:

```text
30
```

A:

```text
PRINT
```

wypisuje `30`.

Czyli nasz program:

```text
print(10 + 20);
```

mógłby ostatecznie zostać zamieniony na:

```text
PUSH 10
PUSH 20
ADD
PRINT
```

To jest właśnie bardzo uproszczony przykład **bytecode**.

Nie jest to jeszcze prawdziwy bytecode Glusha.

Na razie chcemy tylko zrozumieć pomysł.

---

## 1.5. Ale mamy mały problem

Nasz kompilator nie dostaje przecież:

```text
PUSH 10
PUSH 20
ADD
PRINT
```

Użytkownik napisze:

```text
print(10 + 20);
```

A więc pierwszą rzeczą, którą musi zrobić kompilator, jest **zrozumienie tekstu**.

I tutaj zaczyna się ciekawa część.

Spójrzmy na:

```text
int x = 10;
```

Dla nas jest jasne, co tutaj mamy:

```text
int
x
=
10
;
```

Ale komputer widzi początkowo po prostu ciąg znaków:

```text
i n t   x   =   1 0 ;
```

Musimy więc jakoś powiedzieć:

> „Hej, `int` to słowo kluczowe.”

> „`x` to nazwa zmiennej.”

> „`=` to operator przypisania.”

> „`10` to liczba.”

> „`;` kończy instrukcję.”

I właśnie tym zajmie się **lexer**.

---

# 1.6. Lexer

Lexer, nazywany też analizatorem leksykalnym, bierze zwykły tekst i dzieli go na **tokeny**.

Czyli:

```text
int x = 10;
```

może zostać zamienione na:

```text
INT
IDENTIFIER
EQUAL
NUMBER
SEMICOLON
```

Możemy nawet zapamiętać wartość tokenu:

```text
INT        "int"
IDENTIFIER "x"
EQUAL      "="
NUMBER     "10"
SEMICOLON  ";"
```

To jest bardzo ważny moment.

Nasz parser nie będzie już musiał analizować pojedynczych znaków.

Nie będzie musiał zastanawiać się:

> „Czy te trzy znaki to przypadkiem `int`?”

Lexer zrobi to wcześniej.

Parser dostanie już uporządkowaną informację.

---

## 1.7. Czyli mamy pierwszy podział pracy

Możemy więc zacząć myśleć o kompilatorze jak o kilku osobach pracujących nad tym samym programem.

**Lexer** mówi:

> „Znalazłem takie tokeny.”

**Parser** później powie:

> „Okej, rozumiem, jak te tokeny są ze sobą połączone.”

Kolejne części będą mogły powiedzieć:

> „Dobra, skoro już wiemy, co użytkownik napisał, możemy to przetłumaczyć na coś, co da się wykonać.”

To jest właśnie powód, dla którego nie chcemy od razu pisać jednego wielkiego programu:

```text
czytaj tekst
↓
rób wszystko
↓
uruchom
```

Podział na etapy sprawia, że każda część ma konkretną odpowiedzialność.

---

# 1.8. Nasz pierwszy prawdziwy cel

Na tym etapie nie interesuje nas jeszcze:

* garbage collector,
* tablice,
* structy,
* funkcje,
* wskaźniki,
* optymalizacje,
* x86-64.

Nawet bytecode może jeszcze chwilę poczekać.

Chcemy najpierw zrobić coś bardzo małego.

Chcemy, żeby nasz program w Go potrafił przeczytać:

```text
int x = 10;
```

i powiedzieć:

```text
INT
IDENTIFIER
EQUAL
NUMBER
SEMICOLON
```

Jeżeli to zadziała, będziemy mieli pierwszy kawałek prawdziwego kompilatora.

Niewielki.

Prosty.

Ale **nasz**.

I co ważniejsze — będziemy dokładnie wiedzieć, dlaczego istnieje.

---

## 1.9. Zanim napiszemy lexer

Jest jeszcze jedna rzecz, którą warto ustalić.

Lexer musi jakoś przechowywać informacje o tokenach.

Możemy mieć na przykład coś w tym stylu:

```text
Token
├── typ
└── tekst
```

Czyli token:

```text
NUMBER "123"
```

mógłby oznaczać:

```text
typ  = NUMBER
tekst = "123"
```

Na razie nie potrzebujemy niczego bardziej skomplikowanego.

I tutaj właśnie zaczniemy nasz pierwszy fragment kodu Go.

Nie napiszemy całego lexera.

Najpierw stworzymy **token**.

Potem każemy programowi rozpoznać pierwszy znak.

Potem pierwszy wyraz.

Potem liczby.

I będziemy dokładać kolejne elementy jeden po drugim.

Tak właśnie zaczyna się Glush.
