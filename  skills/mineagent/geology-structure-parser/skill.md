# Geology Structure Parser for Mining

**Version:** 1.0.0  
**Namespace:** `mineagent/geology-structure-parser`  
**Compatibility:** CSV, JSON, DXF, Datamine, Vulcan, Leapfrog, Excel  
**Last Updated:** 2024-03-29

---

## 🎯 Purpose

This skill processes, validates, and standardizes geological structural data including faults, dykes, reef horizons, and wireframes. Geological structures are fundamental to resource estimation, mine planning, and operational decision-making – yet they often arrive in inconsistent formats with missing attributes or non-standard naming conventions.

The objective is to:
- Standardize geological naming across disparate data sources
- Validate spatial and attribute completeness
- Flag missing or anomalous structural elements
- Generate structured JSON for BI and visualization tools
- Ensure traceability between interpreted structures and source data
- Prepare data for resource modeling and reconciliation

---

## 🔍 When to Use

Use this skill when you encounter:

- **Inconsistent naming** of faults and dykes across different surveys
- **Missing attributes** in structural data (dip, strike, confidence)
- **Wireframe validation** before resource estimation
- **Reef horizon interpretation** for shaft planning
- **Fault clustering analysis** for geotechnical studies
- **Data migration** between geological software packages
- **BI integration** requiring standardized geological dimensions
- **Reconciliation** between interpreted and as-mined structures
- **Exploration data** needing QA/QC before modeling

---

## 📋 Input Validation

Before processing, verify:

```yaml
required_inputs:
  - geological_data: Raw structural data (file path or content)
  - data_format: Source format (csv, json, dxf, datamine, vulcan, leapfrog, excel)
  - structure_type: Type of structure to parse (fault, dyke, reef, wireframe, horizon)

optional_but_recommended:
  - coordinate_reference_system: Spatial reference (WGS84, LO27, etc.)
  - shaft_reference: Mine-specific shaft naming conventions
  - mine_calibration_data: Known reference points for validation
  - expected_attribute_list: Required fields for completeness checking
  - validation_rules: Custom business rules for specific mine sites