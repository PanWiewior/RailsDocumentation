# Przydatne skróty i tipy przy pracy z VS Code

## 1. Szybka Nawigacja (Klucz do Rails)

W projektach Rails plików jest mnóstwo – zapomnij o szukaniu ich myszką w drzewie:

- **`Ctrl + P`**: Szybkie otwieranie plików. Wpisz np. `uscon`, aby znaleźć `users_controller.rb`.
- **`Ctrl + Shift + O`**: Skok do symbolu/metody wewnątrz pliku. Wpisz `:`, aby pogrupować je (metody, zmienne).
- **`Ctrl + G`**: Przejdź do konkretnej linii (niezbędne, gdy logi serwera Rails wskazują błąd w konkretnym miejscu).
- **`Alt + ←` / `→`**: Powrót/Przejście dalej w historii pozycji kursora (świetne po użyciu "Go to Definition").

## 2. Edycja i Manipulacja Kodem

- **`Ctrl + D`**: Zaznaczanie kolejnych wystąpień tego samego słowa. Idealne do szybkiej zmiany nazw zmiennych.
- **`Ctrl + Shift + K`**: Usuwa całą bieżącą linię, na której stoi kursor.
- **`Alt + ↑` / `↓`**: Przesuwa całą linię (lub zaznaczony blok) w górę lub w dół. Super do przenoszenia metod w modelu.
- **`Shift + Alt + ↑` / `↓`**: Kopiuje linię lub zaznaczony blok w górę lub w dół.

## 3. Praca z Terminalem (Zintegrowanym)

W Rails terminal to podstawa (`rails s`, `bundle exec`, `rails g`):

- **`Ctrl + ` `** (tylda): Pokazuje lub ukrywa terminal.
- **`Ctrl + Shift + 5`**: Rozdziela terminal na dwa okna (Split Terminal) – np. logi serwera obok konsoli Rails.
- **`Ctrl + PageUp` / `PageDown`**: Przełączanie się między kartami (instancjami) terminala.

## 4. Railsowe "Must-Have"

- **`Ctrl + Shift + P`**: Paleta komend. Najważniejsze okno – wpisujesz tu polecenia dla Rubocop czy Ruby LSP (np. Ruby LSP: Restart, gdy coś przestanie podpowiadać, lub Rubocop: Autocorrect).
- **`F12`**: Przejdź do definicji (Go to Definition) – skacze do kodu źródłowego metody.
- **`Shift + F12`**: Podgląd wszystkich miejsc, w których dana metoda jest używana (References).
- **`Ctrl + /`**: Szybkie komentowanie/odkomentowanie linii lub bloku kodu.

## 5. Zarządzanie Widokiem i Ubuntu Specific

- **`Ctrl + B`**: Pokazuje/ukrywa lewy pasek boczny (Explorer).
- **`Ctrl + Alt + B`**: Pokazuje/ukrywa prawy pasek boczny (Secondary Side Bar).
- **`Ctrl + Shift + E`**: Skok fokusu do Eksploratora plików.
- **`Ctrl + Shift + G`**: Skok fokusu do kontroli wersji (Git).
- **`Ctrl + Shift + F`**: Globalne szukanie w projekcie.

## 6. Tryb Zen i Centrowanie (Zen Mode)  

Gdy skupiasz się tylko na pisaniu logiki w Ruby:

- Naciśnij **`Ctrl + K`**, a potem **`Z`**.
- Wszystkie paski znikną. Aby wrócić, naciśnij dwukrotnie **`Esc`**.
