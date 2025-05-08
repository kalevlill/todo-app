Kontext: Todos App

Man hat die Möglichkeit todos zu erstellen (create), lesen (read), update und löschen (delete).

ID: ID des Todos (integer)

Title: Name der Aufgabe (VARCHAR(255))

Description: Beschreibung der Aufgabe, z.B was, wie wo gemacht werden muss (TEXT)

Status: TRUE = Aufgabe erledigt, FALSE = Aufgabe offen (BOOLEAN)

Erstellungsdatum: Erstellungsdatum der Aufgabe. (TIMESTAMP)

Enddatum: Bis wann die Aufgabe erledigt sein muss. (TIMESTAMP)

Länge: Kategorisierung nach Titellänge: z.B kurz, mittel oder lang.

CREATE TABLE todos (
  id INTEGER PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  status BOOLEAN NOT NULL DEFAULT FALSE,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  length_category VARCHAR(10)
);

Die Spalte ID ist geeignet als Primärschlüssel, weil:

* Jede Aufgabe ist eindeutig identifiziert

CREATE

Neues Todo einfügen:
INSERT INTO todos (title, description, status, created_at, length_category)
VALUES (?, ?, ?, ?, ?);

READ 

Alle Todos abrufen:
SELECT * FROM todos;

Todo anhand der id:
SELECT * FROM todos
WHERE id = ?;

Erledigte Todos abrufen (Status = True):
SELECT * FROM todos
WHERE status = TRUE;

UPDATE

Titel und Status Änderung:
UPDATE todos
SET title = ?, status = ?
WHERE id = ?;

DELETE

TOdo anhand der id löschen:
DELETE FROM todos
WHERE id = ?;

Reflexion:

* Warum ist die Speicherung von Anwendungsdaten in einer strukturierten Datenbank (mit Tabellen, Spalten, Datentypen, Schlüsseln) besser als die einfache Speicherung in einer JSON-Datei auf dem Dateisystem, wie wir sie in der vorherigen Aufgabe umgesetzt haben? Nenne mindestens drei Vorteile. 

1. In einer DB wird durch Tabellen, Datentypen und Schlüssel sichergestellt, dass die Daten konsistent und korrekt bleiben. Man kann z. B. festlegen, dass eine ID immer eindeutig ist oder ein Datum im richtigen Format gespeichert wird – das geht bei JSON-Dateien nicht zuverlässig.
2. Mit SQL lassen sich sehr schnell gezielte Informationen aus der Datenbank holen – z. B. alle erledigten Todos oder alle Aufgaben, die nach einem bestimmten Datum erstellt wurden. In einer JSON-Datei müsste man dafür alles laden und manuell durchsuchen, was bei großen Datenmengen langsam und unpraktisch ist.
3. Datenbanken sind dafür gemacht, dass viele Nutzer gleichzeitig darauf zugreifen können, ohne dass es zu Problemen kommmt. JSON-Dateien eignen sich nur für kleine, einfache Anwendungen – bei größeren Projekten wird es schnell unübersichtlich und fehleranfällig.

* Was ist der Hauptzweck eines Primärschlüssels in einer Tabelle, und wie hast du dieses Konzept in deinem Entwurf umgesetzt? 

Der Hauptzweck eines Primärschlüssels in einer Tabelle ist es, jede Zeile eindeutig zu identifizieren. Damit kann man genau sagen, welches Datenelement gemeint ist – zum Beispiel, wenn man etwas ändern oder löschen will. Ohne einen Primärschlüssel könnten zwei Einträge komplett gleich aussehen, und man wüsste nicht, welcher welcher ist.

In meinem Entwurf habe ich die Spalte id als Primärschlüssel verwendet. Diese ID ist eine eindeutige Zahl für jede Aufgabe (Todo), die automatisch vergeben wird. So ist garantiert, dass kein Todo die gleiche ID wie ein anderes hat, und man kann jederzeit gezielt auf ein bestimmtes Todo zugreifen.

* Wie würden die API-Endpunkte deiner Backend-Anwendung (GET /items, GET /items/:id, POST /items, DELETE /items/:id) theoretisch auf die von dir formulierten SQL-Abfragen abgebildet werden? Welche Art von Abfrage (SELECT, INSERT, UPDATE, DELETE) würde jeder Endpunkt typischerweise ausführen? 

Jeder dieser API-Endpunkte entspricht direkt einer bestimmten SQL-Abfrage, die im Hintergrund ausgeführt wird:

GET /items: Dieser Endpunkt holt alle Einträge aus der Datenbank. Dahinter steckt ein SELECT * FROM todos; – also eine SELECT-Abfrage, die alle Todos zurückgibt.

GET /items/:id: Hier will man nur ein bestimmtes Todo sehen, basierend auf seiner ID. Dafür wird ein SELECT * FROM todos WHERE id = ?; verwendet – auch eine SELECT-Abfrage, aber mit Filter.

POST /items: Dieser Endpunkt erstellt ein neues Todo. Im Hintergrund wird ein INSERT INTO todos (...) VALUES (...); ausgeführt – also eine INSERT-Abfrage.

DELETE /items/:id: Damit wird ein Todo gelöscht. Die entsprechende SQL-Abfrage lautet DELETE FROM todos WHERE id = ?; – also eine DELETE-Abfrage.

* Warum ist die Nutzung einer Datenbank für persistente Daten wichtig im Kontext von containerisierten Anwendungen und DevOps? 

In containerisierten Anwendungen und im DevOps-Umfeld ist eine DB für die dauerhafte Speicherung von Daten (also Persistenz) besonders wichtig, weil Container oft nur vorübergehend laufen. Sobald ein Container neu gestartet oder gelöscht wird, gehen alle darin gespeicherten Daten verloren – es sei denn, sie wurden außerhalb gespeichert, z.B in einer DB.

Eine DB sorgt dafür, dass die Daten unabhängig vom Zustand des Containers erhalten bleiben. 

Die DBs lassen sich gut in DevOps-Prozesse integrieren, z. B. durch automatisierte Tests, Backups oder Migrationen – das wäre mit einfachen Dateien viel schwieriger und fehleranfälliger.