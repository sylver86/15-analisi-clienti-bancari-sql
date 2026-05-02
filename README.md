# Bank Customer Analytics — SQL Stored Procedure

![MySQL](https://img.shields.io/badge/MySQL-8.x-4479A1?logo=mysql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Stored%20Procedure-orange)
![Analytics](https://img.shields.io/badge/Analytics-Customer%20360-blue)

## Overview

Customer 360 analytics for a banking database, implemented as a single MySQL stored procedure. The analysis aggregates **11 KPI groups** per customer across all account types and transaction directions, producing a comprehensive per-customer fact table with 30+ columns — ready for BI or customer segmentation.

Demonstrates advanced SQL: dynamic pivot via `PREPARE`/`EXECUTE`, multi-level `LEFT JOIN` chains, `GROUP_CONCAT` for runtime column generation, and `COALESCE` null-safety throughout.

---

## Database Schema

```
banca
├── cliente          — customer master (id_cliente, nome, cognome, data_nascita)
├── conto            — accounts (id_conto, id_cliente, id_tipo_conto)
├── tipo_conto       — account types: Base · Business · Famiglie · Privati
├── transazioni      — transactions (id_conto, id_tipo_trans, importo)
└── tipo_transazione — transaction direction: '+' inflow / '-' outflow
```

---

## Output: Customer 360 Table

One row per customer, 30+ columns:

| KPI Group | Columns Generated |
|-----------|------------------|
| Demographics | Age (computed via `TIMESTAMPDIFF`), name, surname |
| Total accounts | Count of distinct accounts per customer |
| Accounts by type | N° Base · N° Business · N° Famiglie · N° Privati |
| Outflow transaction count | Total + breakdown per account type |
| Inflow transaction count | Total + breakdown per account type |
| Outflow amounts | Total + breakdown per account type |
| Inflow amounts | Total + breakdown per account type |

---

## Key SQL Techniques

| Technique | Purpose |
|-----------|---------|
| `GROUP_CONCAT` | Builds pivot column list dynamically — works for any number of account types |
| `PREPARE` / `EXECUTE` | Runs the dynamically constructed SQL string at runtime |
| `LEFT JOIN` chain (×10) | Joins all temporary KPI tables on `id_cliente` without losing customers |
| `COALESCE(..., 0)` | Defaults missing values to zero for customers with no transactions |
| Subquery filter on `tipo_transazione` | Cleanly separates inflow (`+`) from outflow (`-`) transactions |

---

## How to Run

```sql
-- 1. Connect to MySQL
mysql -u root -p

-- 2. Select the database
USE banca;

-- 3. Run the analysis script
SOURCE /path/to/analisi_clienti.sql;

-- 4. Query the results
SELECT * FROM analisi_clienti;
```

Full stored procedure with all 11 temporary tables and the final `CREATE TABLE analisi_clienti` available in `analisi_clienti.sql`.

---

## Technologies

`MySQL 8.x` · `Stored Procedures` · `Dynamic SQL` · `PREPARE/EXECUTE` · `GROUP_CONCAT` · `Temporary Tables` · `LEFT JOIN`
