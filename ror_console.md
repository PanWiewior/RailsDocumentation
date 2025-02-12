# Przydatne polecenia z konsoli

```rails new Post```

```rails console - e development```

```reload!```

## Migrations

```rails generate migration MigrationName```

```rails db:migrate```

```rails db:migrate:status```

```rails db:migrate VERSION=0```

```rails db:migrate VERSION=20230306080918```

```rake db:migrate```

```rake db:rollback```

### Adding New Columns

   migration name "AddColumnToTable" or "RemoveColumnFromTable" and is followed by a list of column names and types (and index)

```rails generate migration AddPartNumberToProducts part_number:string```

```rails generate migration AddPartNumberToProducts part_number:string:index price:decimal```

```rails generate migration RemovePartNumberFromProducts part_number:string```

### Creating associations using references

rails generate migration AddUserRefToProducts user:references

## Models

```rails generate model Task name:string description:text position:integer completed:boolean```

```rails destroy model post```

### Has And Belongs To Many (HABTM) Associations

```rails generate migration CreateJoinTableModel1Model2 table1 table2```

```rails generate migration CreateJoinTableTagTask tags tasks```

### Rich Join Associations

```rails generate model TaskAssignment task:references user:references role:string```

## Przykład wywołań dla nowego projektu

```rails new SquirrelHelper```

```rails new Blog -j esbuild --css bootstrap```

```rails server```

```rails generate model User name:string{30}```

```rails generate scaffold Project title:string{60} description:text{2000} started_at:datetime user:references:index completed:boolean```

```rails generate scaffold DbChange type:string{60} object_name:string{60} object_alias:string{4} project:references:index object_module:string{10}```

```rails generate scaffold DbChangePart type:string{60} column_name:string{60} column_notnull:boolean db_change:references:index column_unique:boolean position:integer column_descr:string{250} column_ref_table_name:string{60} column_domain:string{60} column_name_new:string{60} column_data_type:string{10} column_data_length:integer column_data_precision:integer column_data_scale:integer```

```rails db:create```

```rails db:migrate```

## rails z obsługą bootstrap

Musi być node.js w najnowszej wersji (bo inaczej nie potrafi zrobić automatycznie wpisów w plikach konfiguracyjnych i trzeba je robić ręcznie).

```rails new Blog -j esbuild --css bootstrap```

opis parametru -j: <https://dev.to/davidteren/ruby-on-rails-7-high-performance-frontend-development-with-esbuild-rollup-vite-2onj>

### Pod windowsem może polecieć błąd

```other
 Could not resolve "app/javascript/*.*"

  It looks like you are trying to use glob syntax (i.e. "*") with esbuild. This syntax is typically
  handled by your shell, and isn't handled by esbuild itself. You must expand glob syntax first
  before passing your paths to esbuild.
```

może być tak, że trzeba zmienić wpis w package.json z:

```"build": "esbuild app/javascript/*.* --bundle --sourcemap --outdir=app/assets/builds --public-path=assets",```

na:

```"build": "esbuild app/javascript/application.js --bundle --sourcemap --outdir=app/assets/builds --public-path=assets",```

następnie wydać polecenie: ```yarn build``` (a może i ```yarn build --watch```) i uruchomić serwer

### Inne rozwiązanie

```npm config get script-shell```  
zwraca:  
```null```  
które jest istotnie różne od ```undefined```. Zaś użycie:  
```npm config set script-shell "C:\\Program Files\\git\\bin\\bash.exe"```  
załatwia sprawę błędu i nie trzeba dodatkowych instrukcji wydawać.

Tak czy inaczej, nie udało mi się uruchomić serwera tak, by ogarniał zmiany w kodzie JS.
Dopiero wydanie polecenia  
```yarn build --watch```  
ogarnęło.  
Jeszcze:  
```yarn build:css --watch```

### 2024-02-06

Zmiany w css-ach zaczęły być ogarniane dopiero po wydaniu polecenia:  
```npx nodemon --watch ./app/assets/stylesheets/ --ext scss --exec "yarn build:css"```  
(npx, bo nodemon nie jest zainstalowany globalnie; instrukcja pochodzi z package.json)

W razie czego znalazłem jeszcze takie podejście od odświeżania, ale na razie nie próbowałem (rails_live_reload):
<https://dev.to/aldoportillo/live-reloading-in-rails-5g57>

### 2024-03-11

Jeśli powyższe kończy się błędem:

```other
error code ENOENT
error syscall lstat
error path C:\Users\username\AppData\Roaming\npm
```

To trzeba sprawdzić czy istnieje katalog npm w ścieżce ```C:\Users\username\AppData\Roaming```. U mnie brakowało, po założeniu pustego poszło  dalej.

Dalej poleciał błąd:

```other
[nodemon] starting `yarn build:css`
'yarn' is not recognized as an internal or external command,
operable program or batch file.
```

Po sprawdzeniu:  
```yarn --version```  
okazało się, że nie zna, więc próba zainstalowania:  
```npm install --global yarn```

Pomogło, ale dalej poleciał kolejny błąd:

```other
[nodemon] starting `yarn build:css`
yarn run v1.22.22
$ yarn build:css:compile && yarn build:css:prefix
$ sass ./app/assets/stylesheets/application.bootstrap.scss:./app/assets/builds/application.css --no-source-map --load-path=node_modules
'sass' is not recognized as an internal or external command,
operable program or batch file.
```

