🇬🇧 **English** | 🇫🇷 [Français](README.fr.md)

---

# Public Sector Investment Survey — Operation & Data Quality Monitoring System
## Tech Stack

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
![VS Code](https://img.shields.io/badge/VS%20Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white)

End-to-end data quality monitoring system for a 2,068-unit administrative survey — star schema design, outlier detection and Power BI dashboard.

## Project Overview

This project consists of designing and documenting a **monitoring and quality-control system for an administrative survey**, built on anonymized data, using a methodology directly inspired by professional survey statistics practices (field monitoring, consistency checks, data-quality indices). The use case is an exhaustive survey on public investment by Moroccan government administrations, covering 2,068 administrative units (local authorities, public administrative establishments, and local development companies) across 12 regions, mobilizing 48 field enumerators and 12 regional supervisors over a 7-month fieldwork period. The goal is not to produce a simple dashboard, but to reconstruct, end to end, the role of a data analyst tasked with designing this system — from requirements gathering to documentation governance, through data modeling, statistical quality processing, and decision-oriented reporting.

The approach is structured into eight successive phases, each producing a concrete deliverable that feeds into the next. <br>**1-Requirements Gathering:** translates the skills to be demonstrated into precise, traceable functional requirements.<br> **2-Data Modeling:** builds, from these requirements, a normalized star schema compatible with both SQLite and Azure SQL Database.<br> **3-Technical Architecture:** defines the infrastructure for data collection, storage, and ingestion.<br> **4-ETL and Quality Control:** implements validation rules, outlier detection via IQR and Z-score methods, and the calculation of the composite quality index.<br> **5-Monitoring Indicators:** formalizes coverage, velocity, and quality KPIs.<br> **6-Power BI Visualization:** renders the full system as an interactive, three-tier dashboard.<br> **7-Automation and Alerts:** introduces notification mechanisms and a per-unit risk score.<br> **8-Documentation and Governance:** consolidates the data dictionary, the methodological decision log, and the project's technical documentation.
