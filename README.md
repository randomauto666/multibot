# Multibot – Funktionen

Multibot ist ein Discord-Bot mit vielen Funktionen. Jede Funktion ist ein eigenes Modul, das du mit Slash-Befehlen einrichtest. Diese Seite beschreibt, was jedes Modul macht und wie du es benutzt.

---

## Sprache

Der Bot antwortet auf **Deutsch** oder **Englisch**.

- `/language set sprache:de|en|auto`: setzt die Sprache des Servers. `auto` übernimmt die Discord-Einstellung des Servers oder des Nutzers.
- `/language show`: zeigt die aktuelle Sprache.

Übersetzt sind die Fehlermeldungen und die Sprachbefehle. Die übrigen Module sind bisher auf Deutsch. Die Befehlsbeschreibungen bleiben deutsch.

---

## Module

Jedes Modul ist eine eigene Funktion. Der Name in Klammern ist das Stichwort, nach dem du suchst. Befehle mit Sternchen (*) sind Pflichtangaben. Neue Module sind auf einem Server standardmäßig aktiv.

### Allgemein

#### language (Sprache)
- **Befehle:** `/language set sprache`, `/language show`
- **Einrichtung:** Keine. Standard ist die Discord-Sprache des Servers.
- **Funktion:** Mit `/language set` wählst du Deutsch oder Englisch. `auto` übernimmt die Discord-Sprache des Servers. Übersetzt sind die Fehlermeldungen und die Sprachbefehle. Die übrigen Texte bleiben vorerst deutsch.

#### ticket (DSGVO-Link)
- **Befehle:** `/ticket`
- **Funktion:** Zeigt nur einen Link zur DSGVO-Seite. Ein Ticketsystem ist **nicht** enthalten.

### Moderation und Sicherheit

#### moderation (Moderation)
- **Befehle:**
  - `/mod warn user* grund`: verwarnt, speichert die Verwarnung und schickt dem Mitglied eine DM
  - `/mod warnings user*`: zeigt bis zu 15 letzte Verwarnungen
  - `/mod kick user* grund`
  - `/mod ban user* grund`
  - `/mod timeout user* minuten* grund` (1 bis 40320 Minuten)
  - `/mod clear anzahl*` (1 bis 100 Nachrichten im aktuellen Kanal, ältere als 14 Tage gehen nicht)
- **Berechtigung:** Discord-Recht „Mitglieder moderieren“
- **Log:** Jede Aktion wird im Log-Kanal (Kategorie „moderation“) festgehalten, wenn Logging eingerichtet ist.

#### automod (Auto-Moderation)
- **Einrichtung:** `/automod an`. Standard ist **aus**.
- **Befehle:**
  - `/automod an` und `/automod aus`
  - `/automod status`: alle Regeln mit Grenzen
  - `/automod regel name* aktiv grenze`: Regel ein/aus oder Grenze ändern
  - `/automod ausnahme kanal*`: Kanal von der Prüfung ausnehmen, erneut ausführen hebt die Ausnahme auf
- **Regeln (Standardgrenzen):**
  - `spam`: mehr als 6 Nachrichten in 5 Sekunden, Timeout 5 Minuten
  - `mentions`: mehr als 5 Erwähnungen je Nachricht
  - `caps`: mehr als 70 % Großbuchstaben, ab 12 Zeichen
  - `invites`: Discord-Einladungslinks (Grenze 1)
  - `emojis`: mehr als 12 Emojis je Nachricht
  - `duplicates`: dieselbe Nachricht 3-mal innerhalb von 30 Sekunden
- **Ausnahmen:** Mitglieder mit „Nachrichten verwalten“ werden nicht geprüft.
- **Ablauf:** Der Verstoß wird gelöscht, der Nutzer bekommt eine DM, und der Vorfall geht ins Log.

