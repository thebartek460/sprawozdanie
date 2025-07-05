Analiza bazy danych i optymalizacja zapytań
===========================================

Wstęp
-----

W tej części dokumentu przedstawiono metody analizy wydajności zapytań SQL oraz sposoby optymalizacji dla baz danych SQLite i PostgreSQL. Opisano narzędzia, przykładowe zapytania testowe, zastosowane techniki indeksowania oraz porównano wydajność obu silników.

Metodologia analizy
-------------------

Do analizy wydajności zapytań wykorzystano następujące narzędzia i techniki:

- Polecenia `EXPLAIN` oraz `EXPLAIN ANALYZE` (w PostgreSQL) do analizy planów wykonania zapytań.
- Narzędzie `sqlite3` z funkcją `EXPLAIN QUERY PLAN` dla SQLite.
- Pomiar czasu wykonania zapytań (w Pythonie oraz w CLI).
- Monitorowanie użycia indeksów i skanów sekwencyjnych.

Przykładowe zapytania testowe
-----------------------------

Poniżej przedstawiono przykładowe zapytania użyte do testów wydajności oraz ich wyniki na obu silnikach baz danych.

Zapytanie 1: Lista pacjentów o określonym statusie

.. code-block:: sql

SELECT * FROM pacjenci WHERE status = 'aktywny';

- PostgreSQL: użyto indeksu na kolumnie ``status`` (czas wykonania: ~2 ms).
- SQLite: bez indeksu – pełny skan tabeli (~15 ms).

Zapytanie 2: Liczba wizyt pacjenta po numerze PESEL

.. code-block:: sql

SELECT COUNT(*) FROM wizyty
JOIN pacjenci ON wizyty.id_pacjenta = pacjenci.id
WHERE pacjenci.pesel = '99010112345';

- PostgreSQL: indeksy na ``id_pacjenta``, ``pesel`` – czas ~1 ms.
- SQLite: brak indeksu – czas ~12 ms.

Zapytanie 3: Wyszukiwanie wizyt lekarza z filtrem daty

.. code-block:: sql

SELECT * FROM wizyty
WHERE id_lekarza = 3 AND godzina_wizyty BETWEEN '2024-01-01' AND '2024-12-31';

- PostgreSQL: wykorzystano indeks złożony (``id_lekarza``, ``godzina_wizyty``) – czas ~2 ms.
- SQLite: czas ~20 ms, skan sekwencyjny.

Optymalizacja zapytań
---------------------

W celu poprawy wydajności bazy danych zastosowano następujące techniki:

- Indeksowanie kolumn najczęściej używanych w filtrach:
- ``status``, ``pesel``, ``godzina_wizyty``, ``id_lekarza``
- Indeksy złożone:
- (``id_lekarza``, ``godzina_wizyty``) – dla zapytań z filtrem daty
- (``id_pacjenta``, ``godzina_wizyty``) – dla raportów historii pacjenta
- Zmiana struktury zapytań:
- unikanie ``SELECT *`` na rzecz wyboru konkretnych kolumn
- użycie aliasów i ``EXISTS`` zamiast ``IN`` tam, gdzie było to szybsze

Analiza planów zapytań (PostgreSQL)
-----------------------------------

Przykład użycia polecenia EXPLAIN ANALYZE:

.. code-block:: sql

EXPLAIN ANALYZE
SELECT * FROM wizyty WHERE id_lekarza = 3 AND godzina_wizyty > '2025-01-01';

Wynik:

.. code-block:: text

Index Scan using idx_lekarz_data on wizyty  (cost=0.29..8.57 rows=3 width=72)
Index Cond: ((id_lekarza = 3) AND (godzina_wizyty > '2025-01-01'))
Planning Time: 0.050 ms
Execution Time: 0.090 ms

Wniosek: PostgreSQL używa indeksu zamiast pełnego skanu tabeli – zapytanie jest zoptymalizowane.

Porównanie wydajności – SQLite vs PostgreSQL
--------------------------------------------

+---------------------------------------------+--------------+------------------+
| Zapytanie                                   | SQLite (ms)  | PostgreSQL (ms)  |
+=============================================+==============+==================+
| SELECT * WHERE status = 'aktywny'           | 15           | 2                |
+---------------------------------------------+--------------+------------------+
| COUNT wizyt po peselu                       | 12           | 1                |
+---------------------------------------------+--------------+------------------+
| SELECT wizyty po dacie                      | 20           | 2                |
+---------------------------------------------+--------------+------------------+

Wniosek: PostgreSQL jest znacząco wydajniejszy przy dużych zbiorach danych i złożonych indeksach. SQLite sprawdza się jako lekka baza testowa.

Wnioski końcowe
---------------

- Wydajność bazy można znacząco poprawić dzięki dobrze zaprojektowanym indeksom.
- Plan zapytania (query plan) dostarcza kluczowych informacji o zachowaniu silnika bazy danych.
- Unikanie pełnych skanów tabeli ma krytyczne znaczenie dla skalowalności.
- Nawet przy niewielkiej liczbie danych testowych różnice między silnikami są zauważalne.
