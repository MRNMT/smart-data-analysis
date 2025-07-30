Task 1: The Five V's of Big Data
Variety
•	Mixed data formats
•	Sensor types

Velocity
•	Real time decisions rely on low latency process
•	Overload can lead to system bottlenecks, traffic congestion or safety risk
Veracity
•	data_source_veracity It represents a confidence level in reliability of the data
•	It supports the veracity aspect by quantifying trust in the data
•	Potential problem is that one record has a null veracity (sensor ID: "TC-05-GER") and another is missing it entirely, indicating questionable data trustworthiness
Volume
•	Storage and data lifestyle management
Value
•	deliver real value
•	Use Case: City planners can use traffic count and noise data to optimize road layouts or adjust signal timings to reduce congestion and noise pollution.

Task 2: Data Quality Assessment

1.Data Quality :the completeness ,consistency reliability and accuracy of data so that it is good for indented use in decision making

•  Inconsistent Timestamps
•	Some timestamps use ISO format (2025-07-29T08:45:10Z), others use 29/07/2025 08:47:00 or July 29 2025, 09:15:03 AM.
•	Problem: This inconsistency affects sorting, querying, and integration with time-series systems.
•   Missing or Null Values
•	"value": null in a traffic sensor, and missing data_source_veracity.
•	Problem: Incomplete data can lead to wrong analytics or skipped records.
•  Invalid Sensor Readings
•	AQI of -10 is invalid; air quality indices must be ≥ 0.
•	Problem: Results in false alerts or misinformed decision-making.
3 bonus question
import re
from datetime import datetime

# Function to check if the timestamp is in valid ISO 8601 format (YYYY-MM-DDTHH:MM:SSZ)
def is_valid_iso8601(ts):
    try:
        # Try to parse the timestamp with the expected ISO 8601 format
        datetime.strptime(ts, "%Y-%m-%dT%H:%M:%SZ")
        return True
    except ValueError:
        # If parsing fails  return False
        return False

# Function to check if a value can be converted to a float (i.e., it's a number)
def is_number(val):
    try:
        float(val)
        return True
    except (ValueError, TypeError):
        # If it is not a number return False
        return False

# Main function to validate a list of sensor data records
def validate_data(records):
    flagged = []  # List to store invalid records
    for record in records:
        # Get the timestamp from the record (or empty string if missing)
        ts = record.get("timestamp", "")
        # Get the sensor value from the 'data' object
        val = record["data"].get("value", None)

        # Check for two conditions:
        # 1. Invalid timestamp format
        # 2. Non-numeric value
        if not is_valid_iso8601(ts) or not is_number(val):
            flagged.append(record)  # Add invalid record to the flagged list

    return flagged  # Return the list of records that failed validation


Task 3: Data Governance & Security
1. Access Control
{
  "sensor_id": "AQ-01-GER",
  "timestamp": "2025-07-29T08:45:10Z",
  "location": { "latitude": -26.2253, "longitude": 28.1613 },
  "data": { "type": "air_quality", "value": 45.5, "unit": "AQI" },
  "data_source_veracity": 0.95,
  "access_level": "Internal"
}

2.Breach Implications
1.	Business Loss
•	A rival logistics company could optimize routes faster giving them unfair competitive advantage.
2.	Reputational Damage & Legal Risk
•	Citizens may lose trust in the platform.
•	Potential violation of the POPI Act if personal or restricted data was mishandled.
3. Data access levels should be defined by a Data Governance Officer or Chief Data Officer not a developer or project manager.
•	This person knows:
•	Compliance requirements 
•	Ethical handling of data.
•	Balancing transparency with security.
•	They enforce data governance frameworks ensuring consistency, accountability, and auditability across the system.


2