#### security (Raid-Schutz)
- **Befehle:**
  - `/raidschutz status`
  - `/raidschutz raid aktiv*`: Erkennung an/aus
  - `/raidschutz grenze beitritte* sekunden*`: Standard 10 Beitritte in 10 Sekunden
  - `/raidschutz mindestalter tage*`: junge Konten beim Beitritt entfernen, `0` = aus
  - `/raidschutz lockdown aktiv*`: Server manuell sperren oder öffnen
- **Funktion:**
  - Wird die Grenze überschritten, schaltet der Bot automatisch den **Lockdown** ein. Dabei wird die Verifizierungsstufe auf die höchste gesetzt, und neue Konten unter 30 Tagen werden entfernt.
  - Die vorherige Verifizierungsstufe wird gemerkt und beim Beenden wiederhergestellt.
- **Berechtigung:** „Server verwalten“ für den Bot.

#### antinuke (Anti-Nuke)
- **Befehle:**
  - `/antinuke status`
  - `/antinuke toggle`
  - `/antinuke threshold wert*` (2 bis 20, Standard 3)
- **Funktion:** Erkennt, wenn ein einzelner Account in 10 Sekunden mehrere Kanäle, Rollen oder Banns löscht. Dann entfernt der Bot dem Verursacher alle Rollen. Der Vorfall geht ins Log.
- **Ausgenommen:** Der Server-Owner und der Bot selbst.
- **Grenze:** Massen-**Kicks** werden noch nicht erkannt.

#### trapchannel (Trap-Channel)
- **Befehle:** `/trapchannel add kanal*`, `/trapchannel remove kanal*`, `/trapchannel list`
- **Funktion:** Jede Nachricht eines Nutzers in einem Trap-Kanal wird gelöscht, und der Nutzer wird gebannt. Der Bot selbst darf dort schreiben, z. B. für automatische Infos.
- **Achtung:** Das ist eine harte Maßnahme. Vorher auf einem Testserver prüfen.

#### linkschutz (Link-Schutz)
- **Einrichtung:** `/linkschutz enable`, `/linkschutz log kanal*`
- **Befehle:**
  - `/linkschutz enable` und `/linkschutz disable`
  - `/linkschutz log kanal*`
  - `/linkschutz ignore-channel kanal*` und `/linkschutz ignore-role rolle*` (umschalten)
  - `/linkschutz allow domain*` und `/linkschutz disallow domain*`
  - `/linkschutz timeout minuten*` (Dauer beim zweiten Verstoß, Standard 60)
  - `/linkschutz resetwarn user*`
  - `/linkschutz status`
- **Funktion:** Links werden blockiert, außer zu erlaubten Domains. Tenor und Klipy sind immer erlaubt. Beim ersten Verstoß gibt es eine Warnung per DM, beim zweiten einen Timeout.

#### imageschutz (Bild-Blockliste)
- **Befehle:**
  - `/bildschutz add bild* label` (Referenzbild hochladen)
  - `/bildschutz remove id*`, `/bildschutz list`
  - `/bildschutz toggle`
  - `/bildschutz threshold wert*` (0 bis 20, Standard 8)
- **Funktion:** Erkennt bekannte Bilder per Perceptual Hash, also auch wenn sie neu komprimiert oder leicht verändert sind, und löscht sie. Gut gegen Scam-Banner nach einer Kontokompromittierung.
- **Berechtigung:** Administrator

#### pingschutz (Ping-Schutz)
- **Befehle:** `/pingschutz hinzufügen [rolle|user]`, `/pingschutz entfernen [rolle|user]`, `/pingschutz liste`
- **Funktion:** Erwähnungen geschützter Rollen oder Nutzer werden gelöscht. Der Absender bekommt eine DM. Admins sind ausgenommen.

#### invites (Invite-Tracking)
- **Einrichtung:** `/invites logchannel kanal*`
- **Befehle:**
  - `/invites top`: Rangliste der Einlader
  - `/invites user [user]`
  - `/invites set user* anzahl*`, `/invites reset user*`
  - `/invites toggle`
  - `/invites exclude add|remove|list rolle`: Rollen vom Zählen ausschließen
