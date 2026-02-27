# MineAgent Skills – Usage Examples

**Real-world workflows combining multiple MineAgent skills for complete mining intelligence solutions**

[![Mining](https://img.shields.io/badge/Industry-Mining-blue.svg)](https://github.com/mineagent/skills)
[![Examples](https://img.shields.io/badge/Workflows-Complete-green.svg)](.)

---

## 📋 Overview

This document provides practical, end-to-end examples of how MineAgent skills work together to solve real mining problems. Each example follows a complete workflow from raw data to actionable insight.

---

## 📑 Table of Contents

1. [Production Intelligence Pipeline](#example-1-production-intelligence-pipeline)
2. [Equipment Reliability & Maintenance](#example-2-equipment-reliability--maintenance)
3. [Geological Resource to Reserve](#example-3-geological-resource-to-reserve)
4. [Safety & Compliance Reporting](#example-4-safety--compliance-reporting)
5. [Executive Portfolio Dashboard](#example-5-executive-portfolio-dashboard)
6. [Shift Supervisor Operations Board](#example-6-shift-supervisor-operations-board)
7. [Cost Control & Budget Analysis](#example-7-cost-control--budget-analysis)
8. [Regulatory Compliance Reporting](#example-8-regulatory-compliance-reporting)

---

## Example 1: Production Intelligence Pipeline

**Skills Used:** `sql-query-optimizer` → `production-kpi-dashboard-designer`

### Business Problem
The monthly production report takes 45 minutes to run, frequently times out, and doesn't provide drill-down capability for supervisors to investigate variances.

### Workflow

```mermaid
graph LR
    A[Raw Shift Data<br/>2.5M rows/month] --> B[SQL Query Optimizer]
    B --> C[Optimized Queries<br/>< 30 seconds]
    C --> D[KPI Dashboard Designer]
    D --> E[Executive Summary]
    D --> F[Shaft-Level View]
    D --> G[Shift Supervisor View]
Step 1: Optimize the Base Queries
User Prompt:

text
Using the mineagent/sql-query-optimizer skill, optimize this production summary query:

SELECT 
    s.shift_date,
    sh.shaft_code,
    COUNT(DISTINCT s.shift_id) as shifts,
    SUM(s.tons_mined) as total_tons,
    SUM(CASE WHEN s.ore = 1 THEN s.tons_mined ELSE 0 END) as ore_tons,
    AVG(s.grade) as avg_grade,
    (SELECT SUM(tons_mined) FROM production_shift 
     WHERE shaft_id = s.shaft_id 
       AND shift_date <= s.shift_date) as running_total
FROM production_shift s
JOIN dim_shaft sh ON s.shaft_id = sh.shaft_id
WHERE s.shift_date BETWEEN '2024-01-01' AND '2024-03-31'
GROUP BY s.shift_date, sh.shaft_code, s.shaft_id
ORDER BY s.shift_date, sh.shaft_code;

Database: SQL Server
Table size: production_shift: 2.5M rows, dim_shaft: 50 rows
Current execution time: 45 minutes
Optimized Output:

sql
-- Optimized query with window functions and covering indexes
WITH daily_production AS (
    SELECT 
        s.shift_date,
        sh.shaft_code,
        s.shaft_id,
        SUM(s.tons_mined) as total_tons,
        SUM(CASE WHEN s.ore = 1 THEN s.tons_mined ELSE 0 END) as ore_tons,
        AVG(s.grade) as avg_grade,
        COUNT(DISTINCT s.shift_id) as shifts
    FROM production_shift s WITH (INDEX(ix_shift_date_shaft))
    JOIN dim_shaft sh ON s.shaft_id = sh.shaft_id
    WHERE s.shift_date BETWEEN '2024-01-01' AND '2024-03-31'
    GROUP BY s.shift_date, sh.shaft_code, s.shaft_id
)
SELECT 
    shift_date,
    shaft_code,
    shifts,
    total_tons,
    ore_tons,
    avg_grade,
    SUM(total_tons) OVER (PARTITION BY shaft_code ORDER BY shift_date) as running_total
FROM daily_production
ORDER BY shift_date, shaft_code;

-- Index recommendations:
CREATE NONCLUSTERED INDEX ix_shift_date_shaft 
ON production_shift(shift_date, shaft_id) 
INCLUDE (tons_mined, ore, grade);
Result: Query execution time: 28 seconds (96% improvement)

Step 2: Design the Production Dashboard
User Prompt:

text
Using the mineagent/production-kpi-dashboard-designer skill, create a three-level dashboard:

1. Executive summary – Site-wide KPIs
2. Shaft-level view – Comparison between shafts
3. Shift supervisor view – Real-time shift progress

Data available through optimized queries.
Users: Mine manager, superintendents, shift supervisors
Refresh: Hourly during shifts, daily for summaries
Dashboard Output:

yaml
dashboard_name: "Production Intelligence Suite"
design:

  # Level 1 - Executive Summary
  - page: "Executive Overview"
    layout:
      - row:
          - card: "Total Production MTD"
            metric: "SUM(tons_mined) WHERE month = current"
            comparison: "vs budget, vs prior year"
            visualization: "big number with trend sparkline"
          
          - card: "Ore Percentage"
            metric: "(SUM(ore_tons) / SUM(tons_mined)) * 100"
            target: "85%"
            visualization: "gauge"
          
          - card: "Average Grade"
            metric: "AVG(grade)"
            comparison: "vs plan"
            visualization: "big number with variance"
      
      - row:
          - chart: "Daily Production Trend"
            type: "line chart"
            metrics: ["actual_tons", "planned_tons", "prior_year_tons"]
            x_axis: "date"
            y_axis: "tons"
          
          - chart: "Production by Shaft"
            type: "horizontal bar"
            metrics: ["tons_mined", "vs_plan_percent"]
            sort: "tons_mined DESC"
      
      - row:
          - table: "Shaft Performance Summary"
            columns: ["shaft", "today_tons", "vs_plan", "mtd_tons", "grade", "status"]
            conditional_formatting:
              - column: "status"
                rules:
                  - "Behind": "🔴"
                  - "On Track": "🟢"
                  - "Ahead": "🔵"

  # Level 2 - Shaft Detail
  - page: "Shaft Performance"
    drill_from: "Executive Overview"
    parameters: ["shaft_code"]
    layout:
      - row:
          - chart: "Last 30 Days Production"
            type: "column chart"
            metrics: ["daily_tons", "target_tons"]
          
          - chart: "Grade Trend"
            type: "line with control limits"
            metrics: ["daily_grade", "upper_control", "lower_control"]
      
      - row:
          - chart: "Hourly Production (Today)"
            type: "step chart"
            metrics: ["cumulative_tons", "target_line"]
          
          - table: "Shift Details"
            columns: ["shift", "foreman", "tons", "ore_pct", "grade", "downtime"]

  # Level 3 - Shift Supervisor View
  - page: "Shift Board"
    refresh: "Real-time (5 min)"
    layout:
      - row:
          - card: "Shift Target"
            metric: "planned_tons"
          
          - card: "Current Tons"
            metric: "actual_tons"
            progress: "actual_tons / planned_tons * 100"
          
          - card: "Hours Remaining"
            metric: "shift_end_time - current_time"
      
      - row:
          - chart: "Hourly Progress"
            type: "gantt"
            activities: ["drill", "blast", "load", "haul"]
            actual_vs_planned: true
          
          - table: "Equipment Status"
            columns: ["equipment", "status", "operator", "hours", "next_service"]
Example 2: Equipment Reliability & Maintenance
Skills Used: predictive-maintenance-analyzer → sql-query-optimizer → production-kpi-dashboard-designer

Business Problem
Unplanned haul truck downtime costs $15,000 per hour. Current reactive maintenance catches failures after they occur. Need predictive analytics to schedule maintenance before failure.

Workflow
Step 1: Analyze Telemetry Data
User Prompt:

text
Using the mineagent/predictive-maintenance-analyzer skill, analyze 6 months of haul truck telemetry:

Equipment: 24 haul trucks (CAT 793)
Data: Vibration (X/Y/Z), engine temp, payload, speed, brake pressure
Sampling: 1 reading per minute per truck
Known failures: 12 engine-related, 8 transmission, 15 brake

Identify patterns that precede failures and generate ML-ready dataset.
Analysis Output:

json
{
  "fleet_health_summary": {
    "analysis_period": "2023-10-01 to 2024-03-31",
    "trucks_analyzed": 24,
    "critical_alerts": 7,
    "watch_list": 12,
    "healthy": 5
  },
  
  "failure_patterns": {
    "engine_failure": {
      "precursor_patterns": [
        {
          "pattern": "Engine temp oscillation > ±8°C for >2 hours",
          "lead_time": "5-8 days",
          "detected_in": "85% of engine failures"
        },
        {
          "pattern": "Vibration RMS increasing trend >0.5 mm/s per week",
          "lead_time": "10-14 days",
          "detected_in": "92% of engine failures"
        }
      ],
      "feature_importance": [
        {"feature": "engine_temp_variance", "importance": 0.35},
        {"feature": "rms_trend_7day", "importance": 0.28},
        {"feature": "load_cycles", "importance": 0.18}
      ]
    },
    
    "brake_failure": {
      "precursor_patterns": [
        {
          "pattern": "Brake pressure response time increasing",
          "threshold": ">0.3 seconds from baseline",
          "lead_time": "3-5 days"
        },
        {
          "pattern": "Temperature differential across brakes",
          "threshold": ">15°C difference",
          "lead_time": "1-2 days"
        }
      ]
    }
  },
  
  "current_alerts": [
    {
      "truck_id": "HT-12",
      "risk_level": "CRITICAL",
      "detected_pattern": "Engine temp oscillation + vibration trend",
      "predicted_failure": "Engine within 7 days",
      "confidence": 0.89,
      "recommended_action": "Immediate engine diagnostic, plan for replacement"
    },
    {
      "truck_id": "HT-08",
      "risk_level": "HIGH",
      "detected_pattern": "Brake pressure response degrading",
      "predicted_failure": "Brake system within 14 days",
      "confidence": 0.92,
      "recommended_action": "Schedule brake inspection next maintenance window"
    },
    {
      "truck_id": "HT-21",
      "risk_level": "MEDIUM",
      "detected_pattern": "Transmission temperature trending up",
      "predicted_failure": "Transmission within 30 days",
      "confidence": 0.76,
      "recommended_action": "Monitor closely, prepare for rebuild"
    }
  ],
  
  "maintenance_schedule_recommendation": [
    {
      "truck": "HT-12",
      "priority": "IMMEDIATE",
      "action": "Engine replacement",
      "estimated_downtime": "48 hours",
      "schedule_by": "2024-04-05"
    },
    {
      "truck": "HT-08",
      "priority": "THIS WEEK",
      "action": "Brake system overhaul",
      "estimated_downtime": "8 hours",
      "schedule_by": "2024-04-10"
    },
    {
      "truck": "HT-21",
      "priority": "THIS MONTH",
      "action": "Transmission rebuild",
      "estimated_downtime": "24 hours",
      "schedule_by": "2024-04-30"
    }
  ]
}
Step 2: Optimize Health Queries
User Prompt:

text
Using the mineagent/sql-query-optimizer skill, optimize real-time equipment health queries for 1-minute refresh.
Optimized Queries:

sql
-- Materialized view for real-time health
CREATE VIEW vw_equipment_health_live AS
WITH latest_readings AS (
    SELECT 
        equipment_id,
        MAX(timestamp) as last_reading,
        AVG(vibration_rms) OVER (PARTITION BY equipment_id ORDER BY timestamp ROWS 59 PRECEDING) as vibration_avg_1hr,
        STDDEV(vibration_rms) OVER (PARTITION BY equipment_id ORDER BY timestamp ROWS 59 PRECEDING) as vibration_std_1hr
    FROM equipment_telemetry
    WHERE timestamp >= DATEADD(hour, -1, GETDATE())
)
SELECT 
    e.equipment_id,
    e.equipment_name,
    lr.last_reading,
    lr.vibration_avg_1hr,
    lr.vibration_std_1hr,
    CASE 
        WHEN lr.vibration_avg_1hr > e.critical_threshold THEN 'CRITICAL'
        WHEN lr.vibration_avg_1hr > e.warning_threshold THEN 'WARNING'
        ELSE 'NORMAL'
    END as health_status
FROM equipment e
JOIN latest_readings lr ON e.equipment_id = lr.equipment_id
WHERE e.equipment_type = 'haul_truck';
Step 3: Design Maintenance Dashboard
User Prompt:

text
Using the mineagent/production-kpi-dashboard-designer skill, create a maintenance dashboard showing:

- Fleet health overview (traffic light)
- Predictive alerts with confidence scores
- MTBF/MTTR trends
- Maintenance schedule optimizer
Dashboard Output:

yaml
dashboard_name: "Fleet Reliability Center"
refresh: "Real-time with 5-min cache"

pages:
  - name: "Fleet Overview"
    layout:
      - row:
          - card: "Fleet Health Score"
            metric: "(healthy_trucks / total_trucks) * 100"
            visualization: "big number with gauge"
          
          - card: "Critical Alerts"
            metric: "COUNT(equipment WHERE status = 'CRITICAL')"
            visualization: "big number (red)"
          
          - card: "Watch List"
            metric: "COUNT(equipment WHERE status = 'WARNING')"
            visualization: "big number (yellow)"
      
      - row:
          - chart: "Fleet Health Matrix"
            type: "grid heatmap"
            x_axis: "equipment_type"
            y_axis: "equipment_id"
            color: "health_status"
            tooltip: ["mtbf", "mttr", "next_service"]
          
          - chart: "Alert Timeline"
            type: "gantt"
            metrics: ["equipment", "alert_start", "predicted_failure"]
            colors: "by_severity"
      
      - row:
          - table: "Critical Alerts"
            columns: ["truck", "alert", "confidence", "predicted_failure", "action"]
            conditional_formatting:
              - column: "confidence"
                rules:
                  - ">0.9": "🔴"
                  - ">0.7": "🟡"
                  - "<0.7": "🔵"

  - name: "Reliability Metrics"
    layout:
      - row:
          - chart: "MTBF Trend (Rolling 30 days)"
            type: "line with target"
            metrics: ["mtbf_by_type"]
          
          - chart: "MTTR Trend"
            type: "line"
            metrics: ["mttr_by_type"]
      
      - row:
          - chart: "Failure Pareto"
            type: "bar + line"
            metrics: ["failure_count_by_component", "cumulative_percentage"]
          
          - table: "Top 10 Failure Modes"
            columns: ["component", "failure_mode", "count", "avg_downtime", "trend"]
Example 3: Geological Resource to Reserve
Skills Used: geology-structure-parser → sql-query-optimizer → production-kpi-dashboard-designer

Business Problem
Resource model reconciliation shows 15% variance between modeled and actual tons. Multiple data sources with inconsistent naming make root cause analysis impossible.

Workflow
graph TD
    A[Drill Hole Data<br/>CSV/Excel] --> B[Geology Structure Parser]
    C[Wireframes<br/>DXF/Datamine] --> B
    D[Survey Pickups<br/>Vulcan] --> B
    B --> E[Standardized Structure<br/>JSON]
    E --> F[SQL Query Optimizer]
    F --> G[Reconciliation Queries]
    G --> H[Reconciliation Dashboard]
    E --> I[Block Model Update]
Step 1: Parse and Standardize Geological Data
User Prompt:

text
Using the mineagent/geology-structure-parser skill, process these geological data sources:

1. Fault interpretations from 3 different geologists (CSV, inconsistent naming)
2. Reef wireframes from Leapfrog export (DXF)
3. Survey pickups from development headings (Vulcan format)

Standardize all to common naming convention and validate completeness.
Parsing Output:

json
{
  "processing_summary": {
    "sources_processed": 3,
    "total_structures": 247,
    "standardized": 247,
    "issues_flagged": 43
  },
  
  "naming_standardization": {
    "original_variations": {
      "Main Fault": ["Main Fault", "Main", "Fault Main", "MF"],
      "VR1 Reef": ["VR1", "VR-1", "Reef VR1", "VR1 Reef"]
    },
    "mapped_to": {
      "Main_Fault": "F001",
      "VR1_Reef": "R015"
    }
  },
  
  "validation_issues": [
    {
      "structure": "F023",
      "type": "fault",
      "issue": "Missing dip angle",
      "severity": "HIGH",
      "action": "Review with senior geologist"
    },
    {
      "structure": "R045",
      "type": "reef",
      "issue": "Discontinuous wireframe",
      "severity": "MEDIUM",
      "details": "Gap of 45m between interpreted sections",
      "action": "Additional drilling recommended"
    }
  ],
  
  "bi_ready_json": {
    "faults": [...],
    "reefs": [...],
    "wireframes": [...]
  }
}
Step 2: Optimize Reconciliation Queries
User Prompt:

text
Using the mineagent/sql-query-optimizer skill, create queries to reconcile:
- Block model tons vs actual production
- Modeled grade vs actual grade
- By stope, by development round
Optimized Queries:

sql
-- Stope reconciliation query
WITH modeled_tons AS (
    SELECT 
        stope_id,
        SUM(tonnage) as model_tons,
        AVG(grade) as model_grade
    FROM block_model
    WHERE stope_id IN (SELECT DISTINCT stope_id FROM production_actual)
    GROUP BY stope_id
),
actual_tons AS (
    SELECT 
        stope_id,
        SUM(tons_mined) as actual_tons,
        AVG(grade) as actual_grade
    FROM production_actual
    WHERE stope_id IS NOT NULL
    GROUP BY stope_id
)
SELECT 
    m.stope_id,
    s.stope_name,
    m.model_tons,
    a.actual_tons,
    a.actual_tons - m.model_tons as tons_variance,
    (a.actual_tons - m.model_tons) / m.model_tons * 100 as tons_variance_pct,
    m.model_grade,
    a.actual_grade,
    a.actual_grade - m.model_grade as grade_variance,
    CASE 
        WHEN ABS((a.actual_tons - m.model_tons) / m.model_tons * 100) > 15 THEN 'CRITICAL'
        WHEN ABS((a.actual_tons - m.model_tons) / m.model_tons * 100) > 5 THEN 'WARNING'
        ELSE 'GOOD'
    END as reconciliation_status
FROM modeled_tons m
JOIN actual_tons a ON m.stope_id = a.stope_id
JOIN dim_stope s ON m.stope_id = s.stope_id
ORDER BY tons_variance_pct DESC;
Step 3: Design Reconciliation Dashboard
User Prompt:

text
Using the mineagent/production-kpi-dashboard-designer skill, create a geological reconciliation dashboard showing:

- Overall reconciliation factor by area
- Stope-by-stope variance heatmap
- Grade control performance
- Causes of variance analysis
Dashboard Output:

yaml
dashboard_name: "Geological Reconciliation"
refresh: "Monthly, with weekly updates for active stopes"

pages:
  - name: "Reconciliation Overview"
    layout:
      - row:
          - card: "Overall Tons Factor"
            metric: "SUM(actual_tons) / SUM(model_tons)"
            target: "1.0 ±0.05"
            visualization: "gauge with range"
          
          - card: "Overall Grade Factor"
            metric: "AVG(actual_grade) / AVG(model_grade)"
            target: "1.0 ±0.03"
            visualization: "gauge"
          
          - card: "Stopes Outside Tolerance"
            metric: "COUNT(stope WHERE reconciliation_status = 'CRITICAL')"
            visualization: "big number (red)"
      
      - row:
          - chart: "Reconciliation Matrix"
            type: "scatter plot"
            x_axis: "model_tons"
            y_axis: "actual_tons"
            color: "reconciliation_status"
            quadrants:
              - name: "Over Mined"
                condition: "actual_tons > model_tons * 1.1"
              - name: "Under Mined"
                condition: "actual_tons < model_tons * 0.9"
          
          - chart: "Grade Reconciliation"
            type: "scatter"
            x_axis: "model_grade"
            y_axis: "actual_grade"
            reference_line: "x=y"
      
      - row:
          - table: "Stope Reconciliation Detail"
            columns: ["stope", "model_tons", "actual_tons", "variance_%", "status", "geologist", "review_notes"]
            conditional_formatting:
              - column: "status"
                mapping:
                  "CRITICAL": "🔴"
                  "WARNING": "🟡"
                  "GOOD": "🟢"

  - name: "Variance Analysis"
    layout:
      - row:
          - chart: "Variance by Cause"
            type: "pie"
            metrics: ["geological", "mining", "measurement", "other"]
          
          - chart: "Variance by Geologist"
            type: "box plot"
            metrics: ["variance_percent by interpreter"]
      
      - row:
          - chart: "Monthly Trend"
            type: "line"
            metrics: ["reconciliation_factor", "confidence_interval"]
Example 4: Safety & Compliance Reporting
Skills Used: safety-report-generator → production-kpi-dashboard-designer

Business Problem
Monthly safety reporting is manual, taking 3 days to compile. Board wants to see trends, not just numbers. Regulators require specific formats.

Workflow
graph LR
    A[Incident Database] --> B[Safety Report Generator]
    B --> C[Monthly HSE Report]
    B --> D[Board Summary]
    B --> E[Regulatory Filing]
    C --> F[Trend Dashboard]
    D --> G[Executive Presentation]
    E --> H[MSHA/OSHA Format]
Step 1: Generate Monthly Safety Report
User Prompt:

text
Using the mineagent/safety-report-generator skill, create a monthly HSE report for March 2024:

Site: North Mine Complex
Hours worked: 425,000
Incidents:
- 2 LTIs (hand injury, ankle fracture)
- 4 MTIs
- 18 First Aid
- 45 Near Misses (3 high potential)
- 2 Property Damage

Include:
- LTIFR, TRIFR calculations
- Trend vs previous month and same month last year
- High potential near miss details
- Leading indicators
- Corrective action status
Generated Report:

markdown
# Monthly HSE Report – North Mine Complex
**Period:** March 2024
**Generated:** 2024-04-02

## Executive Summary

During March, North Mine Complex recorded 2 lost time injuries resulting in an LTIFR of 4.71, a 15% increase from February but a 22% improvement from March 2023. Three high-potential near misses were recorded, the most significant being a haul truck brake failure on a 12% decline.

**Key Metrics:**
- LTIFR: 4.71 (Target: 5.0)
- TRIFR: 16.47 (Target: 15.0)
- Severity Rate: 35.3
- Days Since Last LTI: 14
- Days Since Last Fatality: 1,250

## Incident Breakdown

| Type | Count | % of Total | vs Last Month |
|------|-------|------------|---------------|
| LTI | 2 | 8% | +1 |
| MTI | 4 | 16% | 0 |
| First Aid | 18 | 72% | -2 |
| **Total Recordable** | **6** | **24%** | **+1** |

## High Potential Near Misses

| Date | Description | Corrective Action | Status |
|------|-------------|-------------------|--------|
| 03/16 | Haul truck brake failure on decline | Fleet-wide brake inspection | In Progress |
| 03/22 | Rock fall in development heading | Enhanced scaling procedure | Complete |
| 03/28 | Conveyor start-up during maintenance | Lock-out/tag-out audit | In Progress |

## Leading Indicators

| Indicator | Value | Target | Status |
|-----------|-------|--------|--------|
| Safety Observations | 245 | 200 | 🟢 |
| Critical Control Verifications | 92% | 95% | 🟡 |
| Risk Assessments Completed | 98% | 100% | 🟢 |
| Corrective Action Closure | 87% | 90% | 🟡 |

## Recommendations

1. **Immediate:** Complete fleet brake inspections by April 15
2. **High:** Targeted hand injury prevention campaign in development
3. **Medium:** Review isolation procedures with all maintenance crews
Step 2: Design Safety Dashboard
User Prompt:

text
Using the mineagent/production-kpi-dashboard-designer skill, create a safety dashboard showing:

- Real-time LTIFR/TRIFR vs target
- Incident trend analysis
- High potential near miss tracker
- Leading indicator scorecard
- Corrective action status
Dashboard Output:

yaml
dashboard_name: "Safety Performance Center"
refresh: "Daily"

pages:
  - name: "Safety Overview"
    layout:
      - row:
          - card: "LTIFR (YTD)"
            metric: "ltifr_current"
            target: "5.0"
            comparison: "vs prior year"
            visualization: "gauge"
          
          - card: "TRIFR (YTD)"
            metric: "trifr_current"
            target: "15.0"
            visualization: "gauge"
          
          - card: "Days Since Last LTI"
            metric: "days_since_lti"
            visualization: "big number with milestone"
          
          - card: "Days Since Last Fatality"
            metric: "days_since_fatality"
            visualization: "big number with star"
      
      - row:
          - chart: "LTIFR Trend (24 months)"
            type: "line with target"
            metrics: ["monthly_ltifr", "rolling_avg", "target_line"]
            annotations: ["significant_incidents"]
          
          - chart: "Incident Pyramid"
            type: "funnel"
            levels: ["fatalities", "lti", "mti", "first_aid", "near_miss"]
      
      - row:
          - chart: "High Potential Near Miss Tracker"
            type: "timeline"
            events: "high_potential_events"
            color: "status"
          
          - table: "Open Corrective Actions"
            columns: ["action", "due_date", "responsible", "status", "days_overdue"]
            conditional_formatting:
              - column: "days_overdue"
                rules:
                  - ">30": "🔴"
                  - ">7": "🟡"
                  - "<7": "🟢"

  - name: "Leading Indicators"
    layout:
      - row:
          - chart: "Safety Observation Trends"
            type: "line"
            metrics: ["observations", "target"]
          
          - chart: "Critical Control Effectiveness"
            type: "gauge matrix"
            controls: ["ground_control", "isolation", "transport", "explosives"]
Example 5: Executive Portfolio Dashboard
Skills Used: sql-query-optimizer → production-kpi-dashboard-designer

Business Problem
CEO needs to see performance across 5 mine sites at a glance. Current reporting is fragmented, delayed, and inconsistent.

Workflow
graph TD
    A[Site A Data] --> D[SQL Query Optimizer]
    B[Site B Data] --> D
    C[Site C Data] --> D
    D --> E[Standardized Metrics]
    E --> F[Executive Dashboard]
    F --> G[Board Pack]
    F --> H[Investor Reports]
Dashboard Design
User Prompt:

text
Using the mineagent/production-kpi-dashboard-designer skill, create an executive portfolio dashboard showing:

- 5 mine sites
- Key metrics: Production, Cost, Safety, Development
- Traffic light status for each
- Drill-down to site detail
- YTD vs Budget vs Prior Year
Dashboard Output:

yaml
dashboard_name: "Group Portfolio Performance"
refresh: "Daily"

pages:
  - name: "Portfolio Overview"
    layout:
      - row:
          - chart: "Group Performance Scorecard"
            type: "matrix heatmap"
            rows: ["North Mine", "South Pit", "East UG", "West Mill", "Central"]
            columns: ["Production", "Cost/Ton", "Safety", "Development"]
            values: "variance_percent"
            color_scale: ["red", "yellow", "green"]
            tooltip: ["actual", "budget", "prior_year"]
      
      - row:
          - chart: "Group Production Trend"
            type: "line"
            metrics: ["total_tons", "budget_tons", "prior_year_tons"]
          
          - chart: "Group Cost per Ton"
            type: "bar"
            metrics: ["cost_per_ton_by_site"]
            sort: "descending"
      
      - row:
          - table: "Site Summary"
            columns: [
              "site", 
              "production_vs_budget", 
              "cost_vs_budget", 
              "ltifr", 
              "development_vs_plan",
              "status"
            ]
            conditional_formatting:
              - column: "status"
                rules:
                  - "Behind on 2+ metrics": "🔴"
                  - "Behind on 1 metric": "🟡"
                  - "On Track": "🟢"
Example 6: Shift Supervisor Operations Board
Skills Used: sql-query-optimizer → production-kpi-dashboard-designer

Business Problem
Shift supervisors need real-time visibility into production vs plan, equipment status, and next actions – all on a mobile device underground.

Workflow
graph LR
    A[Real-time Sensors] --> B[Optimized Queries]
    B --> C[Mobile Dashboard]
    C --> D[Shift Supervisor]
    D --> E[Real-time Decisions]
Dashboard Design
User Prompt:

text
Using the mineagent/production-kpi-dashboard-designer skill, create a mobile-friendly shift supervisor dashboard showing:

- Current shift production vs target
- Equipment status (operating/down)
- Grade vs plan
- Next hour actions
- Alerts
Dashboard Output:

yaml
dashboard_name: "Shift Commander (Mobile)"
refresh: "Real-time (1 minute)"
viewport: "mobile (375x667)"

layout:
  - header:
      - title: "Day Shift - Shaft 4"
      - time: "14:30"
      - progress: "74% complete"
  
  - row:
      - card: "Target"
        value: "5,200 t"
        size: "small"
      
      - card: "Actual"
        value: "3,850 t"
        size: "small"
        color: "green"
      
      - card: "Remaining"
        value: "1,350 t"
        size: "small"
        subtitle: "5.5 hours left"
  
  - chart: "Hourly Progress"
    type: "progress"
    current: 3850
    target: 5200
    show_percentage: true
  
  - section: "Equipment Status"
    items:
      - equipment: "Loader-04"
        status: "Operating"
        icon: "🟢"
      - equipment: "Loader-07"
        status: "Down - Hydraulics"
        icon: "🔴"
        action: "Check at 15:00"
      - equipment: "Drill-12"
        status: "Operating"
        icon: "🟢"
      - equipment: "Truck-08"
        status: "En Route"
        icon: "🟡"
  
  - section: "Current Grade"
    value: "2.4 g/t"
    target: "2.2 g/t"
    status: "Above Plan"
    color: "green"
  
  - section: "Next Actions"
    items:
      - "□ Clear muck at drawpoint 4B"
      - "□ Move loader to south face"
      - "□ Check drill-07 hydraulics"
      - "□ Safety brief at 15:30"
  
  - alerts:
      - type: "warning"
        message: "Drill-07 hydraulics due for check"
      - type: "info"
        message: "Blast scheduled at 16:00"
Example 7: Cost Control & Budget Analysis
Skills Used: sql-query-optimizer → production-kpi-dashboard-designer

Business Problem
Cost overruns identified too late. Need real-time cost per ton visibility by activity, with variance alerts.

Workflow
Dashboard Design
User Prompt:

text
Using the mineagent/production-kpi-dashboard-designer skill, create a cost control dashboard showing:

- Cost per ton by site and activity
- Budget vs actual variance
- Trend analysis
- Top 5 cost drivers
- Forecast vs budget
Dashboard Output:

yaml
dashboard_name: "Cost Control Center"
refresh: "Daily"

pages:
  - name: "Cost Overview"
    layout:
      - row:
          - card: "Group Cost/Ton"
            metric: "total_cost / total_tons"
            budget: "$18.50"
            actual: "$19.25"
            variance: "+$0.75"
            status: "🔴"
          
          - card: "Total Spend"
            metric: "$42.5M"
            budget: "$40.2M"
            variance: "+$2.3M"
          
          - card: "Months of Budget Remaining"
            metric: "4.2"
            projection: "3.8 at current burn"
      
      - row:
          - chart: "Cost per Ton by Site"
            type: "bar"
            metrics: ["actual", "budget", "prior_year"]
            sort: "actual DESC"
          
          - chart: "Cost Breakdown"
            type: "pie"
            metrics: ["labor", "fuel", "parts", "contractors", "other"]
      
      - row:
          - chart: "Variance Trend"
            type: "line"
            metrics: ["actual_cpt", "budget_cpt", "rolling_avg"]
          
          - table: "Top Variances"
            columns: ["category", "budget", "actual", "variance", "trend"]
Example 8: Regulatory Compliance Reporting
Skills Used: safety-report-generator → sql-query-optimizer

Business Problem
MSHA/OSHA reporting is manual, error-prone, and time-consuming. Need automated compliance reports.

Workflow
Report Generation
User Prompt:

text
Using the mineagent/safety-report-generator skill, generate MSHA Part 50 reports for Q1 2024.

Format: MSHA-7000-1 required fields
Include: All reportable incidents with required classifications
Generated Output:

json
{
  "msha_part50_report": {
    "mine_id": "12-34567",
    "quarter": "Q1 2024",
    "submission_date": "2024-04-15",
    "incidents": [
      {
        "incident_number": "2024-005",
        "date": "2024-02-15",
        "time": "14:30",
        "location": "North Shaft, 2450 Level",
        "incident_type": "Hand Injury",
        "severity": "LTI",
        "days_lost": 14,
        "msha_classification": "Handling Material",
        "equipment_involved": "Scaling Bar",
        "investigation_summary": "Worker's hand caught between rock and scaling bar",
        "root_cause": "Inadequate barring down procedure",
        "corrective_action": "Two-person barring protocol implemented"
      }
    ],
    "summary": {
      "total_hours": 1250000,
      "employee_count": 520,
      "contractor_hours": 325000,
      "fatalities": 0,
      "lti_count": 2,
      "total_recordables": 8
    }
  }
}
🔗 Cross-Skill Integration Patterns
Pattern 1: Data Pipeline
text
Raw Data → SQL Optimizer → Clean Data → KPI Designer → Dashboard
                              ↘ Predictive Maint → Alerts
                              ↘ Safety Report → Compliance
Pattern 2: Root Cause Analysis
text
Production Variance → SQL Optimizer → Data Quality Check
                                    → Geology Parser → Model Validation
                                    → KPI Dashboard → Variance Visualization
Pattern 3: Integrated Operations Center
text
Real-time Telemetry → Predictive Maint → Equipment Health
Production Data     → SQL Optimizer   → Shift Dashboard
Safety Incidents    → Safety Report   → HSE Alerts
                    → KPI Designer    → Ops Center View
📝 Contributing Examples
Have a real-world example to share? Submit a pull request with:

Business problem description

Skills used and workflow

Input data description

Generated outputs

Results and benefits

See CONTRIBUTING.md for guidelines.

Built for industrial production environments. Not generic AI toys. Not "vibe coding".