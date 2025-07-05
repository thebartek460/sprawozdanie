Modele Bazy Danych
=========================================

:author: [Maciej Gołębiowski]

Wprowadzenie
--------------

W tym rozdziale przedstawiono modele bazy danych systemu rejestracji wizyt lekarskich, oparte na SQLite oraz PostgreSQL. Baza została zaprojektowana zgodnie z zasadami trzeciej postaci normalnej (3NF).

Model Konceptualny
---------------------

Baza danych składa się z 3 głównych tabel: **lekarze**, **pacjenci**, **wizyty**.

Relacje
~~~~~~~~

- **lekarze** z **wizyty** mają relację jeden do wielu (jeden lekarz może mieć wiele wizyt).
- **pacjenci** z **wizyty** mają relację jeden do wielu (jeden pacjent może mieć wiele wizyt).
- **wizyty** łączą lekarzy z pacjentami w określonym terminie.

Model Logiczny
---------------------

Tabela **lekarze** składa się z kolumn:

- ``id`` — format tekst (unikalny identyfikator lekarza)
- ``imie`` — format tekst
- ``nazwisko`` — format tekst
- ``specjalizacja`` — format tekst

Tabela **pacjenci** składa się z kolumn:

- ``id`` — format tekst (unikalny identyfikator pacjenta)
- ``imie`` — format tekst
- ``nazwisko`` — format tekst
- ``pesel`` — format tekst (11 cyfr)
- ``numer_telefonu`` — format tekst (9 cyfr)
- ``email`` — format tekst

Tabela **wizyty** składa się z kolumn:

- ``id_wizyty`` — format tekst (unikalny identyfikator wizyty)
- ``id_lekarza`` — format tekst (klucz obcy do lekarze)
- ``id_pacjenta`` — format tekst (klucz obcy do pacjenci)
- ``godzina_wizyty`` — format data i godzina
- ``status`` — format tekst (kod statusu wizyty)

Relacje
~~~~~~~~

- **lekarze** z **wizyty**: relacja jeden do wielu przez ``id_lekarza``
- **pacjenci** z **wizyty**: relacja jeden do wielu przez ``id_pacjenta``

Model Fizyczny
---------------------

Implementacja **sqlite**
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Tabela **lekarze**:

- ``id`` — format TEXT PRIMARY KEY
- ``imie`` — format TEXT
- ``nazwisko`` — format TEXT
- ``specjalizacja`` — format TEXT

Tabela **pacjenci**:

- ``id`` — format TEXT PRIMARY KEY
- ``imie`` — format TEXT
- ``nazwisko`` — format TEXT
- ``pesel`` — format TEXT
- ``numer_telefonu`` — format TEXT
- ``email`` — format TEXT

Tabela **wizyty**:

- ``id_wizyty`` — format TEXT PRIMARY KEY
- ``id_lekarza`` — format TEXT (FOREIGN KEY)
- ``id_pacjenta`` — format TEXT (FOREIGN KEY)
- ``godzina_wizyty`` — format DATETIME
- ``status`` — format TEXT

Implementacja **postgresql**
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Tabela **lekarze**:

- ``id`` — format VARCHAR(7) PRIMARY KEY
- ``imie`` — format VARCHAR(16)
- ``nazwisko`` — format VARCHAR(32)
- ``specjalizacja`` — format VARCHAR(32)

Tabela **pacjenci**:

- ``id`` — format VARCHAR(7) PRIMARY KEY
- ``imie`` — format VARCHAR(16)
- ``nazwisko`` — format VARCHAR(32)
- ``pesel`` — format CHAR(11)
- ``numer_telefonu`` — format CHAR(9)
- ``email`` — format VARCHAR(64)

Tabela **wizyty**:

- ``id_wizyty`` — format VARCHAR(7) PRIMARY KEY
- ``id_lekarza`` — format VARCHAR(7) (FOREIGN KEY)
- ``id_pacjenta`` — format VARCHAR(7) (FOREIGN KEY)
- ``godzina_wizyty`` — format TIMESTAMP
- ``status`` — format VARCHAR(2)

Statusy wizyt
---------------------

- ``PD`` — Potwierdzona
- ``CN`` — Anulowana
- ``ZR`` — Zrealizowana
- ``OC`` — Oczekująca

Opis statusów
~~~~~~~~~~~~~

- **PD (Potwierdzona):** Wizyta jest aktywna i oczekuje na realizację.
- **CN (Anulowana):** Wizyta została anulowana i nie będzie zrealizowana.
- **ZR (Zrealizowana):** Wizyta odbyła się i została zakończona.
- **OC (Oczekująca):** Wizyta jest wstępnie zarejestrowana, czeka na potwierdzenie.

