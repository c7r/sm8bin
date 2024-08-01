# sm8bin Dokumentation


1. Hardware und Verbindungen

ESP32: Hauptmikrocontroller.
Ultraschallsensor: Verbunden mit ECHO_PIN (4). Dieser Sensor benötigt nur den Echo-Pin für Messungen.
Taste: Verbunden mit BUTTON_PIN (0). Wird zum Umschalten der Modi verwendet.

2. Betriebsmodi

2.1 Messmodus:
Liest einen einzelnen Wert vom Ultraschallsensor.
Speichert die Messung und einen Zeitstempel in NVS.
Wechselt nach der Messung in den Tiefschlafmodus.

2.2 Deep Sleep Modus:
Der Standardmodus nach der Ersteinrichtung.
Wacht durch Drücken der Taste auf.
Wenn die Taste innerhalb einer Sekunde zweimal gedrückt wird, wechselt sie in den Streaming-Modus.
Wacht regelmäßig für neue Messungen auf.

2.3 Streaming-Modus:
Richtet BLE zum Streamen von Messdaten ein.
Streamt Daten als CSV-Datei.
Wechselt nach 3 Minuten Inaktivität oder wenn die Taste länger als 2 Sekunden gedrückt wird, zurück in den Tiefschlafmodus.

3. Programmablauf

3.1 Ersteinrichtung:
Initialisiert NVS und prüft, ob eine Löschung erforderlich ist.
Richtet den Tasten-Pin als Eingang ein.
Wechselt sofort in den Tiefschlafmodus.

3.2 Schleifenausführung:
Messmodus:
Ruft generateMeasurements() auf, um Ultraschallsensordaten zu lesen und zu speichern.
Wechselt in den Tiefschlafmodus.
Tiefschlafmodus:
Überwacht Tastendrücke.
Wechselt in den Streamingmodus, wenn die Taste innerhalb einer Sekunde zweimal gedrückt wird.
Streamingmodus:
Richtet BLE ein.
Streamt Daten auf Clientanforderung.
Überwacht Tastendrücke, um zurück in den Tiefschlafmodus zu wechseln.

4. Übergangsbedingungen

4.1 Zum Messmodus:
Aus dem Tiefschlafmodus aufwachen und mit der Messung fortfahren.

4.2 Zum Tiefschlafmodus:
Nach Abschluss der Messung.
Wenn die Taste im Streamingmodus länger als 2 Sekunden gedrückt wird.
Wenn die BLE-Verbindung im Streamingmodus abläuft.

4.3 Zum Streamingmodus:
Taste im Tiefschlafmodus innerhalb einer Sekunde zweimal gedrückt.