- **Funktion:** Erkennt, über welchen Einladungslink ein Mitglied gekommen ist, und zählt die Einladungen. Der Log-Kanal bekommt eine Begrüßung mit dem Einlader.
- **Berechtigung:** Einladungen verwalten und Audit-Log anzeigen.

### Begrüßung, Rollen und Logging

#### welcome (Willkommen)
- **Einrichtung:** `/welcome channel kanal* text vorlage`
- **Befehle:**
  - `/welcome channel kanal* text vorlage`: Kanal setzen. Der Text darf `{user}`, `{server}` und `{count}` enthalten. `\n` ist ein Zeilenumbruch. `vorlage` nutzt eine Embed-Vorlage aus `/embed`.
  - `/welcome toggle`
  - `/welcome test`: zeigt eine Vorschau
- **Funktion:** Begrüßt neue Mitglieder im Kanal. Zusätzlich landet der Beitritt im Log.

#### autorole (Autorolle)
- **Befehle:** `/autorole add rolle*`, `/autorole remove rolle*`, `/autorole list`, `/autorole bots aktiv*`
- **Funktion:** Vergibt neuen Mitgliedern beim Beitritt automatisch die Rollen. Bots bekommen Rollen nur, wenn `/autorole bots` aktiv ist.

#### massrole (Massenrollen)
- **Befehle:** `/massrole add rolle* nur-mit`, `/massrole remove rolle* nur-mit`
- **Funktion:** Gibt allen Mitgliedern eine Rolle oder entzieht sie. Optional nur denen, die eine bestimmte andere Rolle haben. Die Verarbeitung läuft im Hintergrund, die Antwort kommt am Ende.

#### reactionroles (Reaktions-Rollen)
- **Befehle:**
  - `/reaktionsrollen hinzufügen nachricht-id* emoji* rolle*` (Nachricht im aktuellen Kanal)
  - `/reaktionsrollen entfernen nachricht-id* emoji*`
- **Funktion:** Wer auf das Emoji reagiert, bekommt die Rolle, wer die Reaktion entfernt, verliert sie.

#### selfroles (Self-Roles)
Zwei Varianten:
- **Embed mit Dropdown:** Mitglieder wählen ihre Rollen selbst aus einem Menü.
- **Text mit Reaktionen:** Normale Nachricht, Rollen über Emoji-Reaktionen.

- **Befehle:**
  - `/selfroles erstellen typ* kanal* titel beschreibung farbe bild`: Panel anlegen, `typ` ist `embed` oder `text`
  - `/selfroles design id* titel beschreibung farbe bild`: Embed-Design ändern (nur Embed-Panels)
  - `/selfroles text id* text*`: Text eines Text-Panels ändern
  - `/selfroles rolle id* rolle* name emoji`: Rolle hinzufügen. Bei Text-Panels ist ein Emoji Pflicht.
  - `/selfroles entfernen id* rolle*`
  - `/selfroles kanal id* kanal*`: Panel in einen anderen Kanal verschieben
  - `/selfroles liste`
  - `/selfroles löschen id*`
- **Berechtigung:** Rollen verwalten (Discord-Recht). Der Bot braucht zusätzlich „Rollen verwalten“ und muss über den Self-Rollen stehen.

#### colorme (Farbrollen)
- **Befehle:** `/farbe setze hex*` (z. B. `#ff8800`), `/farbe entfernen`
- **Funktion:** Jedes Mitglied bekommt eine eigene Rolle in der gewählten Farbe. Der Bot braucht „Rollen verwalten“.

#### staff (Staff-Protokoll)
- **Einrichtung:**
  1. `/staff kanal kanal*`
  2. `/staff rolle-hinzufügen rolle*` für jede Staff-Rolle
