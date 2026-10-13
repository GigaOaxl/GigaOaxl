# ARBEITSSTAND: GitHub-Profil GigaOaxl

## Ziel
Kleine animierte Bühne mit Alex in der Mitte; Projekte und Eigenheiten sind die Beweisstücke.
Leicht, trocken, selbstironisch. Kein Lebenslauf. Kein JS (nur SVG mit CSS/SMIL als `<img>`).

## Entscheidungen
- Arbeitsort lokal `~/PhpstormProjects/GigaOaxl`; GitHub-Repo `GigaOaxl/GigaOaxl` legt Alex an.
- Palette: Dunkelblau-Schwarz-Grau-Töne, Cyan `#38bdf8` als Lichtfarbe. Akzent wird in Gruppe 1 an Varianten probiert.
- Mond als Lichtquelle im Banner: **ja**.
- Schreib-/Substack-Abschnitt: **nein**.
- Figur/Avatar: **zunächst keine**; Alex liefert ein neues Profilbild nach, Einbau danach.
- Keine Fremddienste, alle Grafiken unter `assets/`, `prefers-reduced-motion` respektieren, Hell/Dunkel per `<picture>`.
- Nein-Liste (Arbeitgeber, Ort, Namen, Gesundheit, Finanzen/FIRE-Zahlen, Auto-Details, private Repo-Links, fremde Marken/Logos) gilt für alles inkl. Alt-Texte und Commits.
- Ein Commit pro Gruppe, nach jeder Gruppe STOPP bis Freigabe.

## Erledigt
- [x] Gruppe 0: Fundament
- [x] Gruppe 1–5 am Stück (Alex-Wunsch): Hero mit Mond/PCB/Tippzeilen (`assets/hero-*.svg`), Orbit (`assets/orbit-*.svg`), Projektkarten, Werkzeug, Snake-Workflow (`.github/workflows/snake.yml`), README
- [x] Browser-Check (Chromium headless, Dark): Hero + Orbit gerendert, Tippzeile wipet sichtbar

- [x] Überarbeitung: Löten raus, Alter 21, Abschnitt „Unter der Haube" (Codeguard, Pipeline, Gesundheitssysteme, Fortress/Agenten), Orbit: Mini-PC statt Lötkolben

## Offen
- [ ] Alex-Abnahme der Optik
- [ ] Push auf `GigaOaxl/GigaOaxl` main (Remote hat 1 Initial-Commit, Merge nötig), danach Action einmal manuell starten (Branch `output` für die Snake)
- [ ] Profilbild einbauen / Figur, Kontaktlinks

## Nächste Schritte
Alex schaut sich alles an und gibt frei oder nennt Änderungen.

## Offene Eingaben von Alex
Kontaktlinks, neues Profilbild, Freigabe der Akzentfarbe nach Variantenvergleich.
