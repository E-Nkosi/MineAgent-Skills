# Production KPI Dashboard Designer for Mining

**Version:** 1.0.0  
**Namespace:** `mineagent/production-kpi-dashboard-designer`  
**Compatibility:** Power BI, Tableau, Grafana, Superset, Excel  
**Departments:** Production, Maintenance, Geology, Surveying, Finance, Executive, Safety  
**Last Updated:** 2024-03-29

---

## 🎯 Purpose

This skill designs optimized KPI dashboards for mining operations. Mining generates massive amounts of data – shift reports, equipment telemetry, grade control assays, survey pickups – but turning this data into actionable insights requires domain-aware design. Generic dashboards miss shaft-level context, misinterpret development metrics, and hide critical variances.

The objective is to:
- Define mining-specific KPIs by department and role
- Recommend optimal visualizations for each metric
- Design intuitive, drill-down dashboard layouts
- Generate optimized aggregation logic and queries
- Prevent misleading charts common in mining reporting
- Enable data-driven decisions from boardroom to blast crew
- Standardize metrics across sites for benchmarking

---

## 🔍 When to Use

Use this skill when you need to:

- **Design a new production dashboard** from scratch
- **Redesign confusing existing dashboards** that hide insights
- **Standardize KPIs across multiple mine sites**
- **Create executive summaries** for board reporting
- **Build department-specific views** (production, maintenance, geology)
- **Implement real-time operations dashboards** in control rooms
- **Develop mobile dashboards** for supervisors in the field
- **Automate regulatory reporting** compliance dashboards
- **Track cost per ton** across operations
- **Monitor equipment utilization** and availability

---

## 📋 Input Validation

Before processing, verify:

```yaml
required_inputs:
  - department: Target department (production, maintenance, geology, surveying, finance, executive, safety)
  - data_schema: Available tables, fields, and relationships
  - business_questions: Key questions the dashboard must answer

optional_but_recommended:
  - existing_dashboards: Current dashboards to improve or replace
  - user_personas: Who will use the dashboard (roles and technical level)
  - refresh_frequency: How often data updates (real-time, hourly, daily, weekly)
  - alert_thresholds: Values that trigger notifications
  - date_range_preference: Default view (today, week, month, quarter, year-to-date)
  - comparison_periods: Compare against (previous period, same period last year, budget)
  - budget_targets: Planned targets for variance analysis