# Safety Report Generator for Mining Operations

**Version:** 1.0.0  
**Namespace:** `mineagent/safety-report-generator`  
**Compatibility:** Monthly HSE Reports, Board Summaries, Incident Investigations, Near Miss Analysis  
**Regulations:** MSHA, OSHA, ICMM, ISO 45001  
**Last Updated:** 2024-03-29

---

## 🎯 Purpose

This skill transforms incident logs, near misses, and safety observations into executive-level safety reports for mining operations. Mining is a high-risk industry where safety performance directly impacts license to operate, workforce wellbeing, and regulatory compliance. Raw incident data hides insights – this skill extracts meaning, identifies patterns, and drives continuous improvement.

The objective is to:
- Calculate accurate safety frequency rates (LTIFR, TRIFR)
- Categorize incidents by type, severity, and contributing factors
- Identify significant trends before they become patterns
- Produce executive summaries that drive action
- Highlight leading indicators for proactive safety management
- Generate actionable recommendations, not just statistics
- Support regulatory reporting and compliance requirements
- Maintain investigation integrity while enabling board-level communication

---

## 🔍 When to Use

Use this skill when you need to:

- **Generate monthly HSE reports** for management review
- **Prepare board safety presentations** for directors
- **Analyze incident trends** across sites or periods
- **Calculate LTIFR, TRIFR, and other key safety metrics**
- **Investigate significant incidents** with structured analysis
- **Track near miss trends** as leading indicators
- **Report to regulators** (MSHA, OSHA) with accurate data
- **Compare safety performance** against industry benchmarks
- **Identify high-risk activities** or locations
- **Track corrective action completion** and effectiveness
- **Communicate safety performance** to workforce and stakeholders

---

## 📋 Input Validation

Before processing, verify:

```yaml
required_inputs:
  - report_period: Time period for the report (month, quarter, year, YTD)
  - incident_data: Structured incident records with dates, types, severities
  - workforce_hours: Total hours worked by all employees and contractors

optional_but_recommended:
  - previous_period_data: Historical data for trend comparison
  - targets_and_goals: Company safety targets for variance analysis
  - investigation_reports: Detailed findings for significant incidents
  - corrective_actions_status: Open/closed status of action items
  - audit_results: Recent safety audit findings
  - leading_indicators: Proactive safety metrics (inspections, observations, training)
  - site_information: Site names, departments, locations for breakdowns
  - management_comments: Contextual input from leadership