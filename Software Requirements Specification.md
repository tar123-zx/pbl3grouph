---
title: Software Requirements Specification

---

# Software Requirements Specification
## For SafePath

Version 0.1  
Prepared by Group H    
May 20, 2026

## Table of Contents
<!-- TOC -->
* [1. Introduction](#1-introduction)
    * [1.1 Document Purpose](#11-document-purpose)
    * [1.2 Product Scope](#12-product-scope)
    * [1.3 Definitions, Acronyms, and Abbreviations](#13-definitions-acronyms-and-abbreviations)
    * [1.4 References](#14-references)
    * [1.5 Document Overview](#15-document-overview)
* [2. Product Overview](#2-product-overview)
    * [2.1 Product Perspective](#21-product-perspective)
    * [2.2 Product Functions](#22-product-functions)
    * [2.3 Product Constraints](#23-product-constraints)
    * [2.4 User Characteristics](#24-user-characteristics)
    * [2.5 Assumptions and Dependencies](#25-assumptions-and-dependencies)
    * [2.6 Apportioning of Requirements](#26-apportioning-of-requirements)
* [3. Requirements](#3-requirements)
    * [3.1 External Interfaces](#31-external-interfaces)
    * [3.2 Functional](#32-functional)
    * [3.3 Quality of Service](#33-quality-of-service)
    * [3.4 Compliance](#34-compliance)
    * [3.5 Design and Implementation](#35-design-and-implementation)
    * [3.6 AI/ML](#36-aiml)
* [4. Verification](#4-verification)
* [5. Appendixes](#5-appendixes)
<!-- TOC -->

## Revision History

| Name | Date | Reason For Changes | Version |
|------|------|--------------------|---------|
|Initial      |5.20.26      |                    |0.1         |
|      |      |                    |         |

## 1. Introduction
This Software Requirements Specification (SRS) defines the requirements for the safe walking route application developed for the PBL3 Creative Design project. It outlines the product scope, target audiences, and the specific functional and non-functional requirements necessary to implement the route comparison system. Details regarding specific constraints and models are referenced in their respective sections below.

### 1.1 Document Purpose
This Software Requirements Specification (SRS) aims at defining the functional and non-functional requirements of the SafePath system, a pedestrian navigation platform that emphasises personal safety during route generation. This document describes what the system needs to do (safety evaluation, route optimisation, user reporting, data processing requirements) without defining the final implementation or software architecture.

This document is aimed at the Group H development team, project stakeholders, testers, and future maintainers. The SRS will serve as a reference during the system design, implementation, testing, validation and future enhancements phases of the software development lifecycle. It can also be used in conjunction with related documents such as project proposal, system architecture design and development roadmap.

### 1.2 Product Scope
SafePath (Version 0.1) is a web-based route-planning application that generates walking directions optimised for safety rather than travel time. It consumes streetlight location data, crime incident records, and user-contributed safety reports to score road segments on perceived and objective safety. The system outputs directions with a safety score breakdown, enabling users to make informed decisions about their route. Key capabilities include safety-weighted routing (based on streetlight data, user reports, and calculating a localized safety score for street segments using a Kernel Density Estimation (KDE) model applied to historical crime data), transparent safety scoring, crime-heatmap overlays, and crowd-sourced incident reporting. This SRS covers the public-facing web application, routing engine, and data ingestion pipeline. Excluded are native mobile applications, real-time location tracking, and city-maintenance integration, which are deferred to future releases.


### 1.3 Definitions, Acronyms, and Abbreviations
To ensure consistent interpretation among technical developers and non-technical stakeholders, the following glossary defines specialized engineering, geospatial, and business domains utilized throughout this document:


| Term | Definition                                                                                                                   |
|------|------------------------------------------------------------------------------------------------------------------------------|
| API  | Application Programming Interface - Application Programming Interface – A standardized set of protocols and definitions allowing distinct software applications to securely communicate and exchange data.     |
| CSV  | Comma-Separated Values – A plain-text file format utilized to store tabular data structured neatly in rows and columns. |
| Cost Surface   | A continuous spatial grid or vector line network where each distinct segment holds a numerical weight ("cost") representing localized environmental risk or resistance, rather than linear distance alone.                     |
| ETL   | Extract, Transform, Load – A continuous data engineering pipeline process that retrieves raw data from source locations, alters it to fit operational profiles, and writes it to a target database.                     |
| FSD/SRS   | Functional Specification Document / Software Requirements Specification – Detailed technical documentation outlining system behaviors, functional requirements, and criteria for software verification.                     |
| GIS   | Geographic Information System – A specialized framework designed to capture, store, manipulate, analyze, manage, and present diverse forms of spatial or geographical data.                     |
| KDE   | Kernel Density Estimation – A non-parametric mathematical smoothing technique used in spatial statistics to transform discrete coordinate points (e.g., individual crime locations) into a continuous probability density surface.                     |
| Luminance Buffer   | A computed 15–20 meter radial vector boundary extending from a structural streetlight coordinate point, indicating a statistically valid "safe zone" of active illumination.                     |
| RTM   | Risk Terrain Modeling – An analytical framework that diagnoses the spatial landscape of an urban environment to evaluate how specific environmental layout features contribute to the latent risk of criminal events.                    |
| Temporal Decay   | A mathematical function that systematically reduces the numerical weight or significance of historical data points over time, ensuring older incidents have less impact on current cost calculations than recent events.                    |
| WGS84   | World Geodetic System 1984 – The standard geodetic reference coordinate system used globally for spatial mapping and GPS calculations.                    |





### 1.4 References

| Reference | Author/Owner | Version | Date | Location | Type |
|-----------|-------------|---------|------|----------|------|
| Ambient lighting, use of outdoor spaces and perceptions of public safety | Nanda et al. | N/A | 2023 | pmc.ncbi.nlm.nih.gov/articles/PMC8038535/ | Informative |
| Light distribution in dynamic street lighting: effects on perceived safety | Rea et al. | N/A | 2013 | sciencedirect.com | Informative |
| A Mobile Information System Based on Crowd-Sensed and Official Crime Data for Finding Safe Routes | Ceja et al. | N/A | 2016 | onlinelibrary.wiley.com/doi/pdf/10.1155/2016/8068209 | Informative |
| GraphHopper Routing Engine | GraphHopper GmbH | 9.x | 2025 | github.com/graphhopper/graphhopper | Normative |
| openrouteservice API | GIScience Heidelberg | 7.x | 2025 | github.com/GIScience/openrouteservice | Normative |
| Philadelphia Crime Incidents Dataset | OpenDataPhilly | Annual | 2006–2026 | opendataphilly.org | Normative |
| Philadelphia Street Poles Dataset | OpenDataPhilly | Current | 2026 | opendataphilly.org | Normative |
| Urban Navigation Beyond Shortest Route: The Case of Safe Paths | Galbrun, E., Pelechrinis, K., Terzi, E. | N/A | 2015 | [Information Systems. 57.](https://doi.org/10.1016/j.is.2015.10.005) | Academic Paper |
| A Crowd-Sourced Adaptive Safe Navigation for Smart Cities | Goel, N., Sharma, R., Nikhil, N., Mahanoor, S. D., Saini, M. | N/A | 2017 | [2017 IEEE International Symposium on Multimedia (ISM), Taichung, Taiwan](https://doi.ieeecomputersociety.org/10.1109/ISM.2017.77) | Academic Paper |
| Crime Incident Reports (August 2015 To Date) | City of Boston | Current | 2015-Present | https://data.boston.gov/dataset/crime-incident-reports-august-2015-to-date-source-new-system | Dataset |
| Streetlight Locations | City of Boston | Legacy | 2023 | https://data.boston.gov/dataset/streetlight-locations | Dataset |

### 1.5 Document Overview
This document is organized systematically into five primary chapters to guide stakeholders through the engineering lifecycle of SafePath:

* Section 1: Introduction defines the purpose, scope, authoritative terminology, and foundational constraints of the project.

* Section 2: Product Overview provides context and operational dependencies.

* Section 3: Requirements delivers detailed and testable technical system criteria divided into User Interfaces (UI), External System Interfaces, Functional Requirements (FR), and Non-Functional Quality of Service (QoS) constraints.

* Section 4: Verification outlines the system evaluation matrix, mapping each explicit requirement ID directly to standard testing, analysis, inspection, or demonstration scripts.

* Section 5: Appendix contains survey and interview results and a glossary.

Document updates, scope alterations are cataloged in the Revision History located at the top of this document.


### 1.6 Risks and Assumptions

The feasibility of SafePath relies on the mitigation of the following known project assumptions and computational risks:

* Assumption 1: Spatial Completeness of Municipal Data
The system assumes that the open-source GIS repositories provided by target municipalities are structurally complete, accurate, updated regularly, and contain precise absolute longitude/latitude coordinates for both streetlight assets and public crime data.

* Risk 1: High Latency in High-Density Urban Routing Networks

    * Description: Real-time execution of a dynamic, multi-factor $A^*$ pathfinding algorithm across extensive urban street networks can cause high computational server lag or browser timeouts under heavy user traffic.
    * Mitigation: The weights will be precomputed and stored to minimize computation time.

* Risk 2: Reporting Lags and Crime API Stale Rates

    * Description: Public crime databases managed by municipalities often suffer from reporting delays which diminishes the real-time accuracy of temporal crime data.
    * Mitigation: The system will utilize a hybrid weighting mechanism that relies on crowdsourced live incident reports. 

## 2. Product Overview
### 2.1 Product Perspective


SafePath is a new product developed as part of a university project (PBL 3, Semester 3) by Group H. It does not replace an existing system but addresses a gap in current navigation services: no major provider (Google Maps, Apple Maps, etc.) offers pedestrian routing that accounts for street lighting, crime risk, and environmental hazards. The system depends on publicly available municipal open-data portals for streetlight and crime-incident data, and on open-source routing engines (GraphHopper or openrouteservice) for path computation. 
### 2.2 Product Functions
The main purpose of the app is to find and offer safer alternative routes. It builds a street-by-street safe path using maps, crime stats, and live user reports.


The major functional capabilities provided to users and external systems are categorized into the following primary areas:

**Safety-Weighted Route Generation**
- Computes walking paths between a source and destination by minimizing a composite safety cost algorithm.
- Dynamically switches routing logic during nighttime hours to prioritize continuous high-visibility street segments.


**Streetlight Infrastructure Overlay**
* Extracts and displays streetlight locations onto the user interface map view using municipal datasets and external Geographic Information System (GIS) data layers.
Directly injects the presence or absence of "lit" tags into the real-time routing engine's safety scoring model.


**Spatio-Temporal Crime Heatmap Overlay**
- Visualizes localized historical crime incident densities across specific street segments and alleyways.


**Route Safety Score Breakdown**
- Provides safety ratings for each generated route option.


**Crowdsourced Incident Reporting**
- Enables users to actively submit geo-tagged incident reports directly from their current location.
- Supports reporting anomalies such as broken streetlights, environmental hazards, and suspicious or threatening activity.


**Personalized Risk Profile Customization**
- The system will offer predefined routing profiles. Users can either use the default balanced route or select an alternative profile that prioritizes specific safety factors (e.g., environmental lighting or historical data).


---

### 2.3 Product Constraints

- The system shall use open-source routing software for path computation to avoid licensing costs 
- All municipal data ingested must be from openly licensed or public-domain sources
- The prototype must target a single pilot city with available streetlight and crime data
- The system must not require user accounts or persistent location tracking to function
- The web UI must be accessible via modern browsers
- The prototype must operate with offline data snapshots rather than real-time API connections to municipal data sources
- AI/ML components (if any) must be limited to crime risk prediction using publicly available datasets

### 2.4 User Characteristics
The SafePath system is built for multiple user classes with different goals, access levels and interaction patterns.

* Primary Users – Pedestrian Users

The primary users are pedestrians looking for safer walking routes. These users will interact with the system using mobile or web interfaces. Their main task is to safely traverse urban areas and avoid poorly lit or high risk areas. Users can also help the platform by submitting reports about anomalies such as unsafe areas, broken streetlights, suspicious activity or temporary hazards. They’re expected to use the system regularly and help improve route accuracy with crowdsourced feedback.

* Administrators

Administrators oversee user-generated content, examine inappropriate reports, and protect the integrity of the system. This user class has advanced access privileges, including content moderation and system management capabilities.

* City Planners and Researchers

City authorities, planners or researchers can use SafePath to analyse urban safety trends and weaknesses in infrastructure. They can identify high-risk “dark zones” and use the data insights from the system to improve public safety planning.

* Localisation and Accessibility Issues

The system needs to be able to provide responsive web access. User interfaces should be designed to be accessible, which includes features like readable fonts, clear navigation, colour contrast, and easy-to-follow interaction flows. Future versions of the system could have multilingual support and localisation capabilities to cater to the needs of a diverse urban population.



### 2.5 Assumptions and Dependencies


- **Assumptions**: (1) the selected pilot city continues to publish streetlight and crime data under open licenses; (2) open-source routing software remains available and maintained; (3) target users have internet-connected devices with modern browsers; (4) users find value in safety scoring and are willing to trade some trip distance for perceived safety
- **Dependencies**: (1) open-source library for routing; (2) municipal open-data portals for streetlight and crime data; (3) map tile provider for base map rendering (e.g. OpenStreetMap); (4) survey and interview findings as input evidence for requirement prioritisation
- **Impact if false**: unavailable municipal data would require switching pilot city or acquiring commercial satellite imagery ($10–$33/km² with minimum order quantities); unavailable routing library would require developing an in-house routing engine, substantially increasing scope

### 2.6 Apportioning of Requirements

Requirements are allocated across three primary subsystems and two release increments. The prototype (v0.1) targets the pilot-city demonstration deadline; deferred features are tracked for post-prototype releases.

#### 2.6.1 Subsystem Allocation

| Requirement ID(s) | Subsystem | Release | Notes |
|---|---|---|---|
| REQ-INT-UI-001 through REQ-INT-UI-009 | Web Frontend | v0.1 | Map UI, overlays, score panel, routing profiles |
| REQ-INT-HW-001 through REQ-INT-HW-003 | Web Frontend | v0.1 | Cross-device compatibility |
| REQ-INT-SW-001 | Routing Engine | v0.1 | open-source routing API integration with custom safety weights |
| REQ-INT-SW-002, REQ-INT-SW-003 | Data Pipeline | v0.1 | CSV/GeoJSON ingestion of municipal streetlight and crime data |
| REQ-INT-SW-004 | Web Frontend | v0.1 | Base map tile rendering (raster or vector) |
| REQ-INT-SW-005 | Backend | v0.1 | No public API exposed in prototype |
| REQ-FUNC-001 through REQ-FUNC-004 | Routing Engine + Web Frontend | v0.1 | Route computation (Frontend call via internal API consumed by the UI) |
| REQ-FUNC-005, REQ-FUNC-006 | Web Frontend + Data Pipeline | v0.1 | Heatmap rendering (Frontend) depends on preprocessing (Data Pipeline) |
| REQ-FUNC-007, REQ-FUNC-008 | Web Frontend + Reporting Service | v0.1 | Score transparency panel; report submission and map display |
| REQ-PERF-001 | Routing Engine | v0.1 | Server-side route computation latency |
| REQ-PERF-002 | Web Frontend | v0.1 | Client-side map loading time |
| REQ-SEC-001 | Web Frontend + Backend | v0.1 | Location consent enforcement |
| REQ-REL-001 | Routing Engine | v0.1 | Graceful degradation on missing data |
| REQ-AVAIL-001 | All | v0.1 | Demonstration availability |
| REQ-OBS-001 | Backend | v0.1 | Error logging infrastructure |
| REQ-COMP-001, REQ-COMP-002 | Web Frontend | v0.1 | Attribution and licensing notices in UI |
| REQ-INST-001 | All | v0.1 | Modular deployment packaging |
| REQ-BUILD-001 | Documentation | v0.1 | Build and run instructions |
| REQ-DIST-001 | Data Pipeline | v0.1 | Single-city data packaging |
| REQ-MAINT-001 | Data Pipeline | v0.1 | Data refresh documentation |
| REQ-REUSE-001 | Data Pipeline | v0.1 | City-config separation from code |
| REQ-PORT-001 | All | v0.1 | Cross-machine consistency |
| REQ-COST-001 | All | v0.1 | Zero-cost dependency audit |
| REQ-DEAD-001 | All | v0.1 | Semester deadline constraint |
| REQ-POC-001 | Routing Engine | v0.1 | Pre-implementation feasibility check |
| REQ-CM-001 | All | v0.1 | Version-controlled data and scripts |
| REQ-ML-001 through REQ-ML-006 | AI/ML Subsystem | v0.1 | Crime prediction model: model card, training data provenance, abstention, ethics documentation, and disclosure |

#### 2.6.2 Release Roadmap

| Increment | Focus | Key Deliverables | Target Date |
|---|---|---|---|
| v0.1 Prototype | Core routing with safety weighting, heatmap overlay, crowdsourced reporting, score transparency | All REQ-FUNC-001 through REQ-FUNC-008, full UI, single-city data pipeline | End of semester | REQ-ML-001 through REQ-ML-006
| v1.0+ | Multi-city support, native mobile apps, real-time tracking, city-maintenance integration | | Post-prototype

#### 2.6.3 Unknown or Unconfirmed Allocations

- **Real-time tracking and turn-by-turn navigation**: Not allocated in v0.1; feasibility and demand to be re-evaluated for v1.0+ based on survey and interview feedback indicating pre-trip planning preference.
- **Native mobile applications**: Deferred; v0.1 targets responsive web only.
- **City-maintenance dashboard integration**: Deferred; requires stakeholder engagement beyond current project scope.

## 3. Requirements

### 3.1 External Interfaces
This section describes the external interfaces used by the SafePath system in support of navigation, geospatial analysis, crime data integration and community based reporting. These interfaces define how the system communicates with external services, datasets, users and third party platforms. The interfaces defined in this document are the input/output specifications required for system implementation, integration, and testing throughout the software development lifecycle.

#### 3.1.1 User Interfaces

- REQ-INT-UI-001: The system shall provide a web-based map interface with a start-point input, destination input, and a "Find Safe Route" button
- REQ-INT-UI-002: The system shall display the computed route as a coloured polyline on the map, with colour indicating per-segment safety level (green = safe, yellow = moderate, red = unsafe)
- REQ-INT-UI-003: The system shall provide a toggle to overlay streetlight locations as point markers on the map
- REQ-INT-UI-004: The system shall provide a toggle to overlay a crime heatmap layer
- REQ-INT-UI-005: The system shall display a safety score breakdown panel showing lighting, crime, anomalies, and overall scores for the selected route
- REQ-INT-UI-006: The system shall offer predefined routing profiles, allowing users to select a default balanced route or an alternative profile that prioritizes specific safety factors (e.g., environmental lighting or historical data).
- REQ-INT-UI-007: The system shall display an option to open a tooltip or info box when a route is selected, explaining factors affecting its safety score
- REQ-INT-UI-008: The system shall render correctly on screens at least 360 px wide.
- REQ-INT-UI-009: The system shall prompt for location access but not send location data without explicit user action

#### 3.1.2 Hardware Interfaces

- REQ-INT-HW-001: The system shall operate on any device capable of running a modern web browser
- REQ-INT-HW-002: The system shall function with touch input and mouse/pointer input equivalently
- REQ-INT-HW-003: The system shall not require any specialised hardware peripherals

#### 3.1.3 Software Interfaces

- REQ-INT-SW-001: The system shall use an open source API for route computation, passing custom edge weights derived from safety data
- REQ-INT-SW-002: The system shall consume streetlight location data from a processed CSV/GeoJSON file ingested from the municipal open-data portal
- REQ-INT-SW-003: The system shall consume crime incident data from a processed CSV/GeoJSON file ingested from the municipal open-data portal
- REQ-INT-SW-004: The system shall use raster or vector tiles for base map display
- REQ-INT-SW-005: The system shall expose no public API in the prototype release

### 3.2 Functional

- ID: REQ-FUNC-001
- Title: Safety-weighted route computation
- Statement: The system shall compute a walking route from a user-specified origin to a destination, by calculating a safety weight and attributing a composite safety cost, in addition to distance and time
- Rationale: Core product differentiator — existing navigation apps optimise for time or distance, not safety
- Acceptance Criteria: Given the same origin and destination, the safe route must differ from the fastest route when the fastest route passes through areas with poor lighting, high crime density, or anomalies
- Verification Method: Demonstration

- ID: REQ-FUNC-002
- Title: Safety cost model
- Statement: The safety cost shall be computed as a weighted sum of (a) lighting availability, (b) historical crime density, (c) anomalies
- Rationale: Multi-factor scoring reflects interview findings that safety concerns span crime, lighting, anomalies, and social anxieties
- Acceptance Criteria: Modifying any one input factor (e.g. removing lighting data) must observably change the cost assigned to affected segments
- Verification Method: Test

- ID: REQ-FUNC-003

- Title: Routing Profile Selection

- Statement: The system shall offer predefined routing profiles, allowing users to select a default balanced route or an alternative profile that prioritizes specific safety factors (e.g., environmental lighting or historical data).

- Rationale: User safety priorities vary. Predefined profiles allow for route customizations.

- Acceptance Criteria: Selecting an alternative profile must calculate and display a route that mathematically prioritizes the chosen factor compared to the default balanced route.

- Verification Method: Test

- ID: REQ-FUNC-004
- Title: Comparison with fastest route
- Statement: The system shall display alongside the safe route a "fastest route" for reference and report the estimated time difference
- Rationale: Users need to make an informed trade-off between safety and travel time
- Acceptance Criteria: The fastest route and its estimated duration must be shown; the time difference must be displayed in a clear format (e.g. "+5 min")
- Verification Method: Demonstration

- ID: REQ-FUNC-005
- Title: Crime heatmap layer
- Statement: The system shall render a density heatmap of historical crime incidents on the map
- Rationale: Crime data provides an objective safety dimension that complements subjective lighting and crowd reports
- Acceptance Criteria: The heatmap must render at the correct geographic locations, toggling the layer on and off must show and hide the overlay
- Verification Method: Demonstration

- ID: REQ-FUNC-006
- Title: Safety score breakdown
- Statement: The system shall display a breakdown of the computed safety score into sub-scores for lighting, crime, hazards, and crowd reports for the selected route
- Rationale: Users want transparency — interview participants explicitly stated they want to know why a route is considered safe or unsafe
- Acceptance Criteria: The breakdown must show category scores and an overall score; scores must be updated when the route is recomputed with different weights
- Verification Method: Test

- ID: REQ-FUNC-007
- Title:  transparency
- Statement: The system shall display, following calculation of the path, that path's safety score
- Rationale: Transparency was a recurring theme across all interviews; users want to understand the reasoning behind route recommendations
- Acceptance Criteria: Clicking any route must show a tooltip or info panel
- Verification Method: Demonstration

- ID: REQ-FUNC-008
- Title: Crowd-sourced incident report submission
- Statement: The system shall allow any user to submit a safety incident report specifying location, category (e.g. broken streetlight, suspicious activity, physical hazard), description, and timestamp
- Rationale: Crowd-sourced data fills gaps not covered by municipal datasets and captures real-time conditions
- Acceptance Criteria: A submitted report must appear on the map within the same session; the report location must be near the user's clicked point
- Verification Method: Test

### 3.3 Quality of Service

#### 3.3.1 Performance

- ID: REQ-PERF-001
- Title: Route computation latency
- Statement: The system shall compute a safe walking route within 10 seconds for any origin-destination pair within the pilot city boundary
- Rationale: Users expect interactive response times; longer waits degrade UX
- Acceptance Criteria: 90th percentile route computation time must be ≤10 s
- Verification Method: Test

- ID: REQ-PERF-002
- Title: Map tile loading
- Statement: The map interface shall load and render within 10 seconds on a representative device
- Rationale: First-impression performance affects user adoption and retention
- Acceptance Criteria: Time to display map interface must be <10s on three trials
- Verification Method: Test

#### 3.3.2 Security

- ID: REQ-SEC-001
- Title: Location data privacy
- Statement: The system shall not store, transmit, or log the user's device geolocation without explicit affirmative consent per interaction
- Rationale: Survey data indicates significant privacy concern around location sharing
- Acceptance Criteria: No location data may be sent to the server unless the user clicks a "Share Location" button; a privacy notice must appear before any location access is requested
- Verification Method: Inspection

#### 3.3.3 Reliability

- ID: REQ-REL-001
- Title: Graceful degradation on data absence
- Statement: If lighting data or crime data for a queried route segment is unavailable, the system shall compute the route using the remaining available factors and inform the user that some data is missing
- Rationale: Incomplete municipal data should not prevent the system from functioning; transparency about data gaps maintains user trust
- Acceptance Criteria: Removing the crime data file from the server must still produce a route with a warning banner: "Crime data unavailable for some segments"
- Verification Method: Test

#### 3.3.4 Availability

- ID: REQ-AVAIL-001
- Title: Prototype availability
- Statement: The prototype shall be available for demonstration during scheduled class sessions and assessment periods
- Rationale: Course requirement; the system must be demonstrable on demand
- Acceptance Criteria: The system must load and respond to a route query
- Verification Method: Demonstration

#### 3.3.5 Observability

- ID: REQ-OBS-001

- Title: Error and Crash Logging

- Statement: The system shall log all backend processing errors, route calculation failures, including a timestamp and the specific error message.

- Rationale: Developers need error logs to troubleshoot and fix bugs when the system breaks.

- Acceptance Criteria: Forcing a system error or route failure must generate a log entry containing the time of the failure and the corresponding error details.

- Verification Method: Test / Inspection

### 3.4 Compliance

- ID: REQ-COMP-001
- Title: Open-data license attribution
- Statement: The system shall display attribution for each municipal dataset used (streetlights, crime), including the data source name, license type, and a link to the original data
- Rationale: Required by open-data licenses of the target cities
- Acceptance Criteria: Attribution text must appear in the map UI or an "About" panel; each attribution must link to the original dataset page
- Verification Method: Inspection

- ID: REQ-COMP-002
- Title: Open-source license compliance
- Statement: The system shall comply with the licensing terms of all open-source components used by including appropriate notices
- Rationale: Legal obligation when redistributing or serving open-source software
- Acceptance Criteria: A "Licenses" page or panel must list each dependency, its license, and a notice
- Verification Method: Inspection

### 3.5 Design and Implementation

#### 3.5.1 Installation

- ID: REQ-INST-001
- Title: Modular System Deployment
- Statement: The application components shall be packaged to run as an integrated, self-hosted system.
- Rationale: Simplifies the demonstration setup and ensures all separate software modules can be launched together reliably.
- Acceptance Criteria: Executing the system initialization command locally launches all system components simultaneously, rendering the web application accessible via a standard network port.
- Verification Method: Demonstration

#### 3.5.2 Build and Delivery

- ID: REQ-BUILD-001

- Title: Build and Deployment Documentation

- Statement: The project repository shall include documentation providing step-by-step instructions to configure, build, and run the application manually.

- Rationale: Ensures course supervisors and other developers can replicate and assess the system environment without requiring a fully automated build script.

- Acceptance Criteria: A user following the provided written instructions from a clean environment must be able to successfully start the system.

- Verification Method: Demonstration

#### 3.5.3 Distribution

- ID: REQ-DIST-001
- Title: Single-city deployment
- Statement: The prototype shall be deployed and tested for exactly one pilot city
- Rationale: Scope constraint for the prototype phase; multi-city support is deferred
- Acceptance Criteria: The deployment must contain data for the chosen pilot city only; attempting to route outside the city boundary must show an appropriate message
- Verification Method: Inspection

#### 3.5.4 Maintainability

- ID: REQ-MAINT-001
- Title: Data refresh procedure
- Statement: The system shall document a procedure for refreshing municipal data (streetlights, crime)
- Rationale: Municipal datasets are updated periodically; the system must remain current
- Acceptance Criteria: Following the documented procedure must produce updated scores and overlays within 30 minutes
- Verification Method: Demonstration

#### 3.5.5 Reusability

- ID: REQ-REUSE-001
- Title: City configuration separation
- Statement: City-specific data (streetlight files, crime files, boundary polygons) shall be stored in a separate configuration directory, not mixed with application code
- Rationale: Enables future reuse of the application code for additional cities without code changes
- Acceptance Criteria: Replacing the city configuration directory contents with data for a different city must not require application code modifications
- Verification Method: Inspection

#### 3.5.6 Portability

- ID: REQ-PORT-001
- Title: Local Developer Environment Standardization
- Statement: The system shall run consistently across development machines.
- Rationale: Ensures the application runs smoothly during the live project demonstration.
- Acceptance Criteria: The application can be launched on a team member's local computer
- Verification Method: Demonstration

#### 3.5.7 Cost

- ID: REQ-COST-001
- Title: Zero licensing cost
- Statement: The system shall use only free and open-source software and freely licensed municipal data; no paid API keys or software licenses shall be required for the prototype
- Rationale: The project has no budget; all dependencies must be zero-cost
- Acceptance Criteria: A bill of materials listing each dependency and confirming its free/open-source license must be maintained
- Verification Method: Inspection

#### 3.5.8 Deadline

- ID: REQ-DEAD-001
- Title: Prototype delivery
- Statement: A working prototype demonstrating functional requirements REQ-FUNC-001 through REQ-FUNC-008 must be delivered by the end of the semester
- Rationale: Course assessment deadline
- Acceptance Criteria: The system must be demonstrable and all listed requirements verified
- Verification Method: Demonstration

#### 3.5.9 Proof of Concept

- ID: REQ-POC-001
- Title: Routing engine feasibility POC
- Statement: Before full implementation, a proof of concept must confirm that the chosen routing engine can accept custom edge weights derived from lighting and crime data and produce different routes than the default shortest-path
- Rationale: Confirms the core technical assumption before investing in full UI and data pipeline
- Acceptance Criteria: A test script to prove that changing the safety or lighting scores on a street forces the navigation engine to pick a different path between the same origin-destination.
- Verification Method: Demonstration

#### 3.5.10 Change Management

- ID: REQ-CM-001
- Title: Version-controlled data
- Statement: All data-processing scripts and configuration files shall be stored on Github
- Rationale: Enables traceability and rollback of data changes
- Acceptance Criteria: The Github repository must contain all necessary files; and must show history of data changes
- Verification Method: Inspection

### 3.6 AI/ML

#### 3.6.1 Model Specification

- ID: REQ-ML-001
- Title: Crime risk prediction model
- Statement: The system may incorporate a spatio-temporal crime risk prediction model that estimates future crime probability for regions based on historical incident patterns.
- Rationale: Adds predictive capability beyond retrospective crime heatmaps; research on suitable models (deep reinforcement learning, Bayesian spatio-temporal models)
- Acceptance Criteria: The system shall report standard evaluation metrics (e.g., accuracy, precision, recall, F1-score, or AUC) on the test dataset.
- Verification Method: Analysis

#### 3.6.2 Data Management

- ID: REQ-ML-003
- Title: Training data provenance
- Statement: If a crime prediction model is developed, its training dataset shall be documented
- Rationale: Reproducibility and auditability of ML-based features
- Acceptance Criteria: Source attribution must accompany any model delivery
- Verification Method: Inspection

#### 3.6.3 Guardrails

ID: REQ-ML-004
Title: Prediction abstention
Statement: The system may optionally use a crime prediction model. If a model is not used, the system shall rely solely on historical crime density. 
Rationale: Allows operation without a prediction model
Acceptance Criteria: N/A (conditional on model use)
Verification Method: Test

#### 3.6.4 Ethics

- ID: REQ-ML-005
- Title: Model card documentation
- Statement: If a crime prediction model is developed, a form of documentation shall be produced documenting intended use, performance metrics across geographic areas, known limitations, and fairness evaluation
- Rationale: Transparency and accountability for ML components
- Acceptance Criteria: N/A (deferred with model)
- Verification Method: Inspection

#### 3.6.5 Human-in-the-Loop

- ID: REQ-ML-006
- Title: Route override disclosure
- Statement: Any route that differs substantially from the user's expected path due to predictive model outputs (not historical data) shall be flagged to the user with an explanation
- Rationale: Users value transparency; they should know when predictions rather than recorded data are driving recommendations
- Acceptance Criteria: N/A (deferred with model)
- Verification Method: Demonstration

## 4. Verification

| Requirement ID | Verification Method | Test/Artifact Link | Status | Evidence |
|---|---|---|---|---|
| REQ-INT-UI-001 | Demonstration | prototype UI | Planned | |
| REQ-INT-UI-002 | Demonstration | prototype UI | Planned | |
| REQ-INT-UI-003 | Demonstration | prototype UI | Planned | |
| REQ-INT-UI-004 | Demonstration | prototype UI | Planned | |
| REQ-INT-UI-005 | Demonstration | prototype UI | Planned | |
| REQ-INT-UI-006 | Demonstration | prototype UI | Planned | |
| REQ-INT-UI-007 | Demonstration | prototype UI | Planned | |
| REQ-INT-UI-008 | Test | Responsive design test suite | Planned | |
| REQ-INT-UI-009 | Inspection | Code review / privacy notice in UI | Planned | |
| REQ-INT-HW-001 | Demonstration | Cross-device manual check | Planned | |
| REQ-INT-HW-002 | Demonstration | Touch and mouse input test | Planned | |
| REQ-INT-HW-003 | Inspection | Hardware requirements doc | Planned | |
| REQ-INT-SW-001 | Demonstration | POC script confirming custom weight acceptance (REQ-POC-001) | Planned | |
| REQ-INT-SW-002 | Inspection | Data pipeline configuration | Planned | |
| REQ-INT-SW-003 | Inspection | Data pipeline configuration | Planned | |
| REQ-INT-SW-004 | Demonstration | Map rendering in UI | Planned | |
| REQ-INT-SW-005 | Inspection | API surface audit | Planned | |
| REQ-FUNC-001 | Demonstration | Route comparison: safe vs fastest | Planned | |
| REQ-FUNC-002 | Test | Unit test for safety cost function | Planned | |
| REQ-FUNC-003 | Test | Profile selection integration test | Planned | |
| REQ-FUNC-004 | Demonstration | Side-by-side route display | Planned | |
| REQ-FUNC-005 | Demonstration | Heatmap toggle in UI | Planned | |
| REQ-FUNC-006 | Test | Score breakdown unit test | Planned | |
| REQ-FUNC-007 | Demonstration | Tooltip/info panel on route click | Planned | |
| REQ-FUNC-008 | Test | Report submission + map display integration test | Planned | |
| REQ-PERF-001 | Test | Route computation timing benchmark | Planned | |
| REQ-PERF-002 | Test | Map load performance test | Planned | |
| REQ-SEC-001 | Inspection | Privacy consent flow audit | Planned | |
| REQ-REL-001 | Test | Data-absence integration test | Planned | |
| REQ-AVAIL-001 | Demonstration | Class-session demo readiness | Planned | |
| REQ-OBS-001 | Test / Inspection | Error log generation test | Planned | |
| REQ-COMP-001 | Inspection | Attribution panel in UI | Planned | |
| REQ-COMP-002 | Inspection | Licenses page/dialog | Planned | |
| REQ-INST-001 | Demonstration | Single-command startup | Planned | |
| REQ-BUILD-001 | Demonstration | Clean-environment setup walkthrough | Planned | |
| REQ-DIST-001 | Inspection | City data boundary check | Planned | |
| REQ-MAINT-001 | Demonstration | Data refresh procedure walkthrough | Planned | |
| REQ-REUSE-001 | Inspection | Directory structure audit | Planned | |
| REQ-PORT-001 | Demonstration | Launch on team member machine | Planned | |
| REQ-COST-001 | Inspection | Bill of materials review | Planned | |
| REQ-DEAD-001 | Demonstration | End-of-semester demo | Planned | |
| REQ-POC-001 | Demonstration | Custom-weight routing POC script | Planned | |
| REQ-CM-001 | Inspection | GitHub history audit | Planned | |
| REQ-ML-001 | Analysis | Model evaluation report | Planned | |
| REQ-ML-003 | Inspection | Training data provenance doc | Planned | |
| REQ-ML-004 | Test | Conditional test (if model used) | Planned | |
| REQ-ML-005 | Inspection | Model card | Planned | |
| REQ-ML-006 | Demonstration | Override disclosure in UI | Planned | |


## 5. Appendixes

### A. Glossary

| Term | Definition |
|------|-----------|
| A\* | Graph traversal pathfinding algorithm used to compute the lowest-cost (safest) route across the street network |
| Composite safety cost | Weighted sum of lighting, crime, hazard, and crowd-report sub-costs assigned to each street segment |
| Crime density | Number of reported crime incidents per unit area, optionally kernel-density estimated for heatmap rendering |
| Crowd report | User-submitted observation of a safety-relevant condition at a specific location |
| Edge weight | Numerical cost assigned to a graph edge (street segment) used by the routing engine to determine optimal path |
| GeoJSON | Open standard geospatial data interchange format based on JSON, used for storing streetlight and crime data |
| KDE | Kernel Density Estimation — non-parametric technique to estimate probability density of crime incidents across space |
| Lighting score | Sub-score based on proximity density of streetlights along a segment |
| Polyline | Sequence of connected line segments representing a computed walking route on the map |
| Proof of Concept (POC) | Preliminary implementation confirming core technical feasibility before full development |
| Risk profile | Predefined routing mode (e.g., balanced, lighting-priority, crime-avoidance) selectable by the user |
| Safety score | Composite metric (0–100) for a route segment combining lighting, crime, hazard, and crowd-report sub-scores |
| Segment | A contiguous portion of a walking path between two junctions in the routing graph |
| Temporal decay | Mathematical function reducing the weight of older crime data points over time in safety cost calculations |

### B. Interview Themes Cross-Reference

| Theme | Evidence Source | Related Requirements |
|-------|----------------|---------------------|
| Poor lighting is dominant safety concern | Six interviews (Weeks 4–5) | REQ-FUNC-001, REQ-FUNC-005, REQ-FUNC-007 |
| Users value transparency in route evaluation | Six interviews (Weeks 4–5): participants expressed desire to understand why a route is "safe" or "unsafe" | REQ-FUNC-006, REQ-FUNC-007 |
| Privacy concerns around location sharing | Majority of survey respondents; interview participants hesitant | REQ-SEC-001, REQ-INT-UI-009 |
| Physical hazards as important as crime | Six interviews (Weeks 4–5): potholes, puddles, uneven surfaces, stray dogs, mosquitos near rivers | REQ-FUNC-002 (hazard component) |
| Risk tolerance varies individually | Cross-analysis — Interview_1 (assault-prevention focus), Interview_2 (theft + environmental), Interview_3 (no crime concern, only environmental anxiety) | REQ-FUNC-003 (routing profiles) |
| Poor UX / nagging prompts leads to uninstall | Survey respondents, Interview_2 explicitly rejected gamification and paid subscriptions | REQ-INT-UI-001 through REQ-INT-UI-008 |
| Pre-trip planning only, not during journey | Six interview participants | REQ-INT-UI-009 (no real-time tracking) |
| Route familiarity builds trust | Interviews 1-3, participants prefer own knowledge in familiar areas, only follow app suggestions in unfamiliar locations | REQ-FUNC-003, REQ-FUNC-007 |
| Social anxieties extend beyond crime | Interviews 2-3, dangerous drivers/cyclists (ojisan cyclists), inebriated individuals, loitering teenagers, unsolicited street marketers | REQ-FUNC-002 (expanded hazard scope) |
| Interest in contributing crowd-sourced reports | Interviews 1-3, open to submitting safety reports but with location-privacy safeguards | REQ-FUNC-008 |
| Varying definitions of safety | Across all participants — some fear assault, some theft, some environmental hazards only; no single definition applies | REQ-FUNC-003 (profile customization) |

### C. Survey Results Summary

#### C.1 Methodology

- **Target population**: University students and young professionals who frequently walk at night
- **Distribution**: Online survey link distributed via social media
- **Initial version**: 67 responses collected; feedback from non-native English speakers indicated vocabulary difficulty (e.g., "loitering")
- **Revised version**: Deployed with simplified language; responses pending at time of writing
- **Question structure**: Likert scales with "other" options, open-ended free-text sections, designed to avoid leading bias

#### C.2 Key Quantitative Findings

| Topic | Finding |
|-------|---------|
| Dominant concerns | Poor lighting, empty streets, known high-crime areas |
| Desired features | Real-time safety alerts, well-lit path prioritisation, crime heatmap overlays |
| Location privacy | Majority uncomfortable with real-time location sharing |
| Willingness to trade distance | Most respondents open to taking longer routes for perceived safety |

#### C.3 Notable Free-Text Themes

- Safety concerns beyond crime: ice on footpaths, summer heat, stray animals
- Desire for transparency in how safety scores are calculated
- Users find persistent notification/nagging prompts a reason to uninstall

#### C.4 Limitations

- Sample skewed toward university-age demographics and/or social media users
- Initial version vocabulary may have excluded some non-native English speakers
- Revised version data collection still in progress