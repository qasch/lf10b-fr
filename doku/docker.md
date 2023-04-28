# Docker

Docker ist **keine** Virtualisierung

Docker kann benutzt werden, um einzelne Anwendungen plus aller dazu benötigten Abhängigkeiten in einem **Container** bereitzustellen

Die Anwendung läuft dann ähnlich wie in der Virtualisierung in einer Art **Sandbox**, also abgeschottet vom restlichen Betriebssystem

Die Anwendungen kommen also nicht mit anderen Anwendungen "in die Quere"

**Und vor allem:**

Wird eine Anwendungen in einem Container kompromitiert, hat dies keine Auswirkungen auf das restliche Betriebssystem

---

## Container

Ein Container ist soetwas wie eine **super-light-weight Virtual Machine**

Mit einer entscheidenden Einschränkung: 

Es können nur Container für das jeweilige OS bereitgestellt werden

---

## Unterschied von Virtualisierung und Containern

### Virtualisierung

Was wird alles virtualisiert?

* komplette Hardware (über den Hypervisort [Typ I bzw. Typ II]) wie Ein- und Ausgabegeräte, Netzwerkkarten etc.
* ein komplettes OS mit unter anderem:
    * Bootloader
    * Kernel
    * Init System
    * sämtlichen Anwendungen, die (mindestens) in einem OS laufen
    * die zu bereitzustellende Anwendung selbst
    * etc. etc.

Dies sorgt in den meissten Fällen für einen ziemlichen Overhead, also unnütz verbrauchten Resourcen

---

## Unterschied von Virtualisierung und Containern

### Container

Was wird alles "containerisiert"?

* Dateisystembaum (-> `chroot`)
* die bereitzustellende Anwendunge
* eigenes Netzwerk (Host only)

Container greifen auf den Kernel und die Hardware des Host-Betriebssystems zu

Dadurch benötigen Container extrem wenig Resourcen, sind sehr schnell erstellt und gestartet und einfach übertragbar

---

## Vorteile von Containern

Auf einem System können problemlos **hunderte** Container laufen

In einem Container läuft *idealerweise* eine **einzelne** Anwendung

Komplexere Systeme (z.B. eine Webanwendung mit Datenbank) bestehen dann aus mehreren Containern

Solche Systeme können durch eine einzelne (einfach aufgebaute) Text-Datei beschrieben werden und mit einem einzelnen Kommando (`docker-compose up`) gestartet werden

---

## Images und Container

Ein Container wird bei Bedarf gebaut und gestartet - aus einem **Image** heraus

Ein **Image** ist ein Bauplan für eine beliebige Menge an Containern

Images können z.B. vom **Docker Hub** unter https://hub.docker.com heruntergeladen werden 

Bzw. geschieht das automatisch beim Starten eines Containers, falls das benötigte Image lokal nicht vorhanden ist.
 
---
 
## Container sind "Wegwerfprodukte"

Daten werden in Containern nicht gespeichert (sofern wir nicht auf anderem Wege dafür sorgen) 

Docker-Container sind z.B. dafür gedacht, Dinge einmal schnell auszuprobieren

Docker-Container können aber auch produktiv eingesetzt werden, die Daten müssen dann aber auf dem **Host-System** gespeichert werden (über sog. **Volumes**)

---

## Docker - grundlegende Kommandos

### Starten von Containern

Image vom Docker Hub herunterladen

```
docker pull <image>
```

Container starten 

```
docker run <image>
```

Container mit interaktivem Terminal starten 

```
docker run -it <image> /bin/bash
```

Ein bestimmtes Kommando im Container ausführen

```
docker run <image> <command>

docker run <image> ls -l
```

---

## Docker - grundlegende Kommandos

### Hintergrund und gestoppte Container

Container im Hintergrund starten 

```
docker run -d <image>
```

In laufenden Container wechseln

```
docker exec -it <container-id/name> /bin/bash
```

Container stoppen

```
docker stop <container-id/name>
```

gestoppten Container starten

```
docker start <container-id/name>
```

## Docker - grundlegende Kommandos

### Ports weiterleiten

Den internen Docker-Port 80 auf den Port 8080 des Host Systems weiterleiten

```
docker run -p 8080:80 <image>
```

### Volumes anlegen

Das interne Verzeichnis `/some/folder/inside/container` auf das Verzeichnis `/some/local/folder/on/host` des Host-Systems spiegeln

```
docker run -v /some/local/folder/on/host:/some/foler/inside/container <iamge>
```

---

## Docker - aufräumen

Da Container immer neu gebaut werden, sammeln sich mit der Zeit einige ungenutzte an. Diese sollte man von Zeit zu Zeit aufräumen, allein schon um Namenskonflikten vorzubeugen.

Nur gestoppte Container können entfernt werden:

```
docker rm <container-id/name>
```

### alles aufräumen

Mit dem folgenden Kommando werden nicht mehr verwendete Container, Netzwerke etc auf einmal entfernt werden:

```
docker system prune
```

---

## Doker Compose

Mit `docker compose` können mehrere Container auf einmal gestartet und komfortabel über eine YAML-Datei konfiguriert werden.

Vorgehen:

1. Verzeichnis erstellen, z.B. für den einen Webserver `mkdir webserver`
2. In diesem Verzeichnis einen Datei mit Namen `docker-compose.yml` erstellen mit folgendem Inhalt:

```
version: '3.7'

services:
  apache:
    image: httpd
    ports:
      - 8888:80
    volumes:
      - ./public-html:/usr/local/apache2/htdocs/
```
3. Kommando `docker compose up` ausführen

4. Oder `docker compose up -d` um den Prozess im Hintergrund auszuführen

## Docker - Volumes

Um Daten persistent zu speichern, können **Volumes** verwendet werden.

Dabei wird ein Verzeichnis innherhalb des Containers auf ein Verzeichnis des Hostsystems gespiegelt.


```
version: '3.7'

services:
  apache:
    image: httpd
    ports:
      - 8888:80
    volumes:
      - ./public-html:/usr/local/apache2/htdocs/
```

Im Verzeichnis `public-html` im aktuellen Verzeichnis auf dem Host kann jetzt z.B. eine `index.html` mit beliebigem Inhalt angelegt werden:

```
<h1>Docker Compose mit Volumes</h1>
<p>Die Daten hier bleiben persistent</p>
```

Der Container startet jetzt und gibt den die hinterlegte `index.html` Seite aus
