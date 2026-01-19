# Szybkie wznowienie pracy z ruby lub ror po dłuższej przerwie

1. Odpalamy konsolę (`cmd`)
2. Przechodzimy do katalogu z projektem, np.:
    `cd C:\bse\kursy ruby helion\GuestBook`
3. Odpalamy Visual Studio Code (. pozostawi konsolę do naszego użytku):
    `code .`
4. W VS Code wchodzimy do menu
    `Terminal -> New terminal`
5. Odpalamy serwer:
    `rails server`
6. W przeglądarce łączymy się do aplikacji:
    <http://localhost:3000/>

## Szybki start na nowym kompie

1. Instalujemy rails zgodnie z [Instrukcja instalacji](/ror_installing).
1. Klonujemy repozytorium.
1. Wykonujemy `bundle install`
1. `rails db:create`
1. `rails db:migrate`
2. `rails db:fixtures:load`
