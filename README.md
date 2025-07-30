

# Smart Germiston Data Analysis

Welcome to the Smart City Data Analysis project! This submission includes an analysis of sensor data from Germiston using Big Data principles, data quality assessment, and data governance best practices.

---

## Task 1: The Five V's of Big Data

### 1. Variety
- Mixed data formats: timestamps appear in multiple formats (`ISO 8601`, `DD/MM/YYYY`, full-text).
- Multiple sensor types: air quality, traffic count, temperature, humidity, noise level, and rainfall.

### 2. Velocity
- Real-time decisions rely on low-latency processing.
- Overload can lead to system bottlenecks, traffic congestion, or safety risks if not managed properly.

### 3. Veracity
- The `data_source_veracity` field represents a **confidence level** in the reliability of the data.
- It supports the **veracity** trait by quantifying trust in the data.
- ⚠️ **Problem**: One record (`sensor_id: "TC-05-GER"`) has a `null` value for veracity, and another has no veracity field at all.

### 4. Volume
- With 10,000 sensors reporting every 5 seconds for a year, we would face challenges like:
  - High storage requirements
  - Processing time
  - Lifecycle management of huge datasets

### 5. Value
- The data has real value for both citizens and city administrators.
- **Use Case**: City planners can use traffic count and noise data to optimize road layouts or adjust traffic signal timings to reduce congestion and pollution.

---

## Task 2: Data Quality Assessment

### 1. Definition of Data Quality
Data quality refers to the **completeness**, **consistency**, **reliability**, and **accuracy** of data so that it is fit for its intended use in decision-making.

### 2. Identified Data Quality Issues

#### a) Inconsistent Timestamps
- Formats used: ISO 8601, `29/07/2025 08:47:00`, and `July 29 2025, 09:15:03 AM`.
- ⚠️ **Problem**: Affects sorting, querying, and time-based analysis.

#### b) Missing or Null Values
- Example: `"value": null` in a traffic sensor, and missing `data_source_veracity` field.
- ⚠️ **Problem**: May cause analytics errors or incomplete insights.

#### c) Invalid Sensor Readings
- Example: AQI value of `-10`, which is outside the valid range.
- ⚠️ **Problem**: Can lead to false alerts or bad decision-making.

### 3. Bonus: Data Validation Code (Python)

```python
import re
from datetime import datetime

# Function to check if the timestamp is in valid ISO 8601 format (YYYY-MM-DDTHH:MM:SSZ)
def is_valid_iso8601(ts):
    try:
        # Try to parse the timestamp with the expected ISO 8601 format
        datetime.strptime(ts, "%Y-%m-%dT%H:%M:%SZ")
        return True
    except ValueError:
        return False

# Function to check if a value can be converted to a float (i.e., it's a number)
def is_number(val):
    try:
        float(val)
        return True
    except (ValueError, TypeError):
        return False

# Main function to validate a list of sensor data records
def validate_data(records):
    flagged = []  # List to store invalid records
    for record in records:
        ts = record.get("timestamp", "")
        val = record["data"].get("value", None)

        # Check for invalid timestamp or non-numeric value
        if not is_valid_iso8601(ts) or not is_number(val):
            flagged.append(record)
    return flagged
````

---

## Task 3: Data Governance & Security

### 1. Access Control (Modified JSON with Access Level)

```json
{
  "sensor_id": "AQ-01-GER",
  "timestamp": "2025-07-29T08:45:10Z",
  "location": { "latitude": -26.2253, "longitude": 28.1613 },
  "data": { "type": "air_quality", "value": 45.5, "unit": "AQI" },
  "data_source_veracity": 0.95,
  "access_level": "Internal"
}
```

### 2. Breach Implications

#### a) Business Loss

* A rival logistics company could use real-time traffic data to optimize delivery routes, giving them an unfair advantage.

#### b) Reputational Damage & Legal Risk

* Citizens may lose trust in the system.
* Potential violation of the **POPI Act** (Protection of Personal Information Act) if sensitive or restricted data is mishandled.

### 3. Authority & Control

* **Access levels should be defined by**:

  * A **Chief Data Officer (CDO)** or **Data Governance Officer**.
* **Why not a developer or project manager?**

  * These roles require understanding of:

    * Legal compliance and privacy laws
    * Ethical data handling
    * Enforcement of governance frameworks for consistency, transparency, and accountability.

