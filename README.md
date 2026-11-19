# Malaysia_Road_Accident_Integration using ArcGIS Data Pipeline

## 1. Project Overview

This project demonstrates the implementation of an ETL (Extract, Transform, Load) workflow using **ArcGIS Online Data Pipelines** to integrate and prepare road accident data for geospatial analysis.

The project uses sample road accident records containing accident dates, locations, severity levels, vehicle types and casualty information. The accident dataset is processed and integrated with Malaysia's administrative boundary data to support spatial analysis and geographic visualization.

The final output is a hosted feature layer in ArcGIS Online, which can be used to create interactive web maps and dashboards.

**Project Type:** GIS Data Engineering / ETL  
**Platform:** ArcGIS Online  
**Tool:** ArcGIS Data Pipelines  
**Data Format:** CSV and Hosted Feature Layers  
**Output:** Hosted Feature Layer

---

## 2. Project Objectives

The main objectives are:

- Import road accident records from CSV datasets.
- Perform data quality checks and remove invalid records.
- Standardize and prepare accident attributes.
- Convert latitude and longitude coordinates into geographic point features.
- Calculate additional attributes to classify accident severity.
- Integrate accident records with administrative boundary information.
- Publish the processed dataset as a hosted feature layer.
- Prepare the output for spatial visualization and analysis.

---

## 3. Tools and Technologies

| Tool | Purpose |
|---|---|
| ArcGIS Online | Cloud-based GIS platform |
| ArcGIS Data Pipelines | ETL and data integration |
| CSV | Source data format |
| Arcade | Attribute calculation |
| ArcGIS Map Viewer | Geographic visualization |
| Hosted Feature Layer | Final data storage and publishing |

---

## 4. Dataset Description

Two primary datasets are used in this project.

### Dataset 1: Road Accident Records

File: `road_accidents_sample.csv`

This dataset contains synthetic road accident records created for demonstration and learning purposes.

| Field | Description |
|---|---|
| Accident_ID | Unique accident identifier |
| Accident_Date | Date of accident |
| Accident_Year | Year of accident |
| State | Malaysian state |
| District | Administrative district |
| District_Key | District matching identifier |
| Latitude | Geographic latitude |
| Longitude | Geographic longitude |
| Severity | Accident severity classification |
| Vehicle_Type | Type of vehicle involved |
| Casualties | Number of casualties |
| Weather | Weather conditions |

### Dataset 2: District Reference Data

File: `district_lookup_sample.csv`

This dataset contains district reference information used to demonstrate attribute-based data integration.

| Field | Description |
|---|---|
| District_Key | Unique district identifier |
| District_Name | District name |
| State_Name | State name |
| Data_Note | Dataset description |

### Additional spatial dataset

Malaysia Administrative Boundaries

The administrative boundary dataset provides geographic reference information for spatial analysis and integration.

It is used to support the identification and visualization of accident locations within administrative areas.

**Data disclaimer:** The accident records are synthetic sample data. They do not represent actual Malaysian road accident statistics.

---

## 5. ETL Architecture

The project follows a three-stage ETL architecture.

### Extract

Data is imported into ArcGIS Data Pipelines from CSV files and ArcGIS Online hosted feature layers.

Source datasets:
- Road accident records.
- District reference table.
- Malaysia administrative boundary layer.

### Transform

The datasets undergo several data preparation and transformation operations:

1. Filter invalid accident records.
2. Select relevant attributes.
3. Create geographic point geometry.
4. Calculate accident severity scores.
5. Join accident records with district reference information.
6. Prepare the final attribute structure.

### Load

The processed dataset is published as a hosted feature layer in ArcGIS Online.

The resulting feature layer can be used in ArcGIS Map Viewer for geographic visualization and further spatial analysis.

---

## 6. Data Pipeline Implementation

The following steps describe the ETL workflow implemented in ArcGIS Data Pipelines.

### Step 1: Data Input

Imported the road accident CSV dataset into ArcGIS Data Pipelines.

The dataset was previewed to verify:
- Field names and data types.
- Coordinate values.
- Accident identifiers.
- Severity classifications.
- Missing or invalid values.

### Step 2: Data Filtering

Used the **Filter by Attribute** tool to remove records containing missing or invalid coordinates and missing accident identifiers.

SQL expression:

```sql
Latitude IS NOT NULL
AND Longitude IS NOT NULL
AND Latitude <> 0
AND Longitude <> 0
AND Accident_ID IS NOT NULL
```

This filtering process ensures that only records with valid coordinate information and accident identifiers proceed to subsequent transformations.

### Step 3: Field Selection

Used the **Select Fields** tool to retain the attributes required for analysis.

The selected fields include:
- Accident_ID
- Accident_Date
- Accident_Year
- State
- District
- District_Key
- Latitude
- Longitude
- Severity
- Vehicle_Type
- Casualties
- Weather