- **Befehle:** `/staff kanal`, `/staff rolle-hinzufügen`, `/staff rolle-entfernen`, `/staff liste`
- **Funktion:** Bekommt ein Mitglied eine eingetragene Staff-Rolle oder ändert sich seine höchste Staff-Rolle, schreibt der Bot in den Kanal:
  - **Neues Teammitglied:** wenn das Mitglied zum ersten Mal eine Staff-Rolle bekommt
  - **Neue Beförderung:** wenn die höchste Staff-Rolle steigt
  - **Downrank:** wenn sie sinkt
  - **Teammitglied entfernt:** wenn alle Staff-Rollen wegfallen
- **Rang:** Die Discord-Rollenposition entscheidet über die Rangfolge. Die Staff-Rollen müssen also in der richtigen Reihenfolge in der Rollenliste stehen.
- **Ausführender:** Wird aus dem Audit-Log gelesen, sonst „unbekannt“.

#### teamlist (Teamlisten)
- **Einrichtung:** Pro Liste ein Name, ein Kanal und Rollen.
- **Befehle:**
  - `/teamliste erstellen name* kanal*`: bis zu 10 Listen pro Server
  - `/teamliste kanal name* kanal*`
  - `/teamliste rolle name* rolle* titel`: bis zu 10 Abschnitte pro Liste, Titel optional
  - `/teamliste entfernen name* rolle*`
  - `/teamliste senden name`: ohne Namen werden alle Listen gesendet bzw. aktualisiert
  - `/teamliste löschen name*`, `/teamliste liste`
- **Funktion:** Das Embed zeigt je Rolle die Mitglieder mit Anzahl. Es aktualisiert sich automatisch bei Rollenänderungen, beim Verlassen eines Mitglieds und bei Umbenennung einer Rolle. Gleichzeitige Änderungen werden zusammengefasst, und alle 15 Minuten gibt es einen Abgleich. Eine Nachricht wird nur bearbeitet, wenn sich der Inhalt wirklich ändert.

#### logging (Logging)
- **Einrichtung:** `/logs kanal kanal*` (Standard-Kanal)
- **Befehle:**
  - `/logs kanal kanal*`
  - `/logs ereignis ereignis* kanal` (eigener Kanal für ein Ereignis, ohne Kanal = Standard)
  - `/logs an ereignis*` und `/logs aus ereignis*`
  - `/logs alle aktiv*`
  - `/logs liste`
- **Ereignisse** (einzeln schaltbar, jedes mit Details):
  - Nachrichten: gesendet (standardmäßig aus), bearbeitet, gelöscht, Massen-Löschung
  - Reaktionen: hinzugefügt, entfernt, alle entfernt
  - Mitglieder: beigetreten, verlassen, geändert (Nick, Rollen, Timeout, Server-Avatar), Profil-Änderungen, Bann, Entbann
  - Einladungen: erstellt, gelöscht
  - Kanäle, Rollen, Threads: erstellt, geändert, gelöscht
  - Voice: betreten, verlassen, gewechselt, Status (Mute, Deaf, Stream, Kamera)
  - Emojis, Server-Einstellungen, geplante Events, Webhooks
- **Details:** Autor, Kanal, Link, Inhalt (lange Texte werden auf mehrere Felder verteilt), Anhänge, Vorher/Nachher, Zeitstempel, und wer die Aktion ausgelöst hat, soweit das Audit-Log es hergibt.
- **Nicht erfasst:** Slash-Befehle, Stage-Events, Sticker-Änderungen.

#### rss (RSS-Ankündigungen)
- **Befehle:** `/rss add url* kanal*`, `/rss remove id*`, `/rss list` (Admin)
- **Funktion:** Prüft Feeds alle 10 Minuten (RSS und Atom, nur https, bis zu 10 pro Server). Neue Einträge landen als Embed im Kanal. Beim ersten Lauf werden nur die Einträge gemerkt, nicht gepostet.

#### youtube (YouTube-Benachrichtigungen)
- **Befehle:**
  - `/youtube add channel-id* kanal* shorts-ignorieren premieren-ignorieren`
  - `/youtube remove id*`
  - `/youtube list`
