---
marp: true
theme: gaia
header: '![logo](./Capgemini_Logo_Color.png)'
footer: 'DevHours Docker | Piotr Ptak & Dawid Kolbusz | 09.10.2021'
---
<style>
  :root {
    --color-background: #fff !important;
    --color-foreground: #0070AD !important;
    --color-highlight: #12ABDB !important;
    --color-dimmed: #2B0A3D !important;
  }
  code {
    background-color: #272936 !important;
  }

  header {
    display: flex;
    justify-content: right;
  }

  header > img {
    margin-top: 0.5em;
    width: 7.97em;
    height: 2em;
  }

  footer {
    font-size: small;
  }
</style>

<style scoped>
</style>

<!-- _class: lead -->

# DevHours 2021

## Content

Welcome text

<!-- any notes -->

---

# Agenda

- :white_large_square: Uruchomienie pierwszego kontenera
- :white_large_square: Podstawowe pojęcia
- :white_large_square: Konteneryzacja aplikacji backend
- :white_large_square: Konteneryzacji aplikacji frontend

---

# Autorzy

Cośtam o nas

---

# Wprowadzenie

Janek jest junior developerem

---

# Demo 1 Hello World

https://docs.docker.com/get-started/


<!-- w takim razie wpisujemy w google docker get started i wybieramy pierwszy od góry link -->

<!-- Na stronie znajdujemy informację, żeby w konsoli uruchomić polecenie -->

```sh
$ docker run -p 80:80 docker/getting-started
```

<!-- I co teraz w ogóle się stało? Właśnie uruchomiliśmy nasz pierwszy kontener -->

---

# Podstawowe Pojęcia

## 
* Kontener - proces działający na danym komputerze, odizolowany na poziomie systemu operacyjnego od innych procesów.

<!-- można dodać, że odizolowany za pomocą namespaces i cgroups, które są częścią linuxowego kernela -->

## 
* Obraz - jest to szablon który zawiera całą konfigurację i wszystkie źrodła oraz zależności potrzebne do działania kontenera.

<!-- wszystkie pliki binarne, zmienne środowiskowe, system plików itp -->

##
* Rejestr - miejsce do przechowywania i udostępniania innym obrazów. Na przykład hub.docker.com

<!-- docker hub jest chyba największy i najpopularniejszy, ale możemy tworzyć swoje własne prywatne repozytoria, co zobaczymy później -->

---

# Podstawowe Pojęcia c.d.

## Docker daemon

## Docker host

---

# Architektura

![height:12em](https://docs.docker.com/engine/images/architecture.svg)
https://docs.docker.com/get-started/overview/

---

# Demo 2 Aplikacja backend - obraz

```Dockerfile
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

COPY *.csproj .
RUN dotnet restore

COPY . .
RUN dotnet publish -c release -o /app --no-restore

FROM mcr.microsoft.com/dotnet/runtime:5.0
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "dotnetapp.dll"]
```
<!-- 
Kopiujemy podstawowy dockerfile znaleziony w przykładach dockera. 
https://docs.docker.com/samples/dotnetcore/
Musimy na początku zmienić nazwy na naszą aplikację i dodać osobno wszystkie projekty w ramach solucji które utworzyliśmy.  -->

<!-- Robimy tak dlatego, że chcemy aby pobieranie zależności było w innej warstwie niż kopiowanie plików samej aplikacji i budowanie jej, ponieważ oznacza to że przy budowie obrazu będzie można użyć potwórnie warstwy z zależnościami jeśli zmienimy tylko aplikacji a nie nugety. -->

<!-- Budujemy obraz z szablonu który napisaliśmy za pomocą komendy
docker build -t devhours.cloudnative.api . -->

<!-- Możemy teraz przetestować wszystko uruchamiając kontener z pomocą obrazu który stworzyliśmy wcześniej za pomocą komendy
uruchamiamy docker run devhours.cloudnative.api -->

<!-- Mamy błąd, że nie podano konfiguracji - musimy przekazać zmienną środowiskową ASPNETCORE_ENVIRONMENT aby załadować odpowiedni plik appsettings 
docker run -e ASPNETCORE_ENVIRONMENT="Development" devhours.cloudnative.api -->

<!-- teraz nie ma błędu, zanim jednak przejdziemy od razu dalej to czy można zmienić konfigurację, tak żeby zamiast ładować ją z pliku appsettings można podać zmienne środowiskowe?
docker run -e Cors__AllowedOrigins__0="http://localhost:4200" devhours.cloudnative.api -->

<!-- Super, udało się, natomiast dalej nie udostępniliśmy aplikacji na żadnych portach, spróbujmy więc to zrobić na :5000
docker run -e Cors__AllowedOrigins__0="http://localhost:4200" -p 5000:80 devhours.cloudnative.api -->

---

# Demo 3 Aplikacja frontend - obraz

```Dockerfile
FROM node:10-alpine as builder

COPY package.json package-lock.json ./
RUN npm install

COPY . .
RUN npm run build

FROM nginx:1.16.0-alpine
COPY --from=builder /react-frontend/build /usr/share/nginx/html
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx/nginx.conf /etc/nginx/conf.d
CMD ["nginx", "-g", "daemon off;"]
```

<!-- Kopiujemy przykładowy dockerfile z artykułu w internecie https://dev.to/subhransu/nevertheless-subhransu-maharana-coded-5eam -->

<!-- Ustawimy sobie inny katalog w którym będziemy pracować, zmienimy wersję noda do budowania na nowszą nginx tak samo -->

<!-- Żeby to wszystko zadziałało potrzebujemy utworzyć sobie plik konfiguracyjny nxing który kopiujemy z internetu -->

<!-- Potrzebujemy zmienić ścieżkę do kopiowania nginx -->

<!-- Dodamy również plik dockerignore aby nie kopiować do obrazu niepotrzebnych rzeczy zajmujących miejsce i spowalniające cały proces wykonania -->

<!-- Spróbujmy to wszystko zbudować za pomocą 
docker build -t devhours.cloudnative.frontend . -->

<!-- To byłoby prawie wszystko, ale musimy niestety podać jeszcze adres naszego api. Najłatwiej byłoby podać go w zmiennej środowiskowej tak samo jak konfigurowaliśmy backend. Nie jest to niestety tak proste jak tam, ponieważ adres jest zapisywany po prostu w pliku javascript przy tworzeniu obrazu dlatego musimy go podmienić w momencie uruchamiania kontenera. -->

<!-- Żeby to zrobić dodajmy skrypt sh w którym podmienimy wskazaną wartość używając komendy sed.
Musimy również dodać entrypoint do naszego dockerfile -->

<!-- Zbudujmy nasz obraz
docker build -t devhours.cloudnative.frontend .
oraz spróbujmy uruchomić kontener
docker run -e webapi="http://localhost:5000/api" -p 4200:80 devhours.cloudnative.frontend -->

---

# Kilka słów o docker-compose i orchestracji

---

# Demo 4 docker compose

---

# Napisać coś o rejestrach

---

# Demo 5 Opublikowanie obrazu

---

# Co to jest ACI, jakie są opcje wdrożenia

---

# Demo 6 Wdrożenie do Azure

---

# Kilka słów o CI/CD

---

# Demo 7 CI/CD dla aplikacji frontend/backend

---

# Najlepsze praktyki pisania obrazów

---

# Podsumowanie

- :white_check_mark: Uruchomienie pierwszego kontenera
- :white_check_mark: Podstawowe pojęcia
- :white_check_mark: Konteneryzacja aplikacji backend
- :white_check_mark: Konteneryzacji aplikacji frontend