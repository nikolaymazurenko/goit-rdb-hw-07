# goit-rdb-hw-07
**Виконавець:** Мазуренко Микола

Домашнє завдання 7: часові ряди та JSONB у задачах ML.

## Data source
NYC TLC Yellow Taxi Trip Records:
https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page

Notebook намагається завантажити `yellow_tripdata_2024-03.parquet`. Якщо official Parquet недоступний, автоматично використовується deterministic stub fallback 60 days x 5 zones = 300 daily rows. Поточний режим друкується як `DATA_MODE`.

## Time-series
Основна таблиця: `t7_hw_taxi_daily`.
Feature view: `v_t7_hw_taxi_features`.
Features: lag 1/7/30, rolling 7-day average fare, rolling 30-day fare volatility, cumulative trips, expanding average. Усі windows використовують `PARTITION BY pickup_zone_id`.

## JSONB
`t7_hw_events` містить 50 000 synthetic events і 5 event types з різними payload schemas. Використовуються `@>`, `?`, `?|`, `jsonb_path_query`, `jsonb_path_exists`.

## Indexes
GIN `jsonb_path_ops` використовується для containment workload. Expression B-tree на `(payload ->> 'device')` використовується для path-specific equality. Notebook містить EXPLAIN до і після індексів.

## JSONB vs normalized
Створюється `t7_hw_checkouts_norm`, після чого один aggregate порівнюється через `EXPLAIN (ANALYZE, BUFFERS)` у JSONB та normalized варіантах.
