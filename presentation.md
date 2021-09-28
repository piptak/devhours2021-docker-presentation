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

# DevHours: Cloud Native

## Docker

<!-- any notes -->

---

# Agenda

- :white_large_square: Uruchomienie pierwszego kontenera
- :white_large_square: Podstawowe pojęcia
- :white_large_square: Konteneryzacja aplikacji backend
- :white_large_square: Konteneryzacji aplikacji frontend

---

# Autorzy

<style scoped>
  p > img {
    padding-left: 4.5em;
    padding-right: 4.5em;
  }
</style>

![Piotr Ptak](https://www.capgemini.com/pl-pl/wp-content/uploads/sites/15/2021/09/DevHours-prelegenci-1.png) ![Dawid Kolbusz](https://www.capgemini.com/pl-pl/wp-content/uploads/sites/15/2021/09/DevHours-prelegenci.png)



---

# Wprowadzenie

<style scoped>
  p > img {
    margin-left: auto;
    margin-right: auto;
    display: block;
  }
  p > a {
    font-size: 0.3em;
  }
  p:nth-of-type(1) {
    text-align: center;
  }
</style>

![height:8em](https://res.cloudinary.com/practicaldev/image/fetch/s--08z6gIzC--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://thepracticaldev.s3.amazonaws.com/i/l9w3sg7lfal6o0ewhxht.jpg) https://dev.to/kelvin0712/junior-dev-my-experiences-during-my-first-job-as-a-developer-17b2

Opowiemy wam hostorię Janka, który zatrudnił się w firmie jako junior developer i otrzymał zadanie konteneryzacji aplikacji

---

# Dlaczego docker?

* Szybkość i lekkość uruchomienia
* Niezależność od systemu operacyjnego
* Powtarzalność i automatyzacja
* Łatwość wycofania zmian
* Modularność i skalowanie

<!-- Tworzenie kontenera zajmuje sekundy w porównaniu z minutami w przypadku maszyn wirtualnych i zajmuje dużo mniej pamięci i procesora ponieważ nie jest wymagany osobny system operacyjny -->

<!-- Bez względu czy kontener jest uruchamiany na windowsie, ubuntu czy redhacie czy jeszcze innym systemie, kontener działa zawsze tak samo -->

<!-- Budowanie obrazu i uruchamianie kontenerów wygląda tak samo, obsługa za pomocą docker API, łatwo automatyzować w procesie CI/CD -->

<!-- W przypadku awarii łatwo jest uruchomić kontener korzystając z poprzedniej wersji obrazu -->

<!-- Łatwo jest zwiększać zasoby konkretnych komponentów aplikacji, nie trzeba zwiększać wszystkiego, tylko elementy mające problemy z wydajnością -->

---

# Demo 1 Hello World

https://docs.docker.com/get-started/


<!-- Janek nie ma pojęcia co to docker więc żeby się dowiedzieć wpisujem w google docker get started i wybiera pierwszy od góry link -->

<!-- Na stronie znajduje informację, żeby w konsoli uruchomić polecenie -->

```sh
$ docker run -p 80:80 docker/getting-started
```

<!-- I co teraz w ogóle się stało? Janek właśnie uruchomił swój pierwszy kontener -->

<!-- docker/getting-started w tym przykładzie to nazwa obrazu którą trzeba podać przy uruchamianiu kontenera. Co to jest kontener i obraz dowiecie się za chwilę. -->

---

# Podstawowe Pojęcia

##
* Docker - narzędzie do tworzenia, uruchamiania i wdrażania aplikacji dzięki któremu developerzy pracują na tym samym środowisku na którym działa ona dla klientów

## 
* Obraz - jest to szablon który zawiera całą konfigurację i wszystkie źrodła oraz zależności potrzebne do działania kontenera.

<!-- wszystkie pliki binarne, zmienne środowiskowe, system plików itp -->

## 
* Kontener - proces działający na danym komputerze, odizolowany na poziomie systemu operacyjnego od innych procesów.

<!-- można dodać, że odizolowany za pomocą namespaces i cgroups, które są częścią linuxowego kernela -->

<!-- po konteneryzacji nasza aplikacja działa jako kontener -->

---

# Podstawowe Pojęcia c.d.

##
* Rejestr - miejsce do przechowywania i udostępniania innym obrazów. Na przykład hub.docker.com

<!-- docker hub jest chyba największy i najpopularniejszy, ale możemy tworzyć swoje własne prywatne repozytoria, co zobaczymy później -->

## 
* Docker daemon - to prawdziwe serce dockera które zarządza wszystkimi obrazami i kontenerami oraz nasłuchuje przychodzących instrukcji od klienta

##
*  Docker client - to narzędzie z którego korzystamy aby komunikować się z demonem i wydawać mu polecenia

<!-- na przykład stwórz obraz czy uruchom kontener -->

---

# Podstawowe polecenia - Kontener

* ```$ docker run <image-name>``` - uruchamia nowy kontener
* ```$ docker start <container-name>``` - startuje istniejący kontener
* ```$ docker stop <container-name>``` - zatrzymuje działający kontener
* ```$ docker logs <container-name>``` - wyświetla logi z działania
* ```$ docker exec <container-name> <command>``` - pozwala uruchomić polecenie wewnątrz kontenera
* ```$ docker container ls``` - wyświetla listę działających kontenerów

---

# Podstawowe polecenia - Obraz

* ```$ docker build .``` - utwórz obraz z pliku Dockerfile
* ```$ docker pull <image-name>``` - pobierz obraz z repozytorium
* ```$ docker push <image-name>``` - opublikuj obraz w rejestrze
* ```$ docker image rm <image-name>``` - usuń lokalnie pobrany obraz
* ```$ docker image ls``` - wyświetl listę pobranych obrazów

---

<style scoped>
  p > img {
    margin-left: auto;
    margin-right: auto;
    display: block;
  }
  p > a {
    font-size: 0.3em;
  }
  p:nth-of-type(1) {
    text-align: center;
  }
</style>

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

# Kilka słów o orchestracji

* Orchestracja - proces konfigurowania i zarządzania komponentami takimi jak komunikacja, zmienne środowiskowe, storage 
* docker-compose - narzędzie służące do orchestracji w środowisku docker

---

# Demo 4 docker compose

```yml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

<!-- przykład ze strony https://docs.docker.com/compose/ -->

---

<style scoped>
  p > img {
    margin-left: auto;
    margin-right: auto;
    display: block;
  }
  p > a {
    font-size: 0.3em;
  }
  p:nth-of-type(1) {
    text-align: center;
  }
</style>

# Struktura rejestru

![width:16em](https://docs.microsoft.com/pl-pl/azure/container-registry/media/container-registry-concepts/registry-elements.png)
https://docs.microsoft.com/pl-pl/azure/container-registry/container-registry-concepts

---

# Demo 5 Opublikowanie obrazu

```sh
$ docker login
$ docker tag <local-image> <registry>/<repository>/<image>:<version>
$ docker push <registry>/<repository>/<image>:<version>
```

### Opcjonalnie

```sh
az group create --name myResourceGroup --location eastus
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
```

---

# Wdrożenie

## Azure Container Instances

Usługa w chmurze Azure do uruchamiania skonteneryzowanych aplikacji dockerowych

---

# Demo 6 Wdrożenie do Azure

```sh
az container create \ 
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --dns-name-label aci-demo \
  --ports 80
```

---

# CI/CD



---

# Demo 7 CI/CD

```yaml
# action.yml
name: 'Hello World'
description: 'Greet someone and record the time'
inputs:
  who-to-greet:  # id of input
    description: 'Who to greet'
    required: true
    default: 'World'
outputs:
  time: # id of output
    description: 'The time we greeted you'
runs:
  using: 'docker'
  image: 'Dockerfile'
  args:
    - ${{ inputs.who-to-greet }}
```

---

# Wady docker

---

# Najlepsze praktyki pisania obrazów

https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

---

# Podsumowanie

- :white_check_mark: Uruchomienie pierwszego kontenera
- :white_check_mark: Podstawowe pojęcia
- :white_check_mark: Konteneryzacja aplikacji backend
- :white_check_mark: Konteneryzacji aplikacji frontend