

# MineAgent Skills

**Production-Grade AI Skills for Mining & Industrial Intelligence**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://github.com/mineagent/skills/graphs/commit-activity)
[![Mining](https://img.shields.io/badge/Industry-Mining-blue.svg)](https://github.com/mineagent/skills)
[![AI](https://img.shields.io/badge/AI-Ready-purple.svg)](https://github.com/mineagent/skills)

---

## 📋 Overview

Production-grade AI capabilities for mining and heavy industry. MineAgent Skills provides structured, domain-aware prompts for AI assistants to handle real-world industrial tasks: optimize complex SQL queries with shaft-level filtering, parse geological structures (faults, dykes, reef horizons), predict equipment failures from telemetry data, design mining-specific KPI dashboards, and generate executive safety reports with LTIFR/TRIFR calculations. Each skill includes metadata (YAML) and detailed instructions (Markdown) following strict quality standards. Built by mining industry professionals for data engineers, geologists, maintenance teams, and operations managers working with industrial data systems.

- **Mining Operations** – Production tracking, shaft-level reporting, development metrics
- **Geological Modeling** – Structure parsing, wireframe validation, resource reconciliation
- **Equipment Maintenance** – Predictive analytics, vibration analysis, failure detection
- **Safety & Compliance** – Incident reporting, LTIFR calculation, investigation analysis
- **Industrial BI** – KPI dashboards, cost per ton, executive summaries

Each skill is a self-contained, production-ready capability that can be used with modern AI coding assistants including Claude, GitHub Copilot, Gemini CLI, Cursor, and Continue.dev.

---

## 🎯 Why MineAgent?

Industrial systems are complex. Generic AI tools fail because they:

❌ Don't understand shaft-level filtering or geological hierarchy  
❌ Mis-handle production aggregation and mining-specific joins  
❌ Miss operational edge cases that matter in the field  
❌ Generate pretty visualizations that hide critical insights  

MineAgent Skills introduce **structured, production-ready intelligence** into AI assistants. Every skill is:

✅ **Domain-Aware** – Built by people who understand mining  
✅ **Production-Tested** – Designed for real-world data volumes  
✅ **Security-Conscious** – No hardcoded credentials or unsafe patterns  
✅ **Actionable** – Outputs drive decisions, not just reports  

---

## 🧰 Available Skills

| Skill | Description | Use Cases |
|-------|-------------|-----------|
| [`sql-query-optimizer`](./skills/mineagent/sql-query-optimizer) | Optimizes large-scale SQL queries for mining databases | Slow BI dashboards, geological joins, shaft-level filtering, block model aggregation |
| [`geology-structure-parser`](./skills/mineagent/geology-structure-parser) | Processes faults, dykes, reef horizons, and wireframes | Wireframe validation, structural interpretation, fault clustering, reef continuity |
| [`predictive-maintenance-analyzer`](./skills/mineagent/predictive-maintenance-analyzer) | Analyzes equipment telemetry to detect failure patterns | Conveyor downtime, drill vibration, pump anomalies, MTBF/MTTR tracking |
| [`production-kpi-dashboard-designer`](./skills/mineagent/production-kpi-dashboard-designer) | Designs optimized KPI dashboards for mining operations | Shaft production, tonnage tracking, cost per ton, equipment utilization |
| [`safety-report-generator`](./skills/mineagent/safety-report-generator) | Transforms incident logs into executive safety reports | Board reporting, LTIFR/TRIFR calculation, trend analysis, investigation reports |

---

## 📁 Repository Structure
mineagent-skills/
│
├── README.md # You are here
├── LICENSE # MIT License
├── CONTRIBUTING.md # Contribution guidelines
├── SECURITY.md # Security policies
├── SKILL-STANDARDS.md # Quality standards for skills
│
├── skills/
│ └── mineagent/
│ ├── sql-query-optimizer/
│ │ ├── skill.yaml # Metadata, inputs, outputs, constraints
│ │ └── skill.md # Detailed instructions and logic
│ │
│ ├── geology-structure-parser/
│ │ ├── skill.yaml
│ │ └── skill.md
│ │
│ ├── predictive-maintenance-analyzer/
│ │ ├── skill.yaml
│ │ └── skill.md
│ │
│ ├── production-kpi-dashboard-designer/
│ │ ├── skill.yaml
│ │ └── skill.md
│ │
│ └── safety-report-generator/
│ ├── skill.yaml
│ └── skill.md
│
└── examples/
└── usage-examples.md # Cross-skill workflows and integrations

text

---

## 🚀 Getting Started

### Prerequisites

- A modern AI coding assistant (Claude, GitHub Copilot, Cursor, Continue.dev, etc.)
- Access to your mining data (database connections, CSV files, APIs)
- Understanding of your specific mining domain and data structures

### Quick Start

1. **Clone the repository**

```bash
git clone https://github.com/yourusername/mineagent-skills.git
cd mineagent-skills
Browse available skills

Each skill is in skills/mineagent/[skill-name]/ with two files:

skill.yaml – Metadata, inputs, outputs, and constraints

skill.md – Detailed instructions and logic for the AI

Use a skill with your AI assistant

Example prompt:

text
Using the mineagent/sql-query-optimizer skill, optimize this mining query:

SELECT * FROM production_shift WHERE shift_date > '2024-01-01'

Database: SQL Server
Table has 5M rows, indexed on shift_id only.
Review and adapt

The AI will follow the skill's logic to generate optimized queries, dashboards, or reports. Always test in staging before production deployment.

📖 Skill Format
Each skill follows a standardized format:

skill.yaml
yaml
name: skill-name
namespace: mineagent
version: 1.0.0
description: Clear purpose statement
author: MineAgent
license: MIT

required_inputs:   # What must be provided
  - input_name
  - input_type

optional_inputs:   # What helps but isn't required

tools:             # Capabilities needed
  - tool_name

constraints:       # Rules the AI must follow
  - never_do_this

output_structure:  # What the skill produces
  - output_field

tags:              # Categorization
  - mining
  - domain
skill.md
The markdown file contains:

Purpose and when to use – Clear guidance on applicability

Input validation – What data is needed and in what format

Core logic – Step-by-step instructions, algorithms, and patterns

Mining-specific examples – Real-world scenarios with inputs and outputs

Edge cases – Handling of unusual situations

Output format – Structured data the skill produces

Testing checklist – How to validate the skill's output

🔒 Security Notice
Skills are curated, not audited. Before production deployment:

✅ Review the skill logic for your specific use case

✅ Validate all database interactions for SQL injection risks

✅ Ensure no hardcoded credentials, paths, or connection strings

✅ Test in staging environment with production-like data

✅ Verify outputs don't expose sensitive information

✅ Use at your own discretion – mining operations are safety-critical

See SECURITY.md for detailed security guidelines.

📊 Quality Standards
All MineAgent skills must:

🔧 Solve real industrial problems – Not theoretical exercises

🚫 Avoid hardcoded paths or credentials – Configuration externalized

📋 Declare required tools explicitly – No hidden dependencies

🔍 Follow progressive disclosure – Simple first, complex when needed

📏 Remain under 500 lines per skill – Focused and maintainable

📝 Include example inputs and outputs – Clear expectations

⚠️ Document assumptions and limitations – Honest about constraints

🧪 Provide testing guidance – How to validate correct operation

See SKILL-STANDARDS.md for complete quality criteria.

🤝 Contributing
We welcome contributions from:

⛏️ Mining engineers – Domain expertise and real-world scenarios

📊 BI developers – Query optimization, dashboard design

🔧 Data engineers – Data pipelines, integration patterns

🛠️ Maintenance specialists – Equipment telemetry, failure analysis

⚒️ Geologists – Structural interpretation, resource modeling

🦺 Safety professionals – Incident analysis, HSE reporting

Quick Contribution Guide
Fork the repository

Create a feature branch (git checkout -b feature/amazing-skill)

Add your skill following the standards

Test with real-world scenarios

Submit a pull request

Quality over quantity. Each skill must demonstrate clear industrial value.

See CONTRIBUTING.md for detailed guidelines.

💡 Example Workflows
Production Analytics Pipeline
graph LR
    A[Raw Shift Data] --> B[SQL Query Optimizer]
    B --> C[Optimized Queries]
    C --> D[KPI Dashboard Designer]
    D --> E[Executive Dashboard]
    C --> F[Predictive Maintenance]
    F --> G[Equipment Health Reports]
Safety Reporting Workflow
graph LR
    A[Incident Logs] --> B[Safety Report Generator]
    B --> C[Monthly HSE Report]
    B --> D[Board Summary]
    B --> E[Investigation Reports]
    E --> F[Trend Analysis]
    F --> G[Corrective Actions]
Geology to Production Workflow
graph TD
    A[Drill Hole Data] --> B[Geology Structure Parser]
    B --> C[Validated Wireframes]
    C --> D[Block Model]
    D --> E[SQL Query Optimizer]
    E --> F[Production Queries]
    F --> G[KPI Dashboard]
    D --> H[Resource Report]
See examples/usage-examples.md for complete workflows.

🏭 Industries & Applications
Industry	Applications	Relevant Skills
Underground Mining	Development tracking, ground control, ventilation	SQL Optimizer, KPI Dashboard, Safety Reports
Open Pit Mining	Fleet management, slope monitoring, grade control	Predictive Maintenance, SQL Optimizer, Geology Parser
Processing Plants	Recovery tracking, downtime analysis, throughput	KPI Dashboard, Predictive Maintenance
Exploration	Drill data management, resource estimation	Geology Parser, SQL Optimizer
Contract Mining	Production verification, cost tracking	KPI Dashboard, SQL Optimizer
📚 Documentation
Skill Standards – Quality criteria for all skills

Contributing Guide – How to add new skills

Security Policies – Reporting vulnerabilities

Usage Examples – Cross-skill workflows

🗺️ Roadmap
Q2 2024
Drill & Blast Optimizer – Pattern design, fragmentation prediction

Ventilation on Demand – Airflow optimization, energy savings

Grade Control Assistant – Stope optimization, dilution reduction

Q3 2024
Haul Cycle Optimizer – Truck allocation, cycle time analysis

Inventory Reconciliation – Stockpile management, material tracking

Environmental Compliance – Water management, emissions reporting

Q4 2024
Autonomous Equipment Monitor – AHS performance analysis

Energy Management – Power consumption optimization

Community Engagement – Social impact reporting

📄 License
This project is licensed under the MIT License – see the LICENSE file for details.

🙏 Acknowledgments
Industry Partners – For real-world testing and feedback

Mining Engineers – For domain expertise and validation

Open Source Community – For tools and inspiration

📬 Contact & Support
GitHub Issues – Bug reports and feature requests

Discussions – Community Q&A and ideas

Security – SECURITY.md for responsible disclosure

Built for industrial production environments. Not generic AI toys. Not "vibe coding".

⭐ Star this repository if you find it useful for your mining operations!

text

---

## ✅ What to Do Now

1. **Copy the entire code block above**
2. **Create a new file** named `README.md` in your repository root
3. **Paste the content**
4. **Save the file**

The diagrams will render automatically when you:
- Push to GitHub
- View in VS Code with Mermaid extension
- Use any Markdown viewer that supports Mermaid

**Your README is ready to go!** 🎉
