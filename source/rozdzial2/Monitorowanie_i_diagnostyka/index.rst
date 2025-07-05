Monitorowanie i diagnostyka
============================

:Autorzy:
    - Dominika Półchłopek
    - Kacper Rasztar
    - Grzegorz Szczepanek

.. contents:: Spis treści
   :depth: 3
   :local:

Wstęp
-----

Monitorowanie i diagnostyka baz danych PostgreSQL stanowią fundamentalne elementy zapewniające wydajność, bezpieczeństwo oraz stabilność środowiska produkcyjnego. Nowoczesne rozwiązania monitorowania umożliwiają administratorom proaktywne wykrywanie problemów, optymalizację wydajności oraz zapewnienie zgodności z przepisami bezpieczeństwa. Efektywne monitorowanie PostgreSQL obejmuje szeroki zakres metryk - od aktywności sesji użytkowników, przez analizę operacji na danych, po szczegółowe śledzenie logów systemowych i zasobów na poziomie systemu operacyjnego.

Monitorowanie sesji i użytkowników
----------------------------------

### Analiza aktywności użytkowników

Systematyczne obserwowanie działań wykonywanych przez użytkowników bazy danych stanowi podstawę skutecznego monitorowania PostgreSQL. Kluczowym narzędziem w tym obszarze jest widok systemowy ``pg_stat_activity``, który umożliwia śledzenie bieżących zapytań, czasu ich trwania oraz identyfikowanie użytkowników i aplikacji korzystających z bazy.

Przykład zapytania:

::

   SELECT * FROM pg_stat_activity;

### Zarządzanie zasobami i limity

Parametry takie jak ``max_connections`` czy ``work_mem`` kontrolują liczbę połączeń i użycie pamięci. Przykład zapytania na trafność cache:

::

   SELECT sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) as ratio FROM pg_statio_user_tables;

### Wykrywanie problemów z blokadami

::

   SELECT relation::regclass, * FROM pg_locks WHERE NOT granted;

Dalsza analiza wymaga łączenia ``pg_locks`` i ``pg_stat_activity``.

Monitorowanie dostępu do tabel i operacji na danych
---------------------------------------------------

### Analiza użycia danych

Widok ``pg_stat_user_tables`` pozwala zrozumieć wykorzystanie tabel.

### Wykrywanie nieprawidłowych zapytań

Rozszerzenie ``pg_stat_statements`` śledzi zapytania o długim czasie wykonania.

### Bezpieczeństwo i zgodność

Rozszerzenie ``pgaudit`` pozwala śledzić dostęp do tabel i zgodność z normami.

Monitorowanie logów i raportowanie błędów
-----------------------------------------

### Analiza logów systemowych

Logi PostgreSQL to źródło informacji o stanie serwera. Narzędzia: pgBadger, ELK Stack, Splunk.

### Automatyczne raportowanie i alerty

Zabbix, Prometheus, Grafana – automatyczne alerty dla metryk jak: opóźnienia replikacji, użycie CPU, liczba połączeń.

### Konfiguracja logowania dla pgBadger

Wymagane opcje w ``postgresql.conf``: ``log_checkpoints``, ``log_connections``, ``log_temp_files``, itp.

Monitorowanie na poziomie systemu operacyjnego
----------------------------------------------

### Narzędzia systemowe

Linux: ``top``, ``htop``, ``vmstat``.
Windows: Menedżer zadań, Performance Monitor.

### Integracja z narzędziami zewnętrznymi

Prometheus, OpenTelemetry, Grafana, Nagios.

Narzędzia monitorowania PostgreSQL
----------------------------------

### Open source

pgAdmin, pgBadger, PGWatch.

### Rozwiązania komercyjne

DataDog APM, Sematext Monitoring, pganalyze.

### Zabbix dla PostgreSQL

Szablony i użytkownik monitorujący z rolą ``pg_monitor``.

Najlepsze praktyki monitorowania
--------------------------------

### Ustanawianie baseline'ów wydajności

Tworzenie wzorców wydajności na podstawie obserwacji metryk.

### Korelacja metryk międzysystemowych

Łączenie danych z różnych podsystemów (system, aplikacja, baza danych).

### Konfiguracja efektywnych alertów

Ostrzeżenia na poziomie 70–80%, priorytetyzacja alertów.

Monitorowanie wysokiej dostępności
----------------------------------

### Monitorowanie statusu replikacji

::

   SELECT application_name, pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) AS lag_bytes FROM pg_stat_replication;

### Weryfikacja spójności

Monitoring każdego węzła, sumy kontrolne, failover, routing zapytań.

Wniosek
-------

Skuteczne monitorowanie PostgreSQL wymaga zintegrowanego podejścia obejmującego wiele poziomów systemu. Kluczowe są: baseline'y, alerty, wizualizacja metryk, testy spójności i audyt danych.

Bibliografia
------------

1. https://betterstack.com/community/comparisons/postgresql-monitoring-tools/
2. https://uptrace.dev/tools/postgresql-monitoring-tools
3. https://pganalyze.com/blog/postgres-lock-monitoring
4. https://www.pgaudit.org
5. https://github.com/darold/pgbadger
6. https://prometheus.io
7. https://grafana.com
8. https://www.zabbix.com/integrations/postgresql