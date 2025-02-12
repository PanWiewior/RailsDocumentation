# Instalacja rails na nowym kompie (stan na 2024-02, Rails 7.1)

## 1. Ruby

Ze strony <https://rubyinstaller.org/downloads/>,
wersja rekomendowana, np. Ruby+Devkit 3.2.3-1 (x64).

## 2. SQLite

Ze strony <https://www.sqlite.org/download.html>
ściągamy `sqlite-tools-win-x64-3450100.zip`, rozpakowujemy np. do `c:\sqlite3`,
dodajemy zmienną środowiskową do tego katalogu.

## 3. Rails

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

## 5. Dodatek do VS: Ruby LSP (Shopify)

W miejsce wygaszanych Ruby (Peng Lv), VSCode Ruby (Stafford Brunk). Na razie bez SolarGraph, ale zobaczymy

## 6. Dodatek do VS: endwise (Kai Wood)

## 7. gem rubocop

`gem install rubocop`

## 8. Dodatek do VS: RuboCop (RuboCop Headquarters)

Wcześniej używałem ruby-rubocop (misogi), ale przestał być wspierany.

## 9. Konfiguracja rubocop na poziomie projektu

Plik `.rubocop.yml` o zawartości:

```other
AllCops:
  NewCops: enable

Layout/EndOfLine:
  EnforcedStyle: lf
```

## 10. Dodatek do VS: VS Code Speech (Microsoft)

Na razie testowo. Oferuje rozpoznawanie mowy poprzez CTRL+ALT+V i Esc.

## 11. Emmet w plikach .erb

Otworzyć ustawienia (`CTRL+`, lub `File -> Preferences -> Settings`), wyszukać `Emmet` i w części `Include Languages` dodać `Item: erb, Value: html`. W pliku `settings.json` powinien powstać wpis:

```other
"emmet.includeLanguages": {
    "erb": "html"
}
```

## 12. Dodatek do VS: ERB Helper Tags (Rayhan Wirjowerdojo)

## 13. HtmlBeautifier

`gem install htmlbeautifier`

## 14. Dodatek do VS: ERB Formatter/Beautify (Ali Ariff)

Dodatkowo w pliku settings.json należy dodać:

```other
"[erb]": {
  "editor.defaultFormatter": "aliariff.vscode-erb-beautify",
  "editor.formatOnSave": true
},
"files.associations": {
  "*.html.erb": "erb"
}
```

## 15. Dodatek do VS: vscode-icons (VSCode Icons Team)
