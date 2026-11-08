# SPEC.md · FitZone Berlin

Quelle: Meeting 1 (12 Nachrichten) und Meeting 2 (8 Nachrichten) mit der Inhaberin Lisa Sommer. Nachricht 1–12 = Meeting 1, Nachricht 13–20 = Meeting 2. Zu jeder Regel steht die Nachricht, aus der sie stammt.

## Entitäten

| Entität | Attribute |
|---|---|
| Mitglied | mitglied_nr, tarif → Tarif, name, email, telefon, geburtsdatum, mitglied_seit, status (aktiv, pausiert, gesperrt, probe), probe_genutzt |
| Tarif | tarif_id, name (Basic, Plus, Premium), max_buchungen_monat, online_zugang, storno_gebuehrenfrei |
| Trainer | trainer_id, name, email, anstellung (fest, Honorar) |
| Kurs | kurs_id, kursart (Yoga, Pilates, Spinning, HIIT, Bodyworkout, Functional), wochentag, startzeit, endzeit, format (Studio, Online-Live), raum oder online_link, max_teilnehmer |
| Kurstermin | termin_id, kurs → Kurs, trainer → Trainer, datum, status (geplant, geändert, abgesagt) |
| Buchung | buchung_id, mitglied → Mitglied, termin → Kurstermin, gebucht_am, status (gebucht, warteliste, storniert, no_show, teilgenommen), wartelisten_position, eingecheckt_am, stornogebuehr |
| Video | video_id, titel, kursart, fuer_alle_freigegeben |
| Pause | pause_id, mitglied → Mitglied, beginn, ende, grund (Krankheit, Schwangerschaft, Urlaub) |

## Beziehungen

- Tarif 1:n Mitglied (Probe-Mitglied ohne Tarif)
- Kurs 1:n Kurstermin (Kurs ist die wöchentliche Vorlage)
- Trainer 1:n Kurstermin (jeder Termin hat genau einen Trainer)
- Trainer n:m Kurs über Qualifikation
- Mitglied n:m Kurstermin über Buchung
- Tarif n:m Video über Freischaltung (pro Video festgelegt)
- Mitglied 1:n Pause

## Business Rules

- **BR1** Basic höchstens 5 Kursbuchungen pro Monat, Plus und Premium unbegrenzt (Nachricht 6, 7)
- **BR2** Stornierung bis 2 Stunden vor Kursbeginn kostenlos, danach 50 % des Kurswerts; Premium zahlt nie; Krankheit als manuelle Ausnahme durch Lisa; Gebühr wird in v1 erfasst, Zahlung später (Nachricht 9, 20)
- **BR3** 3 No-Shows hintereinander ohne Absage → 2 Wochen Sperre für Live-Buchungen, keine Gebühr (Nachricht 9, 12)
- **BR4** Warteliste nach Reihenfolge, höchstens 5 Personen; bei freiem Platz wird die erste automatisch benachrichtigt und hat 30 Minuten Zeit; ohne Check-in 10 Minuten nach Beginn rückt die Warteliste nach (Nachricht 7, 9, 13, 19)
- **BR5** Pause höchstens 3 Monate pro Jahr, nur durch Lisa angelegt, währenddessen keine Buchungen, danach automatische Reaktivierung (Nachricht 11, 12, 17)

## Widersprüche

- **W1** „Online ist für alle Mitglieder“ (Nachr. 5) vs. „Basic ist wirklich nur Studio“ (Nachr. 6, 13) → aufgelöst in Nachr. 20 über `online_zugang` und `Tarif n:m Video`: Basic nur Studio, Plus Studio + Live + Videos, Premium alles. Einzige Ausnahme: Lisa schaltet selten ein Video manuell für alle frei (`fuer_alle_freigegeben`, Nachr. 20)
- **W2** „einmal oder zweimal eine Person extra reingelassen“ (Nachr. 7) vs. „Wenn der Kurs voll ist, ist er voll“ (Nachr. 16) → aufgelöst über `max_teilnehmer` als harte Grenze (Spinning 15, Yoga 12, Pilates 12, HIIT 20, Bodyworkout/Functional 18–20, Annahme 20, Online-Live 30); Überbuchung nur als Einzelfall durch Lisa

