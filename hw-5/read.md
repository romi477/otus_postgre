### *PROJECT ID: postgres2021-220685*
### *VM: instance-1 (34.91.90.48)*
#### *Homework 5*
---
/etc/postgresql/13/main/postgresql.conf

- max_connections = 40
- shared_buffers = 1GB
- effective_cache_size = 3GB
- maintenance_work_mem = 512MB
- checkpoint_completion_target = 0.9
- wal_buffers = 16MB
- default_statistics_target = 500
- random_page_cost = 4
- effective_io_concurrency = 2
- work_mem = 6553kB
- min_wal_size = 4GB
- max_wal_size = 16GB

>pgbench -c 8 -P 10 -T 60 -U postgres postgres - ДЛЯ ВСЕХ ТЕСТОВ

На всех тестах в среднем 58000 транзакций.

- latency average = 8.338 ms
- latency stddev = 6.455 ms
- tps = 958.814968 (including connections establishing)
- tps = 958.859564 (excluding connections establishing)

---
log_autovacuum_min_duration = -1 --> 0

Параметр выводит инфу в журнал сервера, эффективность автовакуума не рассматриваем.

- latency average = 8.351 ms
- latency stddev = 6.560 ms
- tps = 957.355227 (including connections establishing)
- tps = 957.399878 (excluding connections establishing)

---
autovacuum_max_workers = 3 --> 10

Общее количество одновременно работающих процессов. Как видим, пропускная способность БД упала при увеличении кол-ва воркеров, 
что может быть связано с накладеыми расходами на процессы. 
На таких ресурсах и такой рабочей нагрузке виртуальной машины они скорее друг другу мешают.

- latency average = 8.496 ms
- latency stddev = 6.422 ms
- tps = 940.996987 (including connections establishing)
- tps = 941.039224 (excluding connections establishing)

--- 
autovacuum_naptime = 1m --> 15s

Интервал прихода воркеров автовакуума, гораниченных количеством autovacuum_max_workers.
Воркеры начали приходить в четыре раза чаще, что дало небольшой прирост пропускной способности БД в секунду 940 --> 960.

- latency average = 8.326 ms
- latency stddev = 5.588 ms
- tps = 960.227654 (including connections establishing)
- tps = 960.271881 (excluding connections establishing)

---
autovacuum_vacuum_threshold = 50 --> 25

Снижаем порог для мёртвых строк в таблице, автовакуум должен случаться чаще, но это повлияло на производительность не в лучшую сторону.

- latency average = 8.503 ms
- latency stddev = 5.537 ms
- tps = 940.265288 (including connections establishing)
- tps = 940.309226 (excluding connections establishing)

---
autovacuum_vacuum_scale_factor = 0.2 --> 01

Еще снижаем порог для мёртвых строк с помощью параметра который определяет долю строк в таблице: 20% --> 10%.

Теперь формула для анализа таблицы будет: pg_stat_all_tables.n_mod_since_analyze >= autovacuum_analyze_threshold + autovacuum_analyze_scale_factor * pg_class.reltupes

Т.е. если число измененных (с момента прошлого анализа) версий строк превышает пороговое значение.

Пропускная способность опять упала, не знаю...

- latency average = 8.311 ms
- latency stddev = 6.574 ms
- tps = 961.937280 (including connections establishing)
- tps = 961.981558 (excluding connections establishing)

---
autovacuum_vacuum_cost_delay = 2ms --> 10ms

Регулятор нагрузки на современных машинах рекомендуется 2ms, на значениях "из прошлого" вроде 20ms производительность падает.

- latency average = 8.672 ms
- latency stddev = 5.424 ms
- tps = 921.979830 (including connections establishing)
- tps = 922.023198 (excluding connections establishing)

---
autovacuum_vacuum_cost_limit = -1 --> 1000

Регулятор нагрузки надо увеличивать если мы увеличиваем чило воркеров, т.е. воркеры мы увеличили, соответственно надо повысить autovacuum_vacuum_cost_limit.
Но -1 (т.е 200) переводим в 1000 и опять получаем снижение производительности. Ну, что тут сказать..

- latency average = 8.706 ms
- latency stddev = 6.533 ms
- tps = 918.284841 (including connections establishing)
- tps = 918.328409 (excluding connections establishing)

---
---

Подытоживая, нужно потратить уйму времени на более менее внятное понимание этих параметров, к тому же многие таблицы требуют индивидуального подхода в оценке их производительности.
