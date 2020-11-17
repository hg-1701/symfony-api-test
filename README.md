## Test Symfony Backend
### verfügbare Zeit 2-3h

#### Voraussetzungen
- Docker-Desktop muss installiert sein => https://www.docker.com/get-started
- Composer sollte installiert sein => https://getcomposer.org/download/

#### WINDOWS 10
- Bitte [WSL2 installieren](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
- [Debian](https://www.microsoft.com/de-de/p/debian/9msvkqc78pk6?rtc=1&activetab=pivot:overviewtab) aus dem MS-Store laden und installieren
- mit PS (Powershell) oder WinT ([Windows Terminal](https://www.microsoft.com/de-de/p/windows-terminal/9n0dx20hk701?rtc=1&activetab=pivot:overviewtab)) ins Debian-Image wechseln (einfach 'Debian' eingeben)
- Den Code im WSL-Image von Debian clonen

Wenn das erfolgt ist, gelten die folgenden Schritte auch für Windows im WSL-Kontext

#### Vorbereitung
- dieses Repo auschecken
- in das Repo wechseln
- folgendes ausführen

```shell script
composer install
docker-compose up --build
```
#### PGAdmin
Bei PGAdmin muss das Verzeichnis wie folgt geändert werden:
```shell script
sudo chown -R 5050:5050 .pgadmin
```

Symfony sollte nun via http://localhost:8080 erreichbar sein

#### Aufgabenstellung
Es soll eine Backend-API erstellt werden, mit der die Kundendaten eines Vermittlers ausgelesen, aktualisiert sowie neu erstellt und gelöscht werden können. Die Ausgabe soll im JSON(+ld) Format erfolgen.

Die API soll **vorrangig** mit Hilfe von [API-Platform](https://api-platform.com/docs/core/) erstellt und mit [OpenApi](https://www.openapis.org/) dokumentiert werden.    

Alle Bundles müssen selbst konfiguriert und eingestellt werden.
Bsp. services.yaml, security.yaml, lexik_jwt_authentication.yaml

Jeder Vermittler aus der Tabelle std.vermittler darf n Kunden haben. Ausgegeben werden sollen nur Kunden, die dem Vermittler anhand der ID zugeordnet und nicht gelöscht sind. Die Vermittlerdaten selbst sollen nicht mit ausgegeben werden.  
Jeder Kunde kann n Adressen haben, die wiederum eine 1:1-Beziehung zu std.kunde_adresse haben, wo festgelegt wird, ob die Adresse geschäftlich genutzt wird und als Rechnungsadresse genutzt werden darf. Zusätzlich besitzt jeder Kunde einen Onlinezugang, der in sec.users steht.

_**Zusatzaufgabe**_:  
_Zur Sicherheit soll die API mit einem JWT abgesichert werden. Dafür soll ein JWT nach einem Login via POST erzeugt werden. Nur Vermittler mit einem aktiven Zugang (sec.vermittler_user) dürfen sich einloggen und einen JWT erhalten._  
_Für den JWT kommt das Bundle lexik/jwt-authentication zur Anwendung._

Daten-Anforderungen:  
- Folgende Felder sind erforderlich:
  - Kunde: vorname, nachname, geburtsdatum
  - Adresse: strasse, plz, ort, bundesland
  - User: username (email), password
- Das Passwort eines Kunden darf nicht mit ausgegeben werden
- Das Passwort darf beim Erstellen eines Users nicht leer sein
- Das Passwort eines Kunden soll 8 Zeichen lang sein und Groß/Kleinbuchstaben sowie mind. eine Zahl und ein Sonderzeichen enthalten
- Die E-Mail-Adresse des Kunden muss valide sein
- Wird ein Datensatz aus einer Tabelle auf "gelöscht" gesetzt, darf dieser bei einer Abfrage nicht erscheinen

Folgende Ressourcen werden erwartet:
- foo/kunden
  - GET Collection (alle Kunden des eingeloggten Vermittlers), POST neuer Kunde für den VP
- foo/kunden/{id}
  - GET/PUT/DELETE
- foo/adressen
  - GET Collection (alle Adressen aller Kunden für den eingeloggten VP), POST neue Adresse für einen Kunden
- foo/adressen/{id}
  - GET/PUT/DELETE
- foo/user
  - GET Collection, POST neuer User für einen Kunden
- foo/user/{id}
  - GET/PUT/DELETE

##### Sub-Ressourcen:
- foo/kunden/{id}/adressen
  - GET Collection Adressen eines Kunden
- foo/kunden/{id}/user
  - GET Collection user eines Kunden
- foo/kunden/{id}/adressen/{id}/details
  - GET Collection Details zu einer Adresse eines Kunden

#### Bestehende Vermittler
- Marcus Findel (Login: mfindel@vp-felder.de, Passwort: hommes)
- Christian Hauser (Login: chauser@vp-felder.de, Passwort: hauser)
- Christian Karasius (Login: c_karasius@fondshaus.ag, Passwort: supersicher)
- Fabian Winkel

#### Erwartetes JSON Format:

````json
{
    "id": "CF451PQ9",
    "name": "Mustermann",
    "vorname": "Max",
    "geburtsdatum": "1970-01-01",
    "geschlecht": "divers",
    "email": "max.mustermann@example.org",
    "vermittlerId": 1000,
    "adressen": [
        {
            "adresseId": 1,
            "strasse": "Musterstrasse 12",
            "plz": "01234",
            "ort": "Musterort",
            "bundesland": "BE",
            "details": {
                "geschaeftlich": false,
                "rechnungsadresse": true
            }
        }
    ],
    "user": {
        "username": "max.mustermann@example.org",
        "aktiv": 1,
        "lastLogin": "2020-05-01T20:20:20+02:00"
    }
}
````
