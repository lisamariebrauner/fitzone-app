# SPEC.md · FitZone Berlin

Quelle: Meeting 1 mit der Inhaberin Lisa Sommer (12 Nachrichten). Zu jeder Regel steht die Nachricht, aus der sie stammt.

## Entitäten

| Entität | Attribute |
|---|---|
| Mitglied | mitglied_nr, tarif → Tarif, name, email, telefon, geburtsdatum, mitglied_seit, status (aktiv, pausiert, gesperrt, probe) |
| Tarif | tarif_id, name (Basic, Plus, Premium), max_buchungen_monat, online_zugang, storno_gebuehrenfrei |
| Trainer | trainer_id, name, anstellung (fest, Honorar) |
| Kurs | kurs_id, kursart (Yoga, Pilates, Spinning, HIIT, Bodyworkout, Functional), format (Studio, Online-Live), raum, max_teilnehmer |
| Kurstermin | termin_id, kurs → Kurs, trainer → Trainer, datum, uhrzeit, dauer |
| Buchung | buchung_id, mitglied → Mitglied, termin → Kurstermin, gebucht_am, status (gebucht, warteliste, storniert, no_show, teilgenommen), wartelisten_position, stornogebuehr |
| Pause | pause_id, mitglied → Mitglied, beginn, ende, grund (Krankheit, Schwangerschaft, Urlaub) |
| Video | video_id, titel, kursart |

## Beziehungen

- Tarif 1:n Mitglied
- Kurs 1:n Kurstermin
- Trainer 1:n Kurstermin (jeder Termin hat genau einen Trainer)
- Trainer n:m Kurs über Qualifikation
- Mitglied n:m Kurstermin über Buchung
- Mitglied 1:n Pause

## Business Rules

- **BR1** Basic höchstens 5 Kursbuchungen pro Monat, Plus und Premium unbegrenzt (Nachricht 6, 7)
- **BR2** Stornierung bis 2 Stunden vor Kursbeginn kostenlos, danach 50 % des Kurswerts; Premium zahlt nie; Krankheit als manuelle Ausnahme durch Lisa (Nachricht 9)
- **BR3** 3 No-Shows hintereinander ohne Absage → 2 Wochen Sperre für Live-Buchungen, keine Gebühr (Nachricht 9, 12)
- **BR4** Warteliste nach Reihenfolge, höchstens 5 Personen; bei Verspätung wird der Platz nach 10 Minuten freigegeben (Nachricht 7, 9)
- **BR5** Pause höchstens 3 Monate pro Jahr, währenddessen keine Buchungen (Nachricht 11, 12)

## Widersprüche

- **W1** „Online ist für alle Mitglieder“ (Nachr. 5) vs. „Basic ist wirklich nur Studio“ (Nachr. 6) → aufgelöst über `online_zugang`: Basic ohne Online, Plus und Premium mit Online
- **W2** Marie trägt Buchungen ein (Nachr. 3) vs. „Die organisatorischen Entscheidungen … das bin ich. Ich bin hier der Boss“ (Nachr. 10) → aufgelöst über `anstellung` und Rechte: Lisa entscheidet alles Organisatorische, Trainer leiten nur ihre Kurse

## Prioritäten

1. Kursbuchung für Studio und Online an einem Ort, mit Warteliste (v1)
2. Login mit automatischer Prüfung von Mitgliedschaft und Tarif (v1)
3. Stornierung, No-Show-Sperre und Pausen automatisch verwalten (v1)
4. Benachrichtigung aller Gebuchten bei Kursänderungen und Vertretungen (v1)
5. Verbindung zu Zahlungen (später)

## Offene Fragen

- Wer darf auf die On-Demand-Videos zugreifen, hängt das vom Tarif ab? (Nachr. 12)
- Wie genau ist der Online-Zugang für Basic geregelt, „nicht so leicht“ oder gar nicht? (Nachr. 6)
- Konkrete maximale Teilnehmerzahlen je Kurs, bisher nur Bereiche (Spinning 15–18, Yoga und Pilates 12–15) (Nachr. 7)
- Was beinhaltet der Premium-Coaching-Slot genau? (Nachr. 7)
- Was ist der „Kurswert“ für die Stornogebühr? (Nachr. 9)
- Probetraining für Nichtmitglieder nur einmal: Wie wird das in der App abgebildet? (Nachr. 5, 6)
- Was plant Lisa als Nächstes? (Meeting 2)
