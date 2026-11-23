# Dziennik treningów

Repozytorium zawiera pięć potwierdzonych treningów siłowych zapisanych w trzech formatach: **JSON, CSV i XML**. Do XML-a dodany jest schemat **XSD**, który określa reguły poprawności danych.

## Dlaczego ten temat?

Trenuję trójbój siłowy i od dawna prowadzę notatki dotyczące treningów. Wybrałem ten temat, ponieważ treningi mają powtarzalną strukturę: datę, rodzaj planu i intensywność. Dzięki temu łatwo je uporządkować i przedstawić te same informacje na kilka sposobów.

Dane pochodzą z moich własnych notatek i rzeczywistego planu treningowego. Zbiór obejmuje pięć wpisów z okresu od 9 do 21 listopada 2026 r., ułożonych od najnowszego do najstarszego. Nie jest to pełna historia wszystkich treningów.

## Co oznaczają plany A, B i C?

Są to różne zestawy ćwiczeń, które wykonuję cyklicznie w kolejności A, B, C. Różnią się m.in. rozłożeniem cięższych ćwiczeń i ich wariantów:

- **Plan A** obejmuje m.in. ciężki przysiad i wyciskanie na ławce z pauzą.
- **Plan B** obejmuje m.in. ciężki martwy ciąg i wyciskanie żołnierskie.
- **Plan C** obejmuje m.in. ciężkie wyciskanie na ławce i przysiad z pauzą.

Pliki zawierają nazwę planu, a nie pełną rozpiskę ćwiczeń, serii i ciężarów.

## Pliki w repozytorium

| Plik | Zawartość |
| --- | --- |
| [dane/treningi.json](dane/treningi.json) | Lista treningów zapisana jako tablica obiektów JSON. |
| [dane/treningi.csv](dane/treningi.csv) | Te same treningi w postaci wierszy i kolumn oddzielonych przecinkami. |
| [dane/treningi.xml](dane/treningi.xml) | Te same treningi zapisane za pomocą znaczników XML. |
| [dane/treningi.xsd](dane/treningi.xsd) | Schemat określający dozwoloną strukturę i wartości w XML-u. |

## Opis pól

| Pole | Znaczenie | Przykład |
| --- | --- | --- |
| `id` | Dodatni, unikalny identyfikator wpisu. | `1` |
| `data` | Data treningu w formacie RRRR-MM-DD. | `2026-11-21` |
| `rodzaj` | Rodzaj treningu wraz z nazwą planu. | `Siłowy – Plan C` |
| `intensywnosc` | Opisowa informacja o intensywności treningu. | `wysoka` lub `umiarkowana` |

Intensywność jest zapisana jako kategoria, a nie jako liczbowy pomiar obciążenia. Schemat XSD dopuszcza wartości `niska`, `umiarkowana` i `wysoka`; w obecnych danych występują dwie ostatnie.

## Różnice między formatami i sposób odczytu

### JSON

Cały zbiór znajduje się w tablicy `[]`. Każdy obiekt `{}` opisuje jeden trening przez pary nazwa pola–wartość. Liczby zapisuje się bez cudzysłowów, a tekst w cudzysłowach. Przykład pierwszego wpisu:

```json
{
  "id": 1,
  "data": "2026-11-21",
  "rodzaj": "Siłowy – Plan C",
  "intensywnosc": "wysoka"
}
```

Plik można przeczytać w edytorze tekstowym lub odczytać programem obsługującym JSON.

### CSV

Pierwszy wiersz zawiera nazwy kolumn. Każdy następny wiersz opisuje jeden trening. Separatorem jest przecinek.

```csv
id,data,rodzaj,intensywnosc
1,2026-11-21,Siłowy – Plan C,wysoka
```


### XML i XSD

W XML-u element główny `<treningi>` zawiera kolejne elementy `<trening>`. Każde pole ma własny znacznik. Przykład pierwszego wpisu:

```xml
<trening>
  <id>1</id>
  <data>2026-11-21</data>
  <rodzaj>Siłowy – Plan C</rodzaj>
  <intensywnosc>wysoka</intensywnosc>
</trening>
```

Jest to fragment dokumentu. W pełnym pliku elementy `<trening>` znajdują się wewnątrz elementu głównego `<treningi>`.

XML można przeczytać w edytorze tekstowym lub przeglądarce. Samo wyświetlenie pliku w przeglądarce nie oznacza jednak sprawdzenia go ze schematem. Do tego potrzebny jest walidator obsługujący XSD.


Plik `treningi.xsd` wymaga czterech pól w ustalonej kolejności: `id`, `data`, `rodzaj`, `intensywnosc`. Określa również typy danych, dozwolone intensywności i unikalność identyfikatorów.

XML wskazuje plik schematu przez `xsi:noNamespaceSchemaLocation="treningi.xsd"`. Oba pliki należy przechowywać w tym samym folderze.

## Sprawdzenie poprawności

Przygotowane pliki zostały sprawdzone za pomocą PowerShell i bibliotek XML platformy .NET:

- JSON został poprawnie odczytany przez `ConvertFrom-Json` i zawiera pięć rekordów o różnych identyfikatorach.
- CSV został odczytany przez `Import-Csv`. Sprawdzono nazwy kolumn, liczbę wpisów i zgodność wartości z JSON-em.
- XML został poprawnie odczytany, a dane porównano z JSON-em.
- Schemat XSD został skompilowany, a XML przeszedł walidację za pomocą `XmlSchemaSet` i `XmlDocument.Validate`.
- Na kopiach danych w pamięci sprawdzono, czy schemat odrzuca niepoprawną datę, niedozwoloną intensywność, powtórzone ID oraz brak wymaganego pola. Wszystkie te przypadki zostały odrzucone.

Przykładowe polecenia odczytu JSON i CSV, uruchamiane w głównym folderze repozytorium:

```powershell
Get-Content -Raw -Encoding UTF8 dane/treningi.json | ConvertFrom-Json
Import-Csv -Encoding UTF8 dane/treningi.csv
```

**Wynik:** pliki są zgodne z przyjętą strukturą, a trzy formaty przedstawiają te same pięć treningów. Do plików projektu nie zapisano błędnych danych użytych podczas sprawdzania schematu.

## Wnioski

Najwięcej trudności sprawiły mi XML i XSD. XML ma bardziej rozbudowany zapis niż CSV, a XSD dodatkowo określa reguły, które musi spełniać dokument. Miałem wcześniej kontakt z XML-em w szkole średniej, ale przygotowanie schematu wymagało dodatkowej pomocy i wyjaśnień.

CSV przedstawia te dane w prosty, tabelaryczny sposób. JSON zapisuje nazwy pól przy każdym treningu i rozróżnia m.in. liczby i tekst. XML jest bardziej rozbudowany, ale w połączeniu z XSD pozwala sprawdzić strukturę i typy danych.

Taki dziennik mógłby służyć mi do dalszego zapisywania treningów w innej formie niż dotychczasowe notatki. Projekt pokazuje, że ten sam zbiór informacji można przedstawić na kilka sposobów, zachowując jego znaczenie.

## Autor

Leon 