- **Einrichtung:** Die Kanal-ID beginnt mit `UC`. Du findest sie in den Kanal-Einstellungen auf YouTube.
- **Funktion:** Prüft alle 5 Minuten. Neue Videos werden angekündigt. Shorts und Premieren lassen sich ausblenden. Beim ersten Lauf werden alte Videos nicht angekündigt.

#### twitch (Twitch)
- **Voraussetzung:** Der Betreiber des Bots hinterlegt die Twitch-Zugangsdaten.
- **Befehle:**
  - `/twitch setup name* kanal* text`: automatische Live-Ankündigung
  - `/twitch settings`
  - `/twitch announce-channel kanal*`
  - `/twitch liveannounce kanalname* uhrzeit* nachricht`: manuelle Vorankündigung mit `@everyone`, Uhrzeit in MEZ/MESZ, Platzhalter `{kanal}`, `{zeit}`, `{everyone}`
- **Funktion:** Prüft jede Minute, ob der Kanal live ist, und postet einmal pro Stream.

#### giveaway (Giveaways)
- **Befehle:**
  - `/giveaway start preis* dauer* gewinner` (Dauer z. B. `10m`, `2h`, `3d`; Gewinner 1 bis 20)
  - `/giveaway end id*`: beendet sofort
  - `/giveaway reroll id*`: neue Gewinner
  - `/giveaway list`
- **Funktion:** Das Giveaway hat einen „Teilnehmen“-Button. Nach Ablauf wird automatisch ausgelost und der Gewinner genannt.
- **Berechtigung:** Server verwalten.

#### birthday (Geburtstage)
- **Einrichtung:** `/geburtstag kanal kanal*` für die Glückwünsche, optional `/geburtstag rolle rolle`.
- **Befehle:**
  - `/geburtstag set tag* monat* jahr`: jeder trägt seinen eigenen Geburtstag ein
  - `/geburtstag remove`, `/geburtstag list`
  - `/geburtstag kanal kanal*` (Admin)
  - `/geburtstag rolle rolle` (Admin, ohne Rolle = entfernen)
- **Funktion:** Am Geburtstag gratuliert der Bot im Kanal. Mit Geburtstagsrolle bekommt man sie 24 Stunden lang. Die Prüfung läuft alle 10 Minuten.

### Community und Unterhaltung

#### levels (Levels)
- **Befehle:**
  - `/level rang [user]`: Level, XP und fehlende XP bis zum nächsten Level
  - `/level top`: Rangliste
  - `/level belohnung level* rolle` (Admin, ohne Rolle = entfernen)
- **Funktion:** Jede Nachricht gibt 15 bis 25 XP, höchstens einmal pro Minute. Die Schwelle steigt pro Level. Bei Level-Aufstieg gibt es eine Nachricht und ggf. eine Belohnungsrolle.

#### economy (Geld)
- **Befehle:**
  - `/geld kontostand [user]`
  - `/geld täglich`: 200 Münzen, einmal pro 24 Stunden
  - `/geld überweisen user* betrag*`
  - `/geld top`
- **Funktion:** Virtuelle Währung ohne Wert außerhalb des Servers.

#### tags (Tags)
- **Befehle:**
  - `/tag use name*`: zeigt den Tag
  - `/tag list`
  - `/tag create name* text*`: braucht „Nachrichten verwalten“
  - `/tag delete name*`
- **Funktion:** Gespeicherte Textbausteine. Der Text darf `\n` enthalten.

#### partners (Partnerliste)
- **Befehle:**
  - `/partner liste`: Embed mit allen Partnern
  - `/partner add name* invite* beschreibung` (Admin)
  - `/partner remove name*` (Admin)

#### messages (Zitate)
- **Befehle:**
  - `/zitat zufall`
  - `/zitat nummer id*`
  - `/zitat hinzufügen text* autor*`
  - `/zitat entfernen id*` (braucht „Nachrichten verwalten“)

