# Farbe vom Hauptlicht an Zusatzlichter

Home-Assistant-Blueprint. Zusatzlichter übernehmen die Farbe eines Hauptlichts und gehen aus, sobald das Hauptlicht aus ist oder auf Weiß steht.

Die Beschriftungen in der Automation sind Deutsch. Eine englische Oberfläche gibt es nicht.

[![Öffnet Home Assistant und zeigt den Blueprint-Import.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ftobivankenobi97%2Fha-farbe-zusatzlichter%2Fblob%2Fmain%2Ffarbe-zusatzlichter.yaml)

Home Assistant 2024.10 oder neuer. Das Hauptlicht braucht einen Farbmodus (`xy`, `hs` oder `rgb`) und einen Weißmodus über die Farbtemperatur.

## Verhalten

| Hauptlicht | Zusatzlichter |
| --- | --- |
| Aus | gehen aus |
| Weiß, also Farbtemperatur von warm bis kalt | gehen aus |
| Farbmodus | übernehmen die Farbe |
| nicht verfügbar | bleiben unverändert |

Weiß ist der ganze Farbtemperatur-Regler, auch kühles Weiß. Nur ein Farbton im Farbrad zählt als Farbe.

Ein Zusatzlicht, das jemand einschaltet, während das Hauptlicht aus oder weiß ist, geht wieder aus. Kommt ein Zusatzlicht aus „nicht verfügbar“ zurück, zieht es den aktuellen Zustand nach. Nach einem Neustart von Home Assistant wartet die Automation 15 Sekunden, damit die Lichter da sind.

Farbänderungen warten 0,3 Sekunden. Beim Ziehen im Farbrad geht nur die letzte Farbe raus. Nicht erreichbare Zusatzlichter werden übersprungen.

## Felder in der Automation

Alle Texte erscheinen so in der Oberfläche.

| Feld | Bedeutung |
| --- | --- |
| Hauptlicht | ein Licht, dessen Farbe gilt |
| Zusatzlichter | ein oder mehrere Lichter, später erweiterbar |
| Helligkeit der Zusatzlichter | siehe unten, Standard ist „Eigene Helligkeit behalten“ |
| Feste Helligkeit | Schieberegler 1–100 %, nur bei „Feste Helligkeit“ |

Helligkeit:

- **Eigene Helligkeit behalten.** Es wird nur die Farbe gesetzt. Die Helligkeit am Zusatzlicht bleibt.
- **Vom Hauptlicht übernehmen.** Helligkeit und Farbe kommen vom Hauptlicht.
- **Feste Helligkeit.** Jeder Farbwechsel setzt den Reglerwert.

Beim ersten Einschalten eines Zusatzlichts ohne gemerkte Helligkeit entscheidet die Integration selbst, wie hell es wird. Danach bleibt der Wert stehen, solange „Eigene Helligkeit behalten“ aktiv ist.

## Einrichten

1. Oben auf den Import-Button tippen und den Blueprint in Home Assistant speichern.
2. Einstellungen → Automationen & Szenen → Automation erstellen → Blueprint **Farbe vom Hauptlicht an Zusatzlichter**.
3. Hauptlicht und Zusatzlichter wählen.
4. Speichern.

Weitere Lampen später in derselben Automation unter **Zusatzlichter** ergänzen.

Manuell liegt die Datei unter `config/blueprints/automation/custom/farbe-zusatzlichter.yaml`. Danach die Automationen neu laden.

## Beispiel Zentrale Spots und PC Corsair

Hauptlicht `light.zentrale_spots` (Zigbee-Gruppe, Farbtemperatur und Farbe). Zusatzlicht `light.pc_corsair_pc_corsair` (MQTT, nur RGB). Helligkeit bleibt am PC, die Spots geben nur die Farbe vor.

Die fertige Automation heißt **Zentrale Spots Farbe an Zusatzlichter**. Dieselbe Fassung steht in [`examples/zentrale-spots.yaml`](examples/zentrale-spots.yaml).

```yaml
alias: Zentrale Spots Farbe an Zusatzlichter
description: Zusatzlichter aus bei Aus oder Weiß, im Farbmodus Farbe übernehmen.
use_blueprint:
  path: custom/farbe-zusatzlichter.yaml
  input:
    main_light: light.zentrale_spots
    slave_lights:
      - light.pc_corsair_pc_corsair
    brightness_mode: keep
    fixed_brightness: 100
```

Wer den Blueprint über den Button importiert, bekommt einen anderen Pfad als `custom/farbe-zusatzlichter.yaml`. In der Oberfläche ist der Name derselbe. In YAML muss `path` zum importierten Blueprint passen.

## Ablauf im Trace

In der Automation unter **Spuren**:

- **Nach dem Start kurz warten** läuft nur nach einem Home-Assistant-Start.
- **Farbe übernehmen** setzt `rgb_color` auf jedem erreichbaren Zusatzlicht.
- **Zusatzlichter aus** schaltet nur Lichter aus, die gerade an sind.

## Lizenz

MIT. Corsair, iCUE und Home Assistant sind Marken der jeweiligen Inhaber. Dieses Blueprint steuert nur Home-Assistant-Lichter.
