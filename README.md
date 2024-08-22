Dokumentation für ESP32-Zustandsmaschine mit Ultraschallmessung und BLE-Streaming
1. Hardwareanforderungen:

ESP32-Entwicklungsboard: Der Kernmikrocontroller, der zum Ausführen des Codes verwendet wird, mit Wi-Fi- und BLE-Funktionen.
Ultraschallsensor (z. B. HC-SR04): Wird zur Entfernungsmessung verwendet. Verbunden mit GPIO 10.
Druckknopf: Verbunden mit GPIO 12, wird verwendet, um den ESP32 aus dem Tiefschlaf zu wecken und bestimmte Modi auszulösen.
Stromversorgung: Eine geeignete Stromquelle für das ESP32-Entwicklungsboard.
Optional: Zusätzliche Komponenten wie Widerstände oder Kondensatoren, abhängig von Ihrer spezifischen Verdrahtung für den Druckknopf und den Ultraschallsensor.

2. Betriebsmodi:

Modus 0: NVS-Reset-Modus
Beschreibung: Überprüft die Weckursache nach einem Reset oder Aufwachen aus dem Tiefschlaf. Wenn die Weckursache nicht auf den Timer oder den externen Knopf zurückzuführen ist, wird der nichtflüchtige Speicher (NVS) gelöscht.
Übergang: Wechselt nach dem NVS-Vorgang automatisch in den Modus 2 (Messmodus).

Modus 1: Tiefschlafmodus
Beschreibung: Versetzt den ESP32 für eine definierte Dauer (25 Sekunden) oder bis die Taste gedrückt wird in den Tiefschlafmodus. In diesem Modus verbraucht das Gerät sehr wenig Strom.
Übergang: Wacht aus dem Tiefschlaf auf und wechselt in den Modus 2 (Messmodus), wenn der Timer das Aufwachen auslöst, oder in den Modus 3 (BLE-Streamingmodus), wenn die Taste gedrückt wird.

Modus 2: Messmodus
Beschreibung: Führt eine einzelne Entfernungsmessung mit dem Ultraschallsensor durch und speichert den gemessenen Wert zusammen mit einem Zeitstempel in NVS unter Verwendung der Bibliothek „Einstellungen“.
Übergang: Wechselt nach der Messung automatisch in den Modus 1 (Tiefschlafmodus).

Modus 3: BLE-Streamingmodus
Beschreibung: Startet einen BLE-Server, der die gespeicherten Messdaten (Entfernung und Zeitstempel) als CSV-Datei an ein verbundenes BLE-Gerät streamt. Der Modus bleibt maximal 3 Minuten aktiv oder bis die BLE-Verbindung beendet wird.

Übergang: Wechselt nach 3 Minuten oder bei Verlust der BLE-Verbindung in den Modus 1 (Tiefschlafmodus).

3. Übergangsbedingungen zwischen den Modi:

Von Modus 0:

Zu Modus 2: Wenn der Weckgrund auf einen Timer oder eine undefinierte Quelle zurückzuführen ist.

Zu Modus 3: Wenn der Weckgrund auf den externen Taster zurückzuführen ist.

Von Modus 1:

Zu Modus 2: Automatisch nach dem Aufwachen aus dem Tiefschlaf durch den Timer.

Zu Modus 3: Automatisch nach dem Aufwachen aus dem Tiefschlaf durch den Taster.

Von Modus 2:

Zu Modus 1: Automatisch nach der Durchführung der Messung.

Von Modus 3:

Zu Modus 1: Nach 3 Minuten oder bei Verlust der BLE-Verbindung.

4. Void-Funktionen und ihr Zweck:

setup():
Initialisiert die serielle Kommunikation und richtet die GPIO-Pins ein. Es setzt auch den Anfangsmodus auf Modus 0.

enterMode0():
Behandelt den NVS-Reset-Vorgang. Überprüft die Weckursache und bestimmt, ob der NVS-Speicher zurückgesetzt oder in einen anderen Modus gewechselt werden soll.

enterMode1():
Versetzt den ESP32 für eine bestimmte Dauer (25 Sekunden) oder bis die Taste gedrückt wird in den Tiefschlafmodus. Konfiguriert die Weckquellen.

enterMode2():
Führt eine Entfernungsmessung mit dem Ultraschallsensor durch und speichert die Messung mit einem Zeitstempel in NVS. Bereitet das Gerät nach der Messung auf den Tiefschlaf (Modus 1) vor.

enterMode3():
Startet einen BLE-Server und streamt die gespeicherten Messdaten (Entfernung und Zeitstempel) an ein verbundenes BLE-Gerät. Verwaltet den Übergang zurück in den Tiefschlaf basierend auf der Dauer oder der BLE-Trennung.

setupBLE():
Initialisiert das BLE-Gerät, richtet den BLE-Server ein und beginnt mit der Werbung für Clientverbindungen. Bereitet den ESP32 für das Datenstreaming vor.

MyServerCallbacks:

Eine Callback-Klasse, die zur Verwaltung von BLE-Verbindungsereignissen verwendet wird, z. B. wenn ein Gerät eine Verbindung herstellt oder trennt. Versetzt den ESP32 in den Tiefschlaf, wenn die Verbindung verloren geht.

loop():

Die Hauptschleifenfunktion, die wiederholt ausgeführt wird. Sie prüft den aktuellen Modus und ruft die entsprechende Funktion auf, um diesen Modus zu handhaben. Sie stellt sicher, dass die Zustandsmaschine basierend auf den vordefinierten Bedingungen zwischen den Modi wechselt.

Fazit:

Dieser Code implementiert eine Zustandsmaschine auf dem ESP32, die verschiedene Betriebsmodi unterstützt: NVS-Reset, Tiefschlaf, Ultraschallmessung und BLE-Datenstreaming. Der ESP32 wechselt basierend auf Weckbedingungen und Timeouts zwischen diesen Modi, was ihn ideal für Anwendungen mit geringem Stromverbrauch macht, die regelmäßige Messungen und drahtlose Datenübertragung erfordern.