#### infos (Infos)
- **Befehle:** `/info server`, `/info user [user]`, `/info rolle rolle*`, `/info kanal [kanal]`, `/info bot`

#### tempvoice (Temp-Channels)
- **Einrichtung:** `/tempvoice setup kanal*` (ein Voice-Kanal als „Erstellen“-Kanal)
- **Befehle:** `/tempvoice name name*` (nur für den Besitzer des Temp-Kanals)
- **Funktion:** Wer den Erstellen-Kanal betritt, bekommt einen eigenen Voice-Kanal. Der wird gelöscht, sobald er leer ist.

#### autodelete (Auto-Delete)
- **Befehle:** `/autodelete set kanal* minuten*` (1 bis 10080), `/autodelete remove kanal*`, `/autodelete list`
- **Funktion:** Alle Nachrichten in dem Kanal werden nach der festgelegten Zeit gelöscht.

#### automessage (Auto-Nachrichten)
- **Befehle:** `/automessage add kanal* minuten* text*` (mindestens 5 Minuten), `/automessage remove id*`, `/automessage list`
- **Funktion:** Wiederkehrende Nachrichten, z. B. Regeln oder Erinnerungen. `\n` ist ein Zeilenumbruch.

#### reminders (Erinnerungen)
- **Befehle:** `/erinnerung setze in* text*` (z. B. `10m`, `2h`, `3d`, `1w`, maximal ein Jahr), `/erinnerung liste`, `/erinnerung löschen id*`
- **Funktion:** Erinnert im Kanal, in dem der Befehl gegeben wurde, und pingt den Nutzer.

#### afk (AFK-System)
- **Befehle:** `/afk [grund]`
- **Funktion:** Wer erwähnt wird, während er AFK ist, sieht den Grund und die Dauer. Wer wieder schreibt, ist automatisch zurück.

#### pingvoice (Voice-Ping)
- **Befehle:** `/voiceping setze voice* text* rolle abklingzeit`, `/voiceping entfernen voice*`
- **Funktion:** Wenn jemand den Voice-Kanal betritt, wird im Text-Kanal die Rolle gepingt (ohne Rolle: `@here`). Die Abklingzeit verhindert Pingflut.

#### mcstatus (Minecraft-Status)
- **Befehle:** `/mc status host*`, `/mc auto host* kanal*` (Admin)
- **Funktion:** Zeigt Status, Spieler und Version. Die automatische Anzeige aktualisiert sich alle 5 Minuten im eingestellten Kanal.

#### quiz (Quiz)
- **Befehle:** `/quiz start`, `/quiz add frage* antwort*`, `/quiz remove id*`, `/quiz list`
- **Funktion:** Eine Frage pro Kanal, 60 Sekunden Zeit. Ohne eigene Fragen gibt es Standardfragen. Die Antwort wird ohne Groß-/Kleinschreibung und Akzente geprüft.

#### counting (Zählspiel)
- **Befehle:** `/zählen kanal kanal*`, `/zählen stand`, `/zählen entfernen kanal*`
- **Funktion:** In dem Kanal wird hochgezählt, beginnend bei 1. Wer zweimal hintereinander zählt oder falsch zählt, bricht die Serie ab. Der Rekord wird gespeichert.

#### zahl (Zahl raten)
- **Befehle:** `/zahl start max`, `/zahl stoppen`
- **Funktion:** Der Bot denkt sich eine Zahl aus (Standard 1 bis 100). Alle können raten, der Bot sagt „höher“ oder „niedriger“. Zeit: 5 Minuten.

#### wordgames (Wortspiele)
- **Befehle:** `/wort kette-start [start]`, `/wort story-start`, `/wort stoppen` (braucht „Nachrichten verwalten“)
- **Funktion:**
  - **Wortkette:** Jedes Wort beginnt mit dem letzten Buchstaben des vorherigen. Keine Wiederholungen, und niemand schreibt zweimal hintereinander.
  - **One Word Story:** Jeder schreibt ein Wort, niemand zweimal hintereinander. Am Ende wird die Geschichte gezeigt.
