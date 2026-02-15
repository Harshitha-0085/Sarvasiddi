# Requirements Document: UdyogAI Predictive Maintenance Platform
Prepared by Team Sarvasiddhi  
AI for Bharat Hackathon – AWS  
Version: 1.0


## Introduction

UdyogAI is an AI-powered predictive maintenance platform designed specifically for Indian Micro, Small, and Medium Enterprises (MSMEs). The platform addresses the critical challenge of unexpected machinery failures that cause financial losses, production downtime, and reduced productivity in small-scale industries. By leveraging real-time sensor data collection, AI/ML-based anomaly detection, and predictive analytics, UdyogAI makes Industry 4.0 technology affordable and accessible to Bharat's manufacturing sector.

The system collects time-series data from industrial machinery through sensors (vibration, temperature, load), analyzes patterns using machine learning models, detects anomalies, predicts failure probability, and provides actionable maintenance recommendations through an intuitive web interface.

UdyogAI helps MSMEs reduce unexpected downtime, lower maintenance costs,
increase equipment lifespan, and improve worker safety.
By shifting from reactive to predictive maintenance, factories can plan repairs,
avoid production losses, and improve profitability.

## Glossary

- **UdyogAI_Platform**: The complete predictive maintenance system including frontend, backend, AI/ML services, and cloud infrastructure
- **Machine**: Industrial equipment being monitored (e.g., motors, pumps, compressors, CNC machines)
- **Sensor_Data**: Time-series measurements from physical sensors including vibration, temperature, and load readings
- **Health_Score**: A numerical value (0-100) representing the current operational condition of a machine
- **Anomaly**: A deviation from normal operational patterns detected by AI models
- **Failure_Risk**: Probability (0-100%) that a machine will fail within a specified time window
- **Alert**: A notification triggered when machine conditions exceed defined thresholds
- **Alert_Severity**: Classification of alerts as Low, Medium, or High based on urgency
- **Maintenance_Recommendation**: AI-generated suggestion for preventive maintenance actions
- **Data_Ingestion_Service**: Backend component that receives and validates sensor data
- **ML_Model**: Machine learning algorithm (LSTM or other) trained to detect patterns and predict failures
- **Dashboard**: Web-based user interface displaying machine health and analytics
- **Factory_Owner**: Primary user who manages overall operations and makes business decisions
- **Supervisor**: User who monitors daily operations and coordinates maintenance activities
- **Maintenance_Team**: Users who perform physical maintenance tasks based on system recommendations
- **Time_Window**: Duration for which predictions are made (e.g., next 24 hours, 7 days, 30 days)

## Requirements

### Requirement 1: Real-Time Sensor Data Ingestion

**User Story:** As a factory owner, I want the system to continuously collect sensor data from my machines, so that I can monitor their operational status in real-time.

#### Acceptance Criteria

1. WHEN sensor data is received from a machine, THE Data_Ingestion_Service SHALL validate the data format and timestamp
2. WHEN valid sensor data is received, THE Data_Ingestion_Service SHALL store it in the database within 2 seconds
3. WHEN sensor data contains vibration readings, THE Data_Ingestion_Service SHALL accept values in the range 0-100 mm/s
4. WHEN sensor data contains temperature readings, THE Data_Ingestion_Service SHALL accept values in the range -50 to 200 degrees Celsius
5. WHEN sensor data contains load readings, THE Data_Ingestion_Service SHALL accept values in the range 0-100 percent
6. IF sensor data is malformed or missing required fields, THEN THE Data_Ingestion_Service SHALL reject it and return a descriptive error
7. WHEN sensor data is stored, THE Data_Ingestion_Service SHALL associate it with the correct machine identifier and timestamp

### Requirement 2: Machine Health Score Calculation

**User Story:** As a supervisor, I want to see a simple health score for each machine, so that I can quickly identify which machines need attention.

#### Acceptance Criteria

1. WHEN new sensor data is processed, THE ML_Model SHALL calculate an updated Health_Score for the machine
2. THE Health_Score SHALL be a value between 0 and 100, where 100 represents optimal health
3. WHEN calculating Health_Score, THE ML_Model SHALL consider vibration, temperature, and load patterns from the last 24 hours
4. WHEN a machine has insufficient historical data (less than 1 hour), THE UdyogAI_Platform SHALL display "Insufficient Data" instead of a Health_Score
5. WHEN Health_Score drops below 70, THE UdyogAI_Platform SHALL classify the machine as requiring attention
6. WHEN Health_Score is between 70-85, THE UdyogAI_Platform SHALL classify the machine as moderate health
7. WHEN Health_Score is above 85, THE UdyogAI_Platform SHALL classify the machine as healthy

