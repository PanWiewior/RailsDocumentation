# Instalacja rails na nowym kompie (stan na 2024-02, Rails 7.1)

## 1. Ruby

Ze strony <https://rubyinstaller.org/downloads/>,
wersja rekomendowana, np. Ruby+Devkit 3.2.3-1 (x64).

## 2.a. SQLite

Ze strony <https://www.sqlite.org/download.html>
ściągamy `sqlite-tools-win-x64-3450100.zip`, rozpakowujemy np. do `c:\sqlite3`,
dodajemy zmienną środowiskową do tego katalogu.

## 2.b. Postgres i Pgadmin

Postgres zgodnie z instrukcjami https://gorails.com/setup/ubuntu/26.04. Wg informacji zawartych [tutaj](https://www.digitalocean.com/community/tutorials/how-to-use-postgresql-with-your-ruby-on-rails-application-on-ubuntu-20-04) potrzeba roli o tej samej nazwie co nazwa użytkownika:  
`sudo -u postgres createuser chris -s`

W każdym razie podczas instalacji automatycznie zakładany jest użytkownik o nazwie `postgres`. Aby się na niego przełączyć wydajemy polecenie:  
`sudo -i -u postgres`  
Możemy teraz odpalić konsolę PostgreSQL:  
`psql`

Warto od razu zdefiniować hasło użytkownikowi postgres, ponieważ bez tego nie udało mi się zalogować przy użyciu pgAdmin:  
`ALTER USER postgres WITH PASSWORD 'your_password'`

---

