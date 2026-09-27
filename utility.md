# Biblioteka `<utility>` w C++

Nagłówek `<utility>` z biblioteki standardowej C++ zawiera zestaw narzędzi ogólnego przeznaczenia. Jest automatycznie dołączany przez wiele innych nagłówków (np. `<map>`), ponieważ dostarcza fundamentalne typy i funkcje używane w całej bibliotece standardowej.

## 📋 Spis treści

- [Funkcje](#-funkcje)
- [Klasy i struktury](#-klasy-i-struktury)
- [Operatory dla std::pair](#-operatory-dla-stdpair)
- [Kiedy używać](#-kiedy-używać)

## 🔧 Funkcje

### `std::move`

Rzutuje argument na referencję do r-wartości (rvalue reference), umożliwiając przenoszenie zasobów zamiast ich kopiowania. Sama funkcja **nie wykonuje** przenoszenia — jedynie sygnalizuje kompilatorowi, że dany obiekt może zostać przeniesiony.

```cpp
std::string str = "tekst";
std::vector<std::string> vec;
vec.push_back(std::move(str)); // str w stanie "valid but unspecified"
