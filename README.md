<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/hero-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/hero-light.svg">
  <img src="assets/hero-dark.svg" alt="Alex. Tüftler zwischen Software und Hardware." width="100%">
</picture>

</div>

Ich schreibe Laravel-Code und flashe danach ESP32-Boards. Ich betreibe einen eigenen Server, weil meine Daten bei mir bleiben sollen.
Ich lerne durch Machen: erst bauen, dann kaputtmachen, dann verstehen, warum.

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/orbit-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/orbit-light.svg">
  <img src="assets/orbit-dark.svg" alt="Alex in der Mitte, drumherum Homeserver, ESP32, Katzenpfote, Mini-PC, Tastatur und Docker-Wal." width="560">
</picture>

</div>

## Beweisstücke

<table>
<tr>
<td width="50%" valign="top">

**Homeserver-Dashboard**
*privat, läuft bei mir zu Hause*

Zwölf Apps für Geld, Licht, Serien und Aufgaben. Jetzt eine Oberfläche auf einem Server, ohne Cloud-Zwang. Läuft auf meinem Server. Meistens.

`Laravel` `Livewire` `Docker` `Ansible`

</td>
<td width="50%" valign="top">

**Lokale KI und Agenten-Pipeline**
*privat, läuft bei mir zu Hause*

Externe API, fremde Server, fremde Rechnung? Stattdessen ein eigenes Modell und eine Loop-Pipeline, die Aufgaben als PRD abarbeitet, während ich schlafe.

`Python` `FastAPI` `aider`

</td>
</tr>
<tr>
<td valign="top">

**Auto-Wartungs-Tracker**
*privat, in Arbeit*

Den Ölwechsel vergisst man. Ein ESP32 mit GPS zählt Fahrten, erinnert rechtzeitig und synct mit meinem Dashboard. Mein erstes Gerät, das mir sagt, was ich zu tun habe.

`ESP32` `GPS` `Laravel`

</td>
<td valign="top">

**Kleiderschrank-PWA**
*privat, läuft bei mir zu Hause*

Voller Schrank, nichts anzuziehen. Jetzt ist der Schrank digital und das Problem nur noch halb so groß.

`Laravel` `Livewire` `PWA`

</td>
</tr>
</table>