No to:  
```npm install --global sass```

Pomogło, ale dalej:

```other
[nodemon] starting `yarn build:css`
yarn run v1.22.22
$ yarn build:css:compile && yarn build:css:prefix
$ sass ./app/assets/stylesheets/application.bootstrap.scss:./app/assets/builds/application.css --no-source-map --load-path=node_modules
Error: Can't find stylesheet to import.
  ╷
1 │ @import 'bootstrap/scss/bootstrap';
  │         ^^^^^^^^^^^^^^^^^^^^^^^^^^
  ╵
  app\assets\stylesheets\application.bootstrap.scss 1:9  root stylesheet
```

Postanowiłem zainstalować na nowo bootstrap (```rails css:install:[tailwind|bootstrap|bulma|postcss|sass]```):
```rails css:install:bootstrap```  
Coś porobił, chociaż na konsolę i tak kilka razy trafiło "The system cannot find the path specified.". W każdym razie po tym polecenie:  
```npx nodemon --watch ./app/assets/stylesheets/ --ext scss --exec "yarn build:css"```  
się niby powiodło.

Dla pewności wywołałem jeszcze zainstalowanie na nowo javascript (```rails javascript:install:[esbuild|rollup|webpack]```):  
```rails javascript:install:esbuild```  
Oczywiście wywaliło błędami, np.:

```other
$ esbuild app/javascript/*.* --bundle --sourcemap --format=esm --outdir=app/assets/builds --public-path=/assets
X [ERROR] Could not resolve "@hotwired/turbo-rails"

    app/javascript/application.js:2:7:
      2 │ import "@hotwired/turbo-rails"
        ╵        ~~~~~~~~~~~~~~~~~~~~~~~
```

### 2024-03-12

Wyczytałem, że yarn nie powinno się instalować poprzez  
```npm install --global yarn```  
ponieważ to przestarzała ścieżka i instaluje yarn z gałęzi 1.x, a to niby nie jest już oficjalnie wspierane w rails. Zrobiłem więc:  
```npm remove yarn```  
```corepack enable``` (z konsoli z poziomu admina)  
```yarn set version stable```  
```yarn install```

Zainstalowało mi to yarn 4.1.1, ale problem  
```X [ERROR] Could not resolve "@hotwired/turbo-rails"```  
nie zniknął.  
Wykonanie  
```yarn add @hotwired/stimulus```  
też nie pomogło.

Znalazłem takie coś:  
```rails turbo:install stimulus:install```  
I po tym  
```rails javascript:install:esbuild```  
już zadziałało.

## Własne CSS-y

Tu jeszcze instrukcja jak własne css-y dodać:
<https://www.youtube.com/watch?v=tYr8yn7yRKw>

Generalnie do ```app/assets/stylesheets/``` dodajemy swój plik, np. ```my_own.scss``` (rozszerzenie ważne ze względu na parametry watcher).  
I teraz 2 ścieżki:

1. Do pliku ```application.bootstrap.scss``` dopisujemy  
     ```@import 'my_own';```  
   Ta ścieżka powoduje, że nasze style zostaną doklejone do ```application.css```, więc trochę trudniej może być się poruszać.
2. (nietestowane) Do pliku ```app/assets/build/manifest.js``` dodajemy wpis:  
     ```//= link my_own.scss```  
   Zaś w pliku ```app/views/layouts/application.html.erb``` (lub innym pożądanym) dodajemy:  
     ```<%= stylesheet_link_tag "my_own", "data-turbo-track": "reload" %>```

## Użycie turbo

Ponieważ turbo ogranicza przeładowywanie stron, zwykłe polecenie render nie będzie miało efektu. Trzeba:  
```render action: 'new', status: 422```  
lub jeszcze lepiej:  
```render :new, status: :unprocessable_entity```

## Wrzucanie na produkcję

Wg kursu, przed wrzuceniem aplikacji na produkcję, powinno się skompilować (nie próbowałem) assety poleceniem:  
```RAILS_ENV=production bundle exec rage assets:precompile```

## Mailer

```rails generate mailer PostsMailer new_post_notification```

## Rubocop a końce linii

W środowisku Windows, jeśli używamy znaku końca linii LF, to rubocop może produkować komunikat ```Carriage return character missing. (convention:Layout/EndOfLine)```. Wówczas w katalogu głównym projektu należy utworzyć plik ```.rubocop.yml``` o zawartości:

```other
Layout/EndOfLine:
  EnforcedStyle: lf
```

Z tym, że rzuca to komunikatem:

```other
The following cops were added to RuboCop, but are not configured. Please set Enabled to either `true` or `false` in your `.rubocop.yml` file.

Please also note that you can opt-in to new cops by default by adding this to your config:
  AllCops:
    NewCops: enable
Gemspec/DeprecatedAttributeAssignment: # new in 1.30
  Enabled: true
[...]
For more information: https://docs.rubocop.org/rubocop/versioning.html
```

więc zmieniłem zawartość pliku na:

```other
AllCops:
  NewCops: enable

Layout/EndOfLine:
  EnforcedStyle: lf
```
