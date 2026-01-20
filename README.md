# Home Assistant Blueprint: PV Nulleinspeisung mit Batterie-Steuerung

Dieser Blueprint ermöglicht eine intelligente **Nulleinspeisung** für Balkonkraftwerke oder kleine PV-Anlagen. Er ist speziell für die Kombination aus einem **Marstek B2500** Batteriespeicher und einem **Deye 800W** (oder baugleichen) Wechselrichter optimiert.

Ziel der Automatisierung ist es, die Ausgangsleistung des Wechselrichters dynamisch an den aktuellen Hausverbrauch anzupassen, um so wenig Strom wie möglich ins Netz zu verschenken und gleichzeitig die Batterie optimal zu nutzen.

## 🚀 Funktionen

* **Echte Nulleinspeisung:** Passt die WR-Leistung basierend auf einem saldierenden Stromzähler (Hausverbrauch) an.
* **LowBat-Schutz:** Sinkt der Batteriestand unter einen definierten Schwellenwert, schaltet das System in den Lademodus (`charge`) und stellt den WR auf Maximum, um den Eigenbedarf direkt zu decken.
* **Clamp-Funktion:** Einstellbare Minimal- und Maximalwerte für die Ausgangsleistung (Min/Max Clamp).
* **Cooldown-Logik:** Getrennte Verzögerungszeiten für die Erhöhung und Reduzierung der Leistung, um "Flattern" der Steuerung und unnötige Lastwechsel zu vermeiden.
* **Modus-Steuerung:** Automatisches Umschalten zwischen `auto` und `charge` Modus der Batterie.

## 🛠 Voraussetzungen

Bevor du diesen Blueprint nutzt, müssen folgende Entitäten in Home Assistant verfügbar sein:

1.  **Hausverbrauch Sensor:** Ein Template-Sensor, der den echten Hausverbrauch berechnet (Watt).
2.  **Batterie Ladezustand:** Sensor für den SOC der Batterie (%).
3.  **Leistungsvorgabe:** Eine `number` Entität deines Wechselrichters (z.B. über Solarman, MQTT oder ESPHome).
4.  **Wechselrichter Schalter:** Ein `switch`, um den Wechselrichter zu aktivieren.
5.  **Lade/Entlade Modus:** Eine `select` Entität zur Steuerung des Batteriemodus (z.B. Marstek Integration).

## ⚙️ Konfiguration (Inputs)

| Input | Beschreibung | Default |
| :--- | :--- | :--- |
| **Hausverbrauch** | Template-Sensor, der den echten Hausverbrauch berechnet. | - |
| **Batterie Ladezustand** | Sensor für den aktuellen SOC in Prozent. | - |
| **Wechselrichter Leistung** | Die `number` Entität für die Watt-Vorgabe. | - |
| **Mindestleistung WR** | Untere Grenze der Einspeisung (z.B. Grundlast). | 30 W |
| **Maximalleistung WR** | Obere Grenze der Einspeisung. | 800 W |
| **LowBat-Schwelle** | SOC-Grenze für den Prioritäts-Lademodus. | 11 % |
| **Cooldown Erhöhung** | Wartezeit vor Leistungserhöhung (träge Reaktion). | 180 s |
| **Cooldown Reduzierung** | Wartezeit vor Leistungsreduzierung (schnelle Reaktion). | 15 s |

## 🧠 Funktionsweise

1.  **Trigger:** Die Automatisierung startet bei jeder Änderung des Hausverbrauchs oder des Batteriestands.
2.  **LowBat-Modus:**
    * Fällt der SOC unter die Schwelle, wird der Batteriemodus auf `charge` gesetzt und der WR auf das eingestellte Maximum gefahren.
3.  **Normalbetrieb:**
    * Der Batteriemodus wird auf `auto` gestellt.
    * Der Zielwert wird innerhalb der `min_output` und `max_output` Grenzen berechnet.
4.  **Zeitverzögerung (Cooldown):**
    * Durch den Modus `restart` wird bei jeder Änderung ein neuer Timer gestartet.
    * Die Leistung wird erst angepasst, wenn der Verbrauchswert stabil über die Dauer des jeweiligen Cooldowns anliegt. Dies schont die Hardware bei kurzen Lastspitzen (z.B. Wasserkocher).

## 📥 Installation

1. Kopiere die YAML-Datei deines Blueprints in deinen Home Assistant Ordner:
   `custom_blueprints/pv_nulleinspeisung.yaml`
2. Gehe zu **Einstellungen** > **Automatisierungen & Szenen** > **Blueprints**.
3. Klicke auf **Blueprint erstellen** und wähle diese Vorlage aus.

---
**Haftungsausschluss:** Die Nutzung erfolgt auf eigene Gefahr. Achte auf die zulässigen Schreibzyklen deiner Hardware (Wechselrichter/Batterie-Steuerung).
