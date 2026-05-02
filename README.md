# BancaInsight — Analisi Customer 360 su Database Bancario (SQL)

![MySQL](https://img.shields.io/badge/MySQL-8.x-4479A1?logo=mysql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Stored%20Procedure-orange)
![Analytics](https://img.shields.io/badge/Analytics-Customer%20360-blue)

## Panoramica

Analisi Customer 360 su database bancario implementata come stored procedure MySQL che aggrega **11 gruppi di KPI per cliente** — transazioni in entrata/uscita, importi, conti per tipologia — in un'unica tabella finale con 30+ colonne. Il pivot dei risultati per tipo di conto è generato dinamicamente a runtime con `GROUP_CONCAT` + `PREPARE/EXECUTE`.

Pattern direttamente applicabile a CRM bancario, assicurativo e telecomunicazioni: qualsiasi contesto enterprise dove si deve aggregare la visione completa del cliente da tabelle relazionali normalizzate.

## Valore Enterprise

| Settore / Azienda | Rilevanza |
|-------------------|-----------|
| Banking & Insurance | Customer 360 view, segmentazione clientela, analisi comportamentale |
| Telecomunicazioni | Analisi consumo e profilo cliente per churn prediction |
| IT Consulting (Accenture, NTT Data) | SQL avanzato per clienti Financial Services |
| Engineering Informatica | Stored procedure complesse in sistemi gestionali enterprise |

## Schema Database

```
banca
├── cliente          — anagrafica cliente (id, nome, cognome, data_nascita)
├── conto            — conti (id_conto, id_cliente, id_tipo_conto)
├── tipo_conto       — tipologie: Base · Business · Famiglie · Privati
├── transazioni      — movimenti (id_conto, id_tipo_trans, importo)
└── tipo_transazione — direzione: '+' entrata / '-' uscita
```

## Output: Tabella Customer 360

Una riga per cliente, 30+ colonne:

| Gruppo KPI | Colonne generate |
|------------|-----------------|
| Anagrafica | Età (calcolata), nome, cognome |
| Conti totali | Numero conti distinti per cliente |
| Conti per tipologia | N° Base · Business · Famiglie · Privati |
| Transazioni uscita | Totale + breakdown per tipo conto |
| Transazioni entrata | Totale + breakdown per tipo conto |
| Importi uscita | Totale + breakdown per tipo conto |
| Importi entrata | Totale + breakdown per tipo conto |

## Tecniche SQL Avanzate

| Tecnica | Scopo |
|---------|-------|
| `GROUP_CONCAT` | Genera dinamicamente la lista colonne — funziona per n tipologie |
| `PREPARE` / `EXECUTE` | Esegue la stringa SQL costruita a runtime |
| `LEFT JOIN` chain (×10) | Join di 10 tabelle temporanee su `id_cliente` senza perdere clienti |
| `COALESCE(..., 0)` | Default zero per clienti senza movimenti su un tipo di conto |
| Subquery su `tipo_transazione` | Separa entrate (`+`) da uscite (`-`) in modo pulito |

## Come Eseguire

```sql
USE banca;
SOURCE /path/to/analisi_clienti.sql;
SELECT * FROM analisi_clienti;
```

La stored procedure completa con tutti gli 11 step intermedi è in `analisi_clienti.sql`.

## Stack Tecnologico

`MySQL 8.x` · `Stored Procedures` · `Dynamic SQL` · `PREPARE/EXECUTE` · `GROUP_CONCAT` · `Tabelle Temporanee`

---

---

# BancaInsight — Bank Customer 360 Analytics (SQL) 🇬🇧

![MySQL](https://img.shields.io/badge/MySQL-8.x-4479A1?logo=mysql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Stored%20Procedure-orange)

## Overview

Customer 360 analytics on a banking database implemented as a single MySQL stored procedure aggregating **11 KPI groups per customer** into a 30+ column fact table. The pivot by account type is generated dynamically at runtime via `GROUP_CONCAT` + `PREPARE/EXECUTE`.

Pattern directly applicable to banking, insurance, and telecom CRM: any enterprise context requiring a complete customer view from normalised relational tables.

## Database Schema

```
banca
├── cliente          — customer master (id, name, surname, birthdate)
├── conto            — accounts (id_conto, id_cliente, id_tipo_conto)
├── tipo_conto       — types: Base · Business · Famiglie · Privati
├── transazioni      — transactions (id_conto, id_tipo_trans, amount)
└── tipo_transazione — direction: '+' inflow / '-' outflow
```

## Output: Customer 360 Table

One row per customer, 30+ columns:

| KPI Group | Columns |
|-----------|---------|
| Demographics | Age (computed), name, surname |
| Total accounts | Count of distinct accounts |
| Accounts by type | N° Base · Business · Famiglie · Privati |
| Outflow transactions | Total + per account type |
| Inflow transactions | Total + per account type |
| Outflow amounts | Total + per account type |
| Inflow amounts | Total + per account type |

## Advanced SQL Techniques

| Technique | Purpose |
|-----------|---------|
| `GROUP_CONCAT` | Builds pivot column list dynamically at runtime |
| `PREPARE` / `EXECUTE` | Runs dynamically constructed SQL string |
| `LEFT JOIN` chain (×10) | Joins 10 temp tables without losing customers |
| `COALESCE(..., 0)` | Defaults to zero for customers with no activity on a type |
| Subquery on `tipo_transazione` | Cleanly separates inflow from outflow transactions |

## How to Run

```sql
USE banca;
SOURCE /path/to/analisi_clienti.sql;
SELECT * FROM analisi_clienti;
```

## Technologies

`MySQL 8.x` · `Stored Procedures` · `Dynamic SQL` · `PREPARE/EXECUTE` · `GROUP_CONCAT` · `Temporary Tables`
