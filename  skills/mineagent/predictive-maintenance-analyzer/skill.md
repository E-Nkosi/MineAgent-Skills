# Predictive Maintenance Analyzer for Mining Equipment

**Version:** 1.0.0  
**Namespace:** `mineagent/predictive-maintenance-analyzer`  
**Compatibility:** Drills, Loaders, Haul Trucks, Conveyors, Crushers, Mills, Ventilation Fans, Pumps  
**Data Sources:** Telemetry CSV, Historian DB, SCADA, IoT Sensors, Maintenance Logs, CMMS  
**Last Updated:** 2024-03-29

---

## 🎯 Purpose

This skill analyzes equipment telemetry data to detect failure patterns, predict maintenance needs, and prevent unplanned downtime in mining operations. Mining equipment operates under extreme conditions – vibration, temperature, and pressure data contain early warning signs that human analysts often miss until failure occurs.

The objective is to:
- Detect anomalies before they become failures
- Predict remaining useful life (RUL) of components
- Prioritize maintenance activities by criticality
- Generate ML-ready datasets for custom model training
- Reduce unplanned downtime by 30-50%
- Extend equipment lifespan through condition-based maintenance
- Optimize maintenance scheduling and parts inventory

---

## 🔍 When to Use

Use this skill when you encounter:

- **Unplanned equipment downtime** affecting production targets
- **Vibration data** from drills, conveyors, or crushers needing analysis
- **Temperature anomalies** in motors, bearings, or hydraulic systems
- **Pressure fluctuations** in pumps or ventilation systems
- **Maintenance logs** showing recurring failure patterns
- **Telemetry data** requiring feature engineering for ML models
- **Condition monitoring** programs needing automation
- **Reliability engineering** studies requiring failure pattern analysis
- **Conveyor belt monitoring** for tear and wear detection
- **Drill health assessment** based on penetration rates and vibration

---

## 📋 Input Validation

Before processing, verify:

```yaml
required_inputs:
  - equipment_type: Type of equipment (drill, loader, haul_truck, conveyor, crusher, mill, fan, pump)
  - telemetry_data: Time-series sensor data (CSV, JSON, or database connection)
  - data_timestamp_field: Field containing timestamp information

optional_but_recommended:
  - maintenance_history: Historical maintenance records
  - failure_logs: Documented failure events with causes
  - equipment_specifications: Manufacturer specs for normal operating ranges
  - operating_parameters: Expected operating conditions (load, speed, etc.)
  - threshold_values: Custom alert thresholds by equipment type
  - baseline_data: Known good operation data for comparison
  - sampling_frequency: Data collection interval (seconds, minutes, hours)