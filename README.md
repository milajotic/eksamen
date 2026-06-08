# Nailicious – Booking System for Nail Salon
 
### Deltakere
- Mila
---
 
## 1. Prosjektidé og problemstilling
 
### Hva er prosjektet?
Nailicious er en nettside der kunder kan bestille negletime direkte hos salongen.
Nettsiden samler timebestilling på ett sted og gjør prosessen enkel for både kunde og negletekniker.
 
### Hvilket problem løser det?
Mange kunder bestiller i dag time via Instagram DM, noe som kan føre til misforståelser, lang ventetid og tapte henvendelser.
Et eget bookingsystem løser dette ved å gjøre kommunikasjonen mer strukturert.
 
### Hvorfor er løsningen nyttig?
Løsningen gjør timebestilling raskere og mer oversiktlig.
Kundene kan bestille når det passer dem, og negleteknikeren får bedre kontroll over sin egen timeplan.
 
### Målgruppe
Målgruppen er personer som jevnlig tar manikyr eller andre neglbehandlinger.
 
### Plan for dagen
Admin-side (vis alle bestillinger,slett og rediger)
Brukerside(mine bestillinger + avbestill booking)
Oppdater Kanban-tavlen, rydd opp i koden, oppdater README
https://github.com/users/milajotic/projects/3
 
---
 
## 2. Systembeskrivelse
 
### Formål
Gi kunder en enkel måte å bestille negletime på, og gi negleteknikeren en adminside for å administrere bestillinger.
 
### Brukerflyt
1. Bruker besøker hovedsiden
2. Bruker registrerer seg eller logger inn
3. Bruker velger tjeneste, dato og tid
4. Bestillingen lagres i databasen
5. Admin logger inn og ser alle bestillinger på adminsiden
6. Admin kan redigere eller slette bestillinger
### Teknologier brukt
- Python / Flask
- MariaDB
- HTML / CSS
- Waitress (produksjonsserver)
- python-dotenv
---
 
## 3. Server-, infrastruktur- og nettverksoppsett
 
### Servermiljø
Debian (lokal maskin / Raspberry Pi)
 
### Nettverksoppsett
- IP-adresse: 172.21.46.141
- Port: 5000 (Flask) / 8080 (Waitress)
- Brannmurregler: Port 8080 er åpen for innkommende trafikk
```
Klient → Waitress (port 8080) → Flask → MariaDB
```
 
### Tjenestekonfigurasjon
- Miljøvariabler lagres i `.env` fil
- `.env` inneholder DB_HOST, DB_USER, DB_PASSWORD, DB_NAME og SECRET_KEY
---
 
## 4. Prosjektstyring – GitHub Projects (Kanban)
 
- To Do / In Progress / Done
- Link: https://github.com/users/milajotic/projects/3
### Refleksjon
Kanban-boardet hjalp meg å holde oversikt over hva som måtte gjøres og hva som var ferdig.
Det var lett å se hvilke oppgaver som var prioritert.
 
---
 
## 5. Databasebeskrivelse
 
Databasenavn: nailsalon
 
### Tabeller
 
| Tabell | Felt | Datatype | Beskrivelse |
|--------|------|----------|-------------|
| users | id | INT | Primærnøkkel |
| users | username | VARCHAR(50) | Brukernavn |
| users | email | VARCHAR(100) | E-postadresse |
| users | password | VARCHAR(255) | Passord |
| users | role | VARCHAR(20) | Brukerrolle (admin/user) |
| service | id | INT | Primærnøkkel |
| service | name | VARCHAR(100) | Navn på tjeneste |
| service | description | VARCHAR(255) | Beskrivelse |
| service | price | DECIMAL(6,2) | Pris |
| appointment | id | INT | Primærnøkkel |
| appointment | user_id | INT | Fremednøkkel til users |
| appointment | service_id | INT | Fremednøkkel til service |
| appointment | date | DATE | Dato for time |
| appointment | time | TIME | Tidspunkt for time |
| faq_questions | id | INT | Primærnøkkel |
| faq_questions | user_id | INT | Fremednøkkel til users |
| faq_questions | sporsmal | TEXT | Brukerens spørsmål |
| faq_questions | opprettet | DATETIME | Tidspunkt spørsmål ble sendt |
 
### SQL-eksempel
 
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    password VARCHAR(255) NOT NULL,
    role VARCHAR(20) NOT NULL DEFAULT 'user'
);
 
CREATE TABLE service (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description VARCHAR(255),
    price DECIMAL(6,2) NOT NULL
);
 
CREATE TABLE appointment (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    service_id INT NOT NULL,
    date DATE NOT NULL,
    time TIME NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (service_id) REFERENCES service(id)
);
 
CREATE TABLE faq_questions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    sporsmal TEXT NOT NULL,
    opprettet DATETIME DEFAULT NOW(),
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```
 
---
 
## 6. Programstruktur
 
```
nailsalon/
 ├── app.py
 ├── templates/
 │   ├── index.html
 │   ├── login.html
 │   ├── registrer.html
 │   ├── book.html
 │   ├── services.html
 │   ├── admin.html
 │   ├── edit_booking.html
 │   ├── user.html
 │   ├── faq.html
 │   └── send_sporsmal.html
 ├── static/
 │   ├── style.css
 │   └── images/
 └── .env
```
 
### Databasestrøm
 
```
HTML → Flask → MariaDB → Flask → HTML
```
 
---
 
## 7. Kodeforklaring
 
| Rute | Metode | Beskrivelse |
|------|--------|-------------|
| / | GET | Hovedside |
| /services | GET | Viser alle tjenester fra databasen |
| /book | GET, POST | Bestill time (krever innlogging) |
| /registrer | GET, POST | Registrer ny bruker |
| /login | GET, POST | Logg inn |
| /logout | GET | Logg ut, tømmer session |
| /admin | GET | Adminside med alle bestillinger (krever admin) |
| /admin/edit/<id> | GET | Rediger en bestilling |
| /admin/update | POST | Lagrer endringer på bestilling |
| /admin/delete/<id> | GET | Sletter en bestilling |
| /user | GET | Viser brukerens egne bestillinger |
| /cancel/<id> | GET | Kansellerer en bestilling |
| /faq | GET | FAQ-side med vanlige spørsmål |
| /faq/sporsmal | GET, POST | Send inn et spørsmål |
| /delete_account | GET | Sletter brukerkonto og all data |
 
---
 
## 8. Sikkerhet og pålitelighet
 
- **`.env`** – Sensitiv informasjon som passord og database-info lagres i en `.env` fil og lastes inn med `python-dotenv`. Dette betyr at passordet ikke ligger synlig i koden.
- **Parameteriserte spørringer** – Vi bruker `%s` i alle SQL-spørringer for å unngå SQL-injection angrep.
- **Session** – Brukerens innloggingsstatus lagres i en kryptert session med en secret key.
- **Rollebasert tilgang** – Admin-siden sjekker `session.get("role") != "admin"` før den viser noe innhold.
- **GDPR** – `ON DELETE CASCADE` på `faq_questions` sørger for at brukerdata slettes automatisk når en bruker sletter kontoen sin.
---
 
## 9. Feilsøking og testing
 
### Typiske feil
### Testmetoder
---
 
## 10. Konklusjon og refleksjon
 
### Hva lærte jeg?
 
### Hva fungerte bra?
 
### Hva ville jeg gjort annerledes?
 
### Hva var utfordrende?
 
---
 
## 11. Kildeliste
- https://flask.palletsprojects.com
- https://www.w3schools.com
- https://mariadb.com/kb/en/documentation/
- https://docs.python.org/3/