## Prioritäten

1. Kursbuchung mit automatischer Warteliste, Stornogebühr, No-Show-Sperre (v1)
2. Mitgliederverwaltung mit Login, Tarifcheck und Pausen (v1)
3. Zugriffskontrolle für Online-Live und Videos (v1)
4. Kursvorlagen, Benachrichtigung bei Änderungen, Vertretungsvorschläge, Probetraining (v1, wenn Zeit bleibt)
5. Neue Anforderung aus Meeting 2: Personal Training, Premium 1 Termin pro Monat inklusive, sonst 60–80 € pro Stunde (später, Nachricht 13, 14, 19)
6. Zahlungsabwicklung, Instagram, Geburtstagserinnerung (später)

## Offene Fragen

- Was ist der „Kurswert“ für die Stornogebühr, wenn Kurse im Tarif enthalten sind? (Nachr. 9)
- Zählen stornierte Buchungen zum Basic-Limit? (Nachr. 7)
- Gilt die Pause pro Kalenderjahr oder rollierend? (Nachr. 12)
- Gilt die No-Show-Sperre auch für Online-Live-Kurse? Annahme: ja (Nachr. 9)
- Probetraining: Nichtmitglieder dürfen genau einmal kostenlos teilnehmen, danach nur mit Mitgliedschaft (Nachr. 5, 6, 17). Im Modell über `status = probe` und `probe_genutzt` abgebildet; möglicherweise ebenfalls eine feste Business Rule
- Zuständigkeit: „Die organisatorischen Entscheidungen … das bin ich. Ich bin hier der Boss“ (Nachr. 10, ähnlich 15, 18) vs. Marie trägt Buchungen ein und führt die Liste, wenn Lisa nicht da ist (Nachr. 3, 18). Annahme: Nur Lisa hat Admin-Rechte, Trainer sehen nur eigene Termine und Teilnehmer. Offen, ob Marie als Vertretung Admin-Rechte bekommt

## Änderungen gegenüber Spec v1 (nach Meeting 2)

- **Offene Fragen aus v1 geklärt:** Video-Zugriff je Tarif und pro Video (Nachr. 13, 20) → neue Beziehung `Tarif n:m Video`; Online-Zugang für Basic (Nachr. 20) → W1 endgültig aufgelöst; feste Teilnehmerzahlen (Nachr. 16) → `max_teilnehmer`; Premium-Coaching-Slot ist dasselbe Angebot wie Personal Training (Nachr. 14); Probetraining einmalig über die App (Nachr. 17) → `probe_genutzt`
- **Neue Anforderung:** Auf die Zukunftsfrage (Nachr. 13) nennt Lisa Personal Training, laut Nachr. 19 erst v2
- **Entitäten geschärft:** Kurs ist jetzt die wöchentliche Vorlage mit Wochentag, Start-/Endzeit und Raum oder Online-Link (Nachr. 15, 17, 20); Kurstermin bekommt einen Status für Änderungen und Absagen (Nachr. 15, 18); Buchung bekommt `eingecheckt_am` für das Check-in (Nachr. 19)
- **Business Rules erweitert:** BR2 Gebühr wird in v1 erfasst (Nachr. 20), BR4 automatisches Nachrücken mit 30 Minuten Frist und Check-in (Nachr. 13, 19), BR5 Pause nur durch Lisa, automatische Reaktivierung (Nachr. 17)
- **W2 neu gewählt:** In v1 war W2 die Zuständigkeit von Marie. Nach Nachr. 15 und 18 ist das kein echter Widerspruch mehr (Lisa entscheidet, Marie springt nur improvisiert ein) und steht deshalb unter Offene Fragen. Der klarere Widerspruch ist die Überbuchung (Nachr. 7 vs. 16)
- **Prioritäten neu geordnet** nach Lisas drei Must-haves (Nachr. 13, 19)
- **Weiter offen:** Kurswert für die Stornogebühr, Basic-Limit bei Stornierungen, Pausenjahr, Admin-Rechte für Marie


