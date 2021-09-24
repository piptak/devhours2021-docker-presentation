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

O czym w ogóle jest ta prezentacja

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

---

# Demo 3 Konteneryzacja aplikacji frontend

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