Pgadmin na podstawie [artykuł medium](https://medium.com/yavar/install-and-configure-postgresql-and-pgadmin-on-ubuntu-20-04-22-04-52c52c249b9e) albo [oficjalna strona](https://www.pgadmin.org/download/pgadmin-4-apt/):

`curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg`

`sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'`

`sudo apt install pgadmin4`

Nie umiem na ten moment odpalać z poziomu konsoli i odpalam z poziomu aplikacji.

## 3. a. Rails on Linux

Ponieważ pojawia się komunikat:

```other
mise WARN  deprecated [idiomatic_version_file_enable_tools]: 
Idiomatic version files like ~/devel/ruby/myapp_test/.ruby-version are currently enabled by default. However, this will change in mise 2025.10.0 to instead default to disabled.

You can remove this warning by explicitly enabling idiomatic version files for ruby with:

    mise settings add idiomatic_version_file_enable_tools ruby

You can disable idiomatic version files with:

    mise settings add idiomatic_version_file_enable_tools "[]"

See https://github.com/jdx/mise/discussions/4345 for more information.
```

wykonałem na pałę:

`mise settings add idiomatic_version_file_enable_tools ruby`

## 3. b. Rails on Windows

Komendą `gem install rails`.
Niestety u mnie poleciały błędy:

```other
Installing required msys2 packages: mingw-w64-ucrt-x86_64-libyaml
error: failed to init transaction (unable to lock database)
error: could not lock database: Permission denied
pacman failed with the following output:
Building native extensions. This could take a while...
ERROR:  Error installing rails:
        ERROR: Failed to build gem native extension.

    current directory: C:/Users/username/.local/share/gem/ruby/3.2.0/gems/psych-5.1.2/ext/psych
C:/Ruby32-x64/bin/ruby.exe extconf.rb
checking for yaml.h... no
yaml.h not found
*** extconf.rb failed ***
Could not create Makefile due to some reason, probably lack of necessary
libraries and/or headers.  Check the mkmf.log file for more details.  You may
need configuration options.
```

Znalezione rozwiązanie na necie:  
`gem install yaml`  
na końcu była też informacja:  

```other
A new release of RubyGems is available: 3.4.19 → 3.5.6!
Run `gem update --system 3.5.6` to update your installation.
```

więc wykonałem  
`gem update --system 3.5.6` (ale z poziomu admina).

Jeszcze raz:  
``gem install yaml`` (tym razem z poziomu admina)

i jeszcze raz:  
`gem install rails` (też z poziomu admina)

Tym razem poszło.

### Update 2024-02-29

Jednak przy próbie utworzenia nowego projektu rails otrzymałem:  

```other
Installing psych 5.1.2 with native extensions
Gem::Ext::BuildError: ERROR: Failed to build gem native extension.

current directory:
C:/Ruby32-x64/lib/ruby/gems/3.2.0/gems/psych-5.1.2/ext/psych
C:/Ruby32-x64/bin/ruby.exe extconf.rb
checking for yaml.h... no
yaml.h not found
```

`gem install yaml` - nie pomogło

`gem install psych` - poszło i pomogło

Niestety poleciał kolejny błąd:

```other
  Install esbuild
         run    yarn add esbuild from "."
  Add build script
bin/rails aborted!
Rails::Generators::Error: The template [C:/Ruby32-x64/lib/ruby/gems/3.2.0/gems/jsbundling-rails-1.3.0/lib/install/esbuild/install.rb] could not be loaded. Error: No such file or directory - npx -v (Rails::Generators::Error)

Caused by:
Errno::ENOENT: No such file or directory - npx -v (Errno::ENOENT)
```

ściągnąłem więc `node-v20.11.1-x64.msi` ze strony <https://nodejs.org/en/download/> i zainstalowałem ze standardowymi ustawieniami.

## 4. Visual Studio Code

Ze strony <https://code.visualstudio.com>.  
Na starym laptopie miałem takie dodatki: Ruby SolarGraph (Castwide), Ruby (Peng Lv), VSCode Ruby (Stafford Brunk), ruby-rubocop (misogi), endwise (KaiWood).  
Na nowym laptopie postanowiłem zrezygnować z dodatków Ruby oraz VSCode Ruby, które są deprecated. W ich miejsce zainstalowałem Ruby LSP (Shopify). Na razie postanowiłem nie instalować SolarGraph, choć wg informacji na necie, mogą działać obok siebie.

## 5. Dodatek do VS: Ruby LSP (Shopify) (update 2026-02-02)

W miejsce wygaszanych Ruby (Peng Lv), VSCode Ruby (Stafford Brunk). Na razie bez SolarGraph, ale zobaczymy. Update 2026-02-02: nadal tylko Ruby LSP.

## 6. Dodatek do VS: endwise (Kai Wood) (update 2026-02-02)

Podobno nie warto przy korzystaniu z Copilota, ale nie gryzie się z Ruby LSP i można go doinstalować. Zobaczę.

## 7. RuboCop (update 2026-02-02)

Od czasu Rails 7.2 domyślnie jest dodawany (w Gemfile) gem rubocop-rails-omakase. Jest to zestaw narzędzi i ustawień starannie dobrany przez twórców Rails.

Pod spodem nadal działa standardowy Rubocop, ale ten gem dostarcza plik .rubocop.yml z "sensownymi domyślnymi ustawieniami" zgodnymi z estetyką twórców Rails. W przeciwieństwie do standardowego Rubocopa, który bywa bardzo restrykcyjny, wersja Omakase skupia się na podstawach formatowania i spójności, unikając zasad, które często irytują programistów.

Zatem nie trzeba nic instalować. Można w konsoli wpisać `bundle exec rubocop -v` lub `bundle info rubocop` by poznać wersję zainstalowanego rubocopa.

Wg AI dodatkowe gemy dodaje się tylko wtedy, gdy chcemy rozszerzyć analizę o specyficzne biblioteki, których Omakase nie obsługuje domyślnie:

1. rubocop-rspec: Jeśli używasz RSpec zamiast Minitest (Omakase nie zawiera reguł dla RSpec).
1. rubocop-performance: Jeśli zależy Ci na dodatkowych sugestiach dotyczących wydajności kodu Ruby.
1. rubocop-capybara: Do sprawdzania testów systemowych/integracyjnych.

Na razie jednak nic jeszcze nie instalowałem.

## 8. Dodatek do rubocop w VS: niepotrzebny (update 2026-02-02)

Wcześniej używałem RuboCop (RuboCop Headquarters), ale Ruby LSP domyślnie wykrywa rubocop i się sam integruje.

## 9. Konfiguracja rubocop na poziomie projektu (update 2026-02-02)

Plik `.rubocop.yml` o zawartości:

```other
# Omakase Ruby styling for Rails
inherit_gem: { rubocop-rails-omakase: rubocop.yml }

Style/EndOfLine:
  EnforcedStyle: lf
```

## 10. Dodatek do VS: VS Code Speech (Microsoft)

Na razie testowo. Oferuje rozpoznawanie mowy poprzez CTRL+ALT+V i Esc.

## 11. Emmet w plikach .erb

Otworzyć ustawienia (`CTRL+`, lub `File -> Preferences -> Settings`), wyszukać `Emmet` i w części `Include Languages` dodać `Item: erb, Value: html`. W pliku `settings.json` powinien powstać wpis:

```json
"emmet.includeLanguages": {
    "erb": "html"
}
```

## 12. Własne snippety (update 2026-02-02)

Konfiguracja dla plików **ERB**:

1. W VS Code nacisnąć `Ctrl + Shift + P`.
1. Wpisać **"Snippets: Configure Snippets"** i wybierz tą opcję.
1. Wyszukać na liście język **erb** (jeśli nie widać, wybrać `html` – snippety często działają w obu, ale erb jest precyzyjniejszy).
W otwartym pliku JSON, wewnątrz klamer `{ ... }`, wkleić poniższy kod:

```json
{
  "ERB Output Tag": {
    "prefix": "er",
    "body": [
      "<%= $1 %>"
    ],
    "description": "Wstawia tag wyświetlający <%= %>"
  },
  "ERB Execution Tag": {
    "prefix": "re",
    "body": [
      "<% $1 %>"
    ],
    "description": "Wstawia tag logiki <% %>"
  }
}
```

Aby snippety pojawiały się na samej górze podpowiedzi i działały błyskawicznie, sprawdzić w `settings.json` (lub w ustawieniach UI), czy jest:

```json
"editor.snippetSuggestions": "top"
```

## 13. Dodatek do VS: Stimulus LSP (Marco Roth) (update 2026-02-02)

Wg AI: konfiguracja podpowiedzi:

Dodać do swojego `settings.json`, aby podpowiedzi Stimulusa pojawiały się błyskawicznie podczas pisania:

```json
{
  "stimulusLsp.controllerRoots": [
    "app/javascript/controllers"
  ],
  "editor.quickSuggestions": {
    "other": true,
    "comments": false,
    "strings": true
  }
}
```

**Pro-tip: Łączenie ze snippetami**  
Skoro mamy już własne snippety do ERB, dodajemy jeden, który błyskawicznie tworzy szkielet dla kontrolera Stimulus w widoku:

- **Prefix:** `stc` (Stimulus Controller)
- **Body:** `<div data-controller="$1">\n $2\n</div>`

Dzięki temu jednym skrótem tworzymy kontener, a **Stimulus LSP** od razu podpowie nam nazwy naszych kontrolerów w miejscu `$1`.

## 14. Dodatek do VS: Rails DB Schema (aki77) (update 2026-02-02)

Pozwala szybko podejrzeć atrybuty modelu bez zaglądania do pliku `schema.rb`.

## 15. Dodatek elia.erb-formatter (Elia Schito) (update 2026-02-02)

`elia.erb-formatter` zamiast ERB Formatter/Beautify (Ali Ariff) bo:

- jest zalecany przez społeczność Rails,
- lepiej formatuje HTML+ERB,
- nie ma konfliktów z Ruby LSP,
- cechuje się lekkim działaniem

Dodatkowo w pliku settings.json należy dodać:

```json
    "[erb]": {
        "editor.defaultFormatter": "elia.erb-formatter",
        "editor.formatOnSave": true,
        "editor.formatOnPaste": true
    },
    "files.associations": {
        "*.html.erb": "erb"
    },
    "erb-formatter.lineLength": 120
```

## 16. Dodatek do VS: vscode-icons (VSCode Icons Team) (update 2026-02-02)

## 17. Dodatkowa konfiguracja GIT

Opisana w private-docs, np. dostosowanie bash w Ubuntu, by pokazywał informacje jak git-bash pod Windowsem.
