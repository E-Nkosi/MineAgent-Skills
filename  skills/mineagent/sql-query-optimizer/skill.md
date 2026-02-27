# SQL Query Optimizer for Mining Databases

**Version:** 1.2.0  
**Namespace:** `mineagent/sql-query-optimizer`  
**Compatibility:** SQL Server, PostgreSQL, MySQL, Oracle, SQLite  
**Last Updated:** 2024-03-29

---

## 🎯 Purpose

This skill optimizes large-scale SQL queries commonly found in mining production environments. Mining databases are unique – they contain massive time-series data, complex geological joins, and shaft-level aggregations that generic SQL optimizers mishandle.

The objective is to:
- Reduce execution time by 40-80%
- Eliminate unnecessary table and index scans
- Improve index usage with composite covering indexes
- Enforce shaft-level and reef-aware filtering
- Maintain data accuracy and business logic integrity
- Reduce I/O and server load

---

## 🔍 When to Use

Use this skill when you encounter:

- **Slow BI dashboards** taking minutes to load
- **Heavy geological joins** between block models and survey data
- **Shaft-level filtering** that performs poorly
- **Block model aggregations** timing out
- **Equipment telemetry queries** slowing down operations
- **Production reports** that timeout or fail
- **Shift data queries** that scan entire tables unnecessarily
- **Ad-hoc queries** from geologists or engineers that run too long
- **ETL processes** that exceed maintenance windows

---

## 📋 Input Validation

Before processing, verify:

```yaml
required_inputs:
  - sql_query: Must be valid SQL syntax
  - database_type: Must be one of supported platforms (SQL Server, PostgreSQL, MySQL, Oracle, SQLite)

optional_but_recommended:
  - execution_plan: Dramatically improves optimization accuracy (JSON, XML, or text format)
  - table_statistics: Enables cardinality-based decisions (row counts, data distribution)
  - index_list: Helps detect missing composite indexes
  - query_purpose: Business context (e.g., "daily production report", "monthly reconciliation")
  - performance_requirements: Target execution time (e.g., "< 5 seconds", "< 30 seconds")