### Requirement 3: Anomaly Detection

**User Story:** As a maintenance team member, I want the system to automatically detect unusual machine behavior, so that I can investigate potential issues before they become failures.

#### Acceptance Criteria

1. WHEN sensor data deviates from learned normal patterns, THE ML_Model SHALL flag it as an Anomaly
2. WHEN an Anomaly is detected, THE UdyogAI_Platform SHALL record the anomaly type, timestamp, and affected sensor parameters
3. WHEN vibration exceeds 3 standard deviations from the machine's baseline, THE ML_Model SHALL detect it as an Anomaly
4. WHEN temperature exceeds 3 standard deviations from the machine's baseline, THE ML_Model SHALL detect it as an Anomaly
5. WHEN load patterns show irregular fluctuations beyond normal variance, THE ML_Model SHALL detect it as an Anomaly
6. WHEN an Anomaly is detected, THE UdyogAI_Platform SHALL update the machine's Health_Score accordingly
7. THE ML_Model SHALL learn and update baseline patterns weekly to adapt to normal operational changes

### Requirement 4: Failure Risk Prediction

**User Story:** As a factory owner, I want to know the probability that a machine will fail in the near future, so that I can plan maintenance proactively and avoid costly downtime.

#### Acceptance Criteria

1. WHEN analyzing machine data, THE ML_Model SHALL calculate Failure_Risk for multiple Time_Windows (24 hours, 7 days, 30 days)
2. THE Failure_Risk SHALL be expressed as a percentage between 0% and 100%
3. WHEN Failure_Risk exceeds 70% for any Time_Window, THE UdyogAI_Platform SHALL classify it as high risk
4. WHEN Failure_Risk is between 40% and 70%, THE UdyogAI_Platform SHALL classify it as medium risk
5. WHEN Failure_Risk is below 40%, THE UdyogAI_Platform SHALL classify it as low risk
6. WHEN calculating Failure_Risk, THE ML_Model SHALL use LSTM or equivalent time-series analysis techniques
7. WHEN a machine has insufficient training data, THE UdyogAI_Platform SHALL display "Prediction Unavailable" instead of Failure_Risk

### Requirement 5: Alert Generation and Management

**User Story:** As a supervisor, I want to receive timely alerts when machines show signs of potential failure, so that I can take immediate action to prevent breakdowns.

#### Acceptance Criteria

1. WHEN Failure_Risk exceeds 70%, THE UdyogAI_Platform SHALL generate a High severity Alert
2. WHEN Failure_Risk is between 40% and 70%, THE UdyogAI_Platform SHALL generate a Medium severity Alert
3. WHEN Health_Score drops below 60, THE UdyogAI_Platform SHALL generate a Medium severity Alert
4. WHEN an Anomaly is detected with significant deviation (>4 standard deviations), THE UdyogAI_Platform SHALL generate a High severity Alert
5. WHEN an Alert is generated, THE UdyogAI_Platform SHALL include machine identifier, timestamp, Alert_Severity, and triggering condition
6. WHEN multiple alerts are generated for the same machine within 1 hour, THE UdyogAI_Platform SHALL consolidate them into a single alert to avoid notification fatigue
7. WHEN an Alert is acknowledged by a user, THE UdyogAI_Platform SHALL mark it as acknowledged and record the user and timestamp

### Requirement 6: Maintenance Recommendations

**User Story:** As a maintenance team member, I want specific recommendations on what maintenance actions to take, so that I can address issues effectively without guessing.

#### Acceptance Criteria

1. WHEN an Alert is generated, THE UdyogAI_Platform SHALL provide at least one Maintenance_Recommendation
2. WHEN vibration anomalies are detected, THE UdyogAI_Platform SHALL recommend checking bearing alignment and lubrication
3. WHEN temperature anomalies are detected, THE UdyogAI_Platform SHALL recommend checking cooling systems and ventilation
4. WHEN load anomalies are detected, THE UdyogAI_Platform SHALL recommend checking power supply and mechanical load balance
5. WHEN Failure_Risk is high, THE Maintenance_Recommendation SHALL include urgency level and estimated time to address
6. THE Maintenance_Recommendation SHALL be displayed in both English and Hindi for accessibility
7. WHEN a Maintenance_Recommendation is completed, THE UdyogAI_Platform SHALL allow users to mark it as done and add notes

### Requirement 7: Dashboard and Visualization

**User Story:** As a factory owner, I want a clear visual dashboard showing all my machines' status, so that I can understand the overall health of my operations at a glance.

#### Acceptance Criteria

1. WHEN a user accesses the Dashboard, THE UdyogAI_Platform SHALL display all registered machines with their current Health_Score
2. WHEN displaying machines, THE Dashboard SHALL use color coding (green for healthy, yellow for moderate, red for attention needed)
3. WHEN a user selects a machine, THE Dashboard SHALL display detailed metrics including current sensor readings, Health_Score, and Failure_Risk
4. WHEN displaying historical data, THE Dashboard SHALL show time-series graphs for vibration, temperature, and load over selectable time ranges (1 day, 7 days, 30 days)
5. WHEN active alerts exist, THE Dashboard SHALL display them prominently with Alert_Severity indicators
6. THE Dashboard SHALL refresh automatically every 30 seconds to show updated data
7. WHEN the Dashboard loads, THE UdyogAI_Platform SHALL display data within 3 seconds for up to 50 machines

### Requirement 8: Historical Trend Analysis

**User Story:** As a supervisor, I want to view historical trends and patterns for each machine, so that I can understand long-term performance and identify recurring issues.

#### Acceptance Criteria

1. WHEN a user requests historical data for a machine, THE UdyogAI_Platform SHALL retrieve data for the requested time range
2. THE UdyogAI_Platform SHALL support historical queries for time ranges up to 1 year
3. WHEN displaying historical trends, THE Dashboard SHALL show Health_Score changes over time
4. WHEN displaying historical trends, THE Dashboard SHALL highlight periods where Anomalies were detected
5. WHEN displaying historical trends, THE Dashboard SHALL show maintenance actions taken and their timestamps
6. WHEN comparing time periods, THE Dashboard SHALL allow users to overlay data from different date ranges
7. WHEN exporting historical data, THE UdyogAI_Platform SHALL provide data in CSV format

### Requirement 9: Machine Registration and Configuration

**User Story:** As a factory owner, I want to register new machines and configure their monitoring parameters, so that I can expand the system as my operations grow.

#### Acceptance Criteria

1. WHEN a user registers a new machine, THE UdyogAI_Platform SHALL require machine name, type, and unique identifier
2. WHEN a machine is registered, THE UdyogAI_Platform SHALL generate a unique machine ID and sensor endpoint
3. WHEN configuring a machine, THE UdyogAI_Platform SHALL allow users to set custom threshold values for vibration, temperature, and load
4. WHEN a machine is registered, THE UdyogAI_Platform SHALL initialize it with default threshold values based on machine type
5. WHEN a user updates machine configuration, THE UdyogAI_Platform SHALL apply changes within 1 minute
6. WHEN a machine is deactivated, THE UdyogAI_Platform SHALL stop processing its sensor data but retain historical records
7. THE UdyogAI_Platform SHALL support registration of at least 100 machines per factory account

### Requirement 10: User Authentication and Authorization

**User Story:** As a factory owner, I want secure access control so that only authorized personnel can view and manage machine data.

#### Acceptance Criteria

1. WHEN a user attempts to access the Dashboard, THE UdyogAI_Platform SHALL require authentication with username and password
2. WHEN a user logs in with valid credentials, THE UdyogAI_Platform SHALL create a session valid for 8 hours
3. WHEN a user has Factory_Owner role, THE UdyogAI_Platform SHALL grant access to all features including machine registration and configuration
4. WHEN a user has Supervisor role, THE UdyogAI_Platform SHALL grant access to monitoring, alerts, and maintenance recommendations but not machine registration
5. WHEN a user has Maintenance_Team role, THE UdyogAI_Platform SHALL grant access to alerts and maintenance recommendations only
6. WHEN a user attempts unauthorized actions, THE UdyogAI_Platform SHALL deny access and log the attempt
7. WHEN a user logs out, THE UdyogAI_Platform SHALL invalidate the session immediately

### Requirement 11: ML Model Training and Updates

**User Story:** As a system administrator, I want the ML models to continuously learn from new data, so that predictions become more accurate over time.

#### Acceptance Criteria

1. WHEN a machine accumulates 30 days of sensor data, THE ML_Model SHALL perform initial training
2. WHEN the ML_Model is training, THE UdyogAI_Platform SHALL continue using the previous model version for predictions
3. WHEN a new model version is trained, THE UdyogAI_Platform SHALL validate it against test data before deployment
4. WHEN a new model achieves better accuracy than the current model, THE UdyogAI_Platform SHALL deploy it automatically
5. THE ML_Model SHALL retrain weekly using the latest 90 days of data
6. WHEN model accuracy drops below 75%, THE UdyogAI_Platform SHALL generate an alert for system administrators
7. WHEN training data contains labeled failure events, THE ML_Model SHALL use them to improve Failure_Risk predictions

### Requirement 12: API for Sensor Integration

**User Story:** As a system integrator, I want a well-documented API to send sensor data from Arduino/ESP32 devices, so that I can easily connect physical sensors to the platform.

#### Acceptance Criteria

1. THE UdyogAI_Platform SHALL provide a REST API endpoint for sensor data submission
2. WHEN sensor data is submitted via API, THE UdyogAI_Platform SHALL require authentication using API keys
3. WHEN API requests are received, THE UdyogAI_Platform SHALL validate the API key within 100 milliseconds
4. THE API SHALL accept sensor data in JSON format with fields: machine_id, timestamp, vibration, temperature, load
5. WHEN API requests are malformed, THE UdyogAI_Platform SHALL return HTTP 400 with error details
6. WHEN API authentication fails, THE UdyogAI_Platform SHALL return HTTP 401
7. THE API SHALL support batch submission of up to 100 sensor readings in a single request

### Requirement 13: Cloud Infrastructure and Scalability

**User Story:** As a factory owner, I want the system to handle growing data volumes reliably, so that I can add more machines without performance degradation.

#### Acceptance Criteria

1. THE UdyogAI_Platform SHALL use AWS IoT Core for sensor data ingestion
2. THE UdyogAI_Platform SHALL store sensor data in DynamoDB with automatic scaling
3. THE UdyogAI_Platform SHALL store ML models and training data in S3
4. THE UdyogAI_Platform SHALL use AWS Lambda for serverless data processing
5. THE UdyogAI_Platform SHALL use AWS SageMaker for ML model training and deployment
6. WHEN data volume increases, THE UdyogAI_Platform SHALL scale automatically without manual intervention
7. THE UdyogAI_Platform SHALL maintain 99.5% uptime availability

### Requirement 14: Data Retention and Privacy

**User Story:** As a factory owner, I want my operational data to be stored securely and retained appropriately, so that I comply with data privacy requirements while maintaining useful historical records.

#### Acceptance Criteria

1. THE UdyogAI_Platform SHALL encrypt all sensor data at rest using AES-256
2. THE UdyogAI_Platform SHALL encrypt all data in transit using TLS 1.2 or higher
3. THE UdyogAI_Platform SHALL retain raw sensor data for 90 days
4. THE UdyogAI_Platform SHALL retain aggregated daily summaries for 2 years
5. WHEN data retention periods expire, THE UdyogAI_Platform SHALL automatically delete expired data
6. THE UdyogAI_Platform SHALL isolate data between different factory accounts
7. WHEN a factory account is deleted, THE UdyogAI_Platform SHALL permanently delete all associated data within 30 days

### Requirement 15: Notification System

**User Story:** As a supervisor, I want to receive notifications through multiple channels when critical alerts occur, so that I don't miss important warnings even when not actively monitoring the dashboard.

#### Acceptance Criteria

1. WHEN a High severity Alert is generated, THE UdyogAI_Platform SHALL send notifications through configured channels
2. WHERE email notifications are enabled, THE UdyogAI_Platform SHALL send alert emails within 1 minute
3. WHERE SMS notifications are enabled, THE UdyogAI_Platform SHALL send alert SMS within 1 minute
4. WHERE in-app notifications are enabled, THE Dashboard SHALL display alert notifications immediately
5. WHEN a user configures notification preferences, THE UdyogAI_Platform SHALL allow selection of alert severity levels to receive
6. WHEN notification delivery fails, THE UdyogAI_Platform SHALL retry up to 3 times with exponential backoff
7. THE UdyogAI_Platform SHALL support notification delivery in English and Hindi languages