- **Ende:** Nach 24 Stunden ohne Eingabe.

#### games (Minispiele)
- **Befehle:**
  - `/spiele tictactoe`: gegen den Bot
  - `/spiele flagge`: Welches Land ist das?
  - `/spiele emoji`: Emoji-Rätsel
  - `/spiele stoppen`
  - `/spiele duell gegner*`: Duell mit Annahme, der Sieger wird per Zufall ermittelt
  - `/spiele config toggle|channel|status`: Spiele an/aus, auf einen Kanal begrenzen (Admin)
- **Rätsel-Zeit:** 90 Sekunden.

#### chess (Schach)
- **Befehle:**
  - `/schach herausfordern gegner*`: Angebot mit Annehmen/Ablehnen-Buttons, 10 Minuten Zeit
  - `/schach zug zug*`: Zug in SAN-Notation, z. B. `e4`, `Nf3`, `O-O`
  - `/schach brett`
  - `/schach aufgeben`
- **Funktion:** Die Farben werden zufällig verteilt. Partien laufen bis zu 7 Tage ohne Zug und überleben Neustarts.

#### connectfour (Vier gewinnt)
- **Befehle:** `/vier gegner*`
- **Funktion:** Vier gewinnt mit Buttons für die Spalten. Die Partie läuft 30 Minuten ohne Zug und überlebt Neustarts.

### Server-Werkzeuge

#### utility (Werkzeuge)
- **Befehle:**
  - `/say text* kanal`: der Bot schreibt eine Nachricht (`\n` = Zeilenumbruch, keine Erwähnungen)
  - `/moveall ziel*`: verschiebt alle Mitglieder aus deinem Voice-Kanal in den Ziel-Kanal

#### admintools (Admin-Werkzeuge)
- **Befehle:**
  - `/admin nick user* name`: Nickname setzen, ohne Name = zurücksetzen
  - `/admin rolle user* rolle* aktion*`: Rolle vergeben (`add`) oder entziehen (`remove`)
  - `/admin sperren kanal`: Schreibrecht für alle entziehen
  - `/admin entsperren kanal`

#### autopublisher (Auto-Publisher)
- **Einrichtung:** Keine. Wirkt in allen Ankündigungs-Kanälen.
- **Funktion:** Jede Nachricht in einem Ankündigungs-Kanal wird automatisch an die folgenden Server veröffentlicht.

#### autothread (Auto-Thread)
- **Befehle:** `/thread add kanal*`, `/thread remove kanal*`, `/thread list`
- **Funktion:** Für jede Nachricht in den eingestellten Kanälen entsteht ein Thread.

#### antighostping (Anti-Ghostping)
- **Einrichtung:** Keine.
- **Funktion:** Wird eine Nachricht mit Erwähnung gelöscht, postet der Bot die Erwähnung noch einmal im Kanal. Der Cache liegt im Speicher und wird beim Neustart geleert.

#### embeds (Embeds)
- **Befehle:**
  - `/embed erstellen name*`
  - `/embed bearbeiten name* titel beschreibung farbe footer autor bild thumbnail url`: `-` leert ein Feld
  - `/embed feld-hinzufügen name* feldname* wert* nebeneinander`
  - `/embed feld-entfernen name* nummer*`
  - `/embed vorschau name*`
  - `/embed senden name* kanal text`
  - `/embed liste`, `/embed löschen name*`
- **Platzhalter:** `{user}`, `{server}`, `{kanal}`. Welcome kennt zusätzlich `{count}`.
- **Verwendung:** Vorlagen lassen sich mit `/embed senden` verschicken oder bei `/welcome channel vorlage` nutzen.

---

---

## Datenschutz

- Der Bot speichert Einstellungen, Verwarnungen, Levels, Geld, Geburtstage und Protokolle.
- Die Logging-Funktion schreibt Nachrichteninhalte in den Log-Kanal, wenn du das einschaltest. Informiere deine Mitglieder darüber.