This reduces unnecessary fields and simplifies the downstream processing workflow.

### Step 4: Create Geographic Geometry

Used the **Create Geometry** tool to convert the latitude and longitude attributes into geographic point features.

Configuration:

| Parameter | Value |
|---|---|
| Geometry Type | Point |
| X Field | Longitude |
| Y Field | Latitude |
| Coordinate System | WGS 1984 (WKID 4326) |

The resulting geometry represents the geographic location of each accident record.

### Step 5: Calculate Accident Severity Score

Used the **Calculate Field** tool to create a new attribute called `Severity_Score`.

Arcade expression:

```javascript
var severity = $record.Severity;

if (severity == "Fatal") {
    return 3;
} else if (severity == "Serious") {
    return 2;
} else if (severity == "Minor") {
    return 1;
} else {
    return 0;
}
```

Severity classification:

| Severity | Score |
|---|---:|
| Fatal | 3 |
| Serious | 2 |
| Minor | 1 |
| Other | 0 |

This additional field provides a numerical representation of accident severity for further analysis.

### Step 6: Attribute-Based Data Integration

Used the **Join** tool to combine the accident records with the district reference dataset.

Join configuration:

| Parameter | Value |
|---|---|
| Target Dataset | Accident records |
| Join Dataset | District reference data |
| Target Field | District_Key |
| Join Field | District_Key |
| Join Type | Left Join |
| Relationship | Attribute |

The District_Key field serves as the common identifier between the two datasets.

The join enriches accident records with additional district reference information.

### Step 7: Final Field Selection

Used the **Select Fields** tool to prepare the final dataset structure.

The final output retains accident identifiers, dates, locations, severity classifications, vehicle information, casualty counts and district information.

### Step 8: Publish Output

Used the **Feature Layer Output** tool to publish the processed dataset as a hosted feature layer in ArcGIS Online.

Output item:

`Malaysia_Road_Accidents_Integrated`

The published layer provides geographic accident locations and associated attributes for visualization and further analysis.

---

## 7. Workflow Architecture

The overall data processing workflow is:

```text
INPUT DATA
    |
    |-- Road Accident CSV
    |
    |-- District Reference CSV
    |
    |-- Malaysia Administrative Boundaries
    |
    v
DATA CLEANING
    |
    |-- Filter by Attribute
    |-- Select Fields
    |
    v
GEOSPATIAL TRANSFORMATION
    |
    |-- Create Geometry
    |-- Calculate Field
    |
    v
DATA INTEGRATION
    |
    |-- Attribute Join
    |-- Spatial Join (Optional)
    |
    v
FINAL DATA PREPARATION
    |
    |-- Select Final Fields
    |
    v
DATA OUTPUT
    |
    |-- ArcGIS Online Hosted Feature Layer
    |
    v
VISUALIZATION
    |
    |-- ArcGIS Map Viewer
    |-- ArcGIS Dashboards (Optional)
```

---

## 8. Expected Results and Validation

The sample dataset contains 15 synthetic accident records and 6 district reference records.

The expected validation results are:

| Validation | Expected Result |
|---|---:|
| Initial accident records | 15 |
| Invalid records removed | 0 |
| Records after filtering | 15 |
| Geographic point features | 15 |
| Severity scores calculated | 15 |
| District records matched | 15 |
| Final output records | 15 |

Validation checks include:

- Confirming that all accident records have valid coordinates.
- Verifying that longitude and latitude are assigned correctly.
- Checking the accuracy of calculated severity scores.
- Confirming that district keys match between datasets.
- Verifying that the final hosted feature layer contains the expected records and geometry.

---

## 9. Final Output and Visualization

The final hosted feature layer can be opened in ArcGIS Online Map Viewer.

Recommended visualization:

- Display accident locations as point features.
- Categorize accident locations by severity.
- Configure pop-ups to show accident information.
- Explore accident distribution by state and district.

Potential dashboard indicators include:

- Total number of accidents.
- Number of fatal accidents.
- Total casualties.
- Accident distribution by state.
- Accidents by vehicle type.
- Accident distribution by severity.

---

## 10. Skills
This project demonstrates practical experience in:
-GIS Data Engineering.
-ETL Workflow Development.
-Data Cleaning and Transformation.
-SQL-Based Attribute Filtering.
-GIS Data Visualization.

---
## 11. Future Improvements
Potential enhancements include:
-Integrating verified real-world Malaysian road accident datasets.
-Incorporating additional administrative boundary attributes.
-Performing spatial joins between accident points and district polygons.
-Identifying accident concentration areas using spatial analysis.

---

## 12. Disclaimer
-This project is developed for educational and portfolio demonstration purposes.
-The road accident dataset contains synthetic records and should not be interpreted as official road accident statistics or used for real-world traffic safety decisions.