Öffentlich, und ehrlich ganz am Anfang: [ErstesProjektCs2stats](https://github.com/GigaOaxl/ErstesProjektCs2stats) (PHP) und [ChessLf10a](https://github.com/GigaOaxl/ChessLf10a) (C).

## Unter der Haube

Das Dashboard ist der Teil, den man sieht. Das hier ist der Teil, der verhindert, dass ich es kaputtmache.

<details>
<summary><b>Codeguard</b>: ein Türsteher für den eigenen Code</summary>

<br>

Ein Hook, der **vor** der Ausführung nein sagt. Commit-Hooks und Pre-Push greifen erst danach, deshalb ist das die einzige Schicht, die wirklich verhindert.

- **Blockiert:** `reset --hard`, Force-Push, `migrate:fresh`, `db:wipe`, `rm -rf` außerhalb von Wegwerf-Ordnern, `docker compose down -v`, `DROP`/`TRUNCATE` am Client vorbei, Dump zurückspielen
- **Secrets:** Lesen ist erlaubt, Lesen **plus** Senke (`curl`, `scp`, `tee`) nicht. Überschreiben und `git add -f` auf Secrets ebenfalls nicht
- **Versteht das Ausführungsmodell, nicht eine Wortliste:** Zeilenfortsetzungen, `sudo`/`timeout`-Wrapper, `bash -lc`, Anführungszeichen mitten im Wort, `docker exec … rm -rf` zählt wie auf dem Host
- **Schützt sich selbst:** Hooks, Settings und das Skript sind für Schreiben und `sed -i` gesperrt. Die Lücke dazu fand ein Gegenlesen, nicht der Guard. Seitdem steht sie als Regel und Testfall da
- **Eigene Testsuite** mit Deny- *und* Negativfällen: Jeder Fehlalarm im Alltag wird zum Testfall
- **Ehrlich dokumentierte Grenzen:** Er liest Text, er sperrt keine Syscalls. Eine Bremse gegen den unabsichtlichen Griff, kein Sandkasten gegen den absichtlichen

</details>

<details>
<summary><b>Pipeline</b>: main bewegt sich nur über die Schleuse</summary>

<br>

- **Kein direkter Push auf `main`.** Der Server entscheidet per `hs merge <branch> <hash>`
- **Staging-Smoke** misst den Live-Stand mit. Rot heißt: Reset auf `last-known-good` und Deploy-Block
- **Ausgang wird ausgewertet:** `ENDE rc=0` **und** `G5 OK` müssen im Log stehen. Ein Monitor prüft genau diese zwei Zeilen, nicht „der Prozess ist weg"
- **Keine Live-Änderung bei laufender Pipeline:** `flock` und Exit 6, wenn jemand dazwischenfunkt
- **`hs status` bleibt rot**, solange der letzte Lauf eines Namens fehlgeschlagen ist
- **Aus einem Fehler eine Regel gemacht:** Ein roter Lauf, der unbemerkt weiterlief, ist heute eine Prozessregel mit Monitor

</details>

<details>
<summary><b>Gesundheitssysteme</b>: der Server meldet sich zuerst</summary>

<br>

- **Geräte-Healthcheck** alle 5 Minuten: der TV per ADB mit bis zu drei automatischen Reconnects, der Lüfter per miio, der Rest per Ping. Statuswechsel gehen als Alert nach Discord
- **Geräte-Discovery:** Netzwerk-Scan alle 30 Minuten, neue Geräte bestätige ich per Reaktion
- **Cache-Monitor:** misst alle 10 Sekunden, behebt hängende Consumer selbst und meldet nur `critical`
- **Backup-Watchdog:** prüft von außen, ob das Backup noch läuft (veraltet, fehlgeschlagen, Restore-Test zu alt), wiederholt nicht, meldet die Rückkehr zur Normalität
- **Docker-Health und Restart-Alerts** über einen read-only Socket-Proxy, nie mit `docker.sock` im Bot
- **SSH-Login-Alerts** über einen PAM-Hook, komplett außerhalb der App
- **LLM-Tagesbudget** pro Anbieter mit Warnung und hartem Limit

</details>

<details>
<summary><b>Fortress-Sweep und Agenten</b>: Sicherheit, die von selbst läuft</summary>

<br>

- **20 Security-Kategorien**, täglich als Voll-Sweep und nach großen Änderungen sofort
- **Koordinator** mit Eskalationsstufen: offene Funde werden erinnert, bis sie weg sind
- **Watchdog** bricht hängende Sweeps ab. Die Review-Agenten laufen report-only und schreiben nie in den Code
- **Maßstab „Stabil":** Ursache in einem Satz, Test auf die Ursache, Fix an der Ursache. Übergangslösungen nur mit Ticket und Ablaufdatum

</details>

## Werkzeug

**Software** PHP 8.3 · Laravel · Livewire · Python · FastAPI · Alpine.js · Bootstrap · Sass · Vite
**Betrieb** Linux · Docker Compose · nginx · Ansible · WireGuard · MQTT
**Hardware** ESP32 · PC-Umbauten, auch mit nur drei funktionierenden RAM-Slots

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/GigaOaxl/GigaOaxl/output/github-snake-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/GigaOaxl/GigaOaxl/output/github-snake.svg">
  <img alt="Contribution-Snake, frisst meine Commits." src="https://raw.githubusercontent.com/GigaOaxl/GigaOaxl/output/github-snake.svg">
</picture>

<sub>Kontakt: folgt. Erreichbar bin ich vorerst über GitHub.</sub>

</div>
