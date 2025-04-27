# FHIRLightPatientLoader v0.0.7

THE FEATURES EXPECTED WILL KEEP CHANGING RAPIDLY FOR THE CURRENT WEEK

A lightweight JavaScript library for loading and processing FHIR R5 patient data bundles, designed to work with D3.js for data visualization.

## Features

- Load and parse FHIR R5 patient bundles
- Process various FHIR resources (Observations, Conditions, Encounters, etc.)
- Medical calculations (BMI, eGFR, age)
- Prepare data for visualization with D3.js
- Handle provenance information
- Support for both browser and Node.js environments
- Basic error handling
- Multi-patient analysis and comparison

## Installation

### Browser

Include the library in your HTML file:

```html
<script src="https://abhisheksinghdhadwal.github.io/FHIRLightPatientLoader/FHIRLightPatientLoader.js"></script>
```

## Usage

### Loading Patient Data

```javascript
// Load a single patient
const patient = await FHIRLightPatientLoader.loadPatient('path/to/patient.json');

// Load multiple patients from a directory
const result = await FHIRLightPatientLoader.loadPatients('path/to/patients/directory');
const patients = result.patients; // Array of loaded Patient objects
```

### Working with Patient Data

```javascript
// Get patient demographics
const name = patient.name;
const gender = patient.gender;
const birthDate = patient.birthDate;
const age = patient.getAge();

// Get vital signs
const vitals = patient.getVitals();
const bloodPressureTrend = patient.getVitalSignTrend('blood-pressure');

// Get lab results
const labResults = patient.getLabResults();
const labChartData = patient.getLabResultsChart();

// Get medications
const activeMeds = patient.getActiveMedications();
const medHistory = patient.getMedicationHistory(medicationId);
const adherenceData = patient.getMedicationAdherenceChart(medicationId, '1m');

// Get conditions
const activeConditions = patient.getActiveConditions();
const conditionHistory = patient.getConditionHistory(conditionId);

// Get encounters
const encounterTimeline = patient.getEncounterTimeline();
const encounterDetails = patient.getEncounterDetails(encounterId);

// Get care plans
const activeCarePlans = patient.getActiveCarePlans();
const carePlanDetails = patient.getCarePlanDetails(carePlanId);

// Get clinical notes
const clinicalNotes = patient.getClinicalNotesHistory();
const rawClinicalNotes = patient.getClinicalNotesHistory({ decode: false });
```

### Medical Calculations

```javascript
// Calculate medical metrics
const bmi = patient.calculateBMI();
const egfr = patient.calculateEGFR();
```

### Visualization Data Preparation

```javascript
// Generate scatter plot data
const riskFactorData = patient.generateRiskFactorScatter('bmi', 'cholesterol');

// Get observation history
const glucoseHistory = patient.getObservationHistory('laboratory', startDate, endDate);

// Get vital sign trends
const bpTrend = patient.getVitalSignTrend('blood-pressure');
```

### Provenance and Relationships

```javascript
// Get provenance for a resource
const provenance = patient.getProvenanceForResource('observation-123');

// Get related resources
const relatedResources = patient.getRelatedResources('observation-123', 'author');

// Get resource timeline
const timeline = patient.getResourceTimeline(resourceId);
```

### Multi-Patient Analysis

The library supports loading and analyzing multiple patients simultaneously, enabling population-level insights and comparative analysis.

#### Loading Multiple Patients

```javascript
// Basic loading with progress tracking
const result = await FHIRLightPatientLoader.loadPatients('../sample_patient', {
    onProgress: (progress) => {
        console.log(`Processed ${progress.processed} of ${progress.total} files`);
    }
});

// Custom loading options
const result = await FHIRLightPatientLoader.loadPatients('../sample_patient', {
    batchSize: 3,           // Process 3 files at a time
    continueOnError: true,  // Continue even if some files fail
    onProgress: updateProgress
});
```

The `loadPatients` method returns an object containing:
- `patients`: Array of loaded Patient objects
- `summary`: Loading statistics (total files, successful loads, failed loads)
- `errors`: Array of any errors encountered during loading

#### Population Analysis

```javascript
// Analyze demographics across patients
const ageGroups = patients.reduce((acc, patient) => {
    const age = patient.getAge();
    const group = Math.floor(age / 10) * 10;
    acc[`${group}-${group + 9}`] = (acc[`${group}-${group + 9}`] || 0) + 1;
    return acc;
}, {});

// Compare vital signs across patients
const vitalSigns = patients.map(patient => ({
    name: patient.name?.given?.[0],
    vitals: patient.getVitals()
}));

// Find common conditions
const commonConditions = patients.reduce((acc, patient) => {
    patient.getActiveConditions().forEach(condition => {
        const conditionName = condition.code?.text || 'Unknown';
        acc[conditionName] = (acc[conditionName] || 0) + 1;
    });
    return acc;
}, {});
```

#### Example Files

The library includes example files demonstrating multi-patient features:

1. **multiple-patients-showcase.html**
   - Demonstrates loading multiple patients
   - Shows demographic analysis
   - Displays condition prevalence
   - Provides vital signs summary

2. **multi-patient-chart.html**
   - Interactive chart comparing vital signs across patients
   - Supports multiple vital sign types
   - Time range filtering
   - Interactive tooltips and legends

## API Reference

### FHIRLightPatientLoader

#### Methods

- `loadPatient(filePath)`: Loads and parses a single FHIR patient JSON file
- `loadPatients(directoryPath)`: Loads and parses all FHIR patient JSON files in a directory

### Patient Object

#### Properties

- `id`: Patient identifier
- `name`: Patient name
- `gender`: Patient gender
- `birthDate`: Patient birth date
- `encounters`: Array of Encounter resources
- `conditions`: Array of Condition resources
- `observations`: Array of Observation resources
- `immunizations`: Array of Immunization resources
- `diagnosticReports`: Array of DiagnosticReport resources
- `documentReferences`: Array of DocumentReference resources
- `claims`: Array of Claim resources
- `explanationOfBenefits`: Array of ExplanationOfBenefit resources
- `procedures`: Array of Procedure resources
- `medicationRequests`: Array of MedicationRequest resources
- `careTeams`: Array of CareTeam resources
- `carePlans`: Array of CarePlan resources
- `provenances`: Array of Provenance resources
- `devices`: Array of Device resources
- `supplyDeliveries`: Array of SupplyDelivery resources
- `medications`: Array of Medication resources
- `medicationAdministrations`: Array of MedicationAdministration resources

#### Methods

- `getAge()`: Calculates patient's age
- `getVitals()`: Returns all vital sign observations
- `getVitalSignTrend(vitalType)`: Returns trend data for a specific vital sign
- `getLabResults()`: Returns all laboratory observations
- `getLabResultsChart()`: Prepares lab results data for visualization
- `getActiveMedications()`: Returns currently active medications
- `getMedicationHistory(medicationId)`: Returns history for a specific medication
- `getMedicationAdherenceChart(medicationId, period)`: Prepares medication adherence data
- `getActiveConditions()`: Returns currently active conditions
- `getConditionHistory(conditionId)`: Returns history for a specific condition
- `getEncounterTimeline()`: Returns chronological list of encounters
- `getEncounterDetails(encounterId)`: Returns detailed information about an encounter
- `getActiveCarePlans()`: Returns currently active care plans
- `getCarePlanDetails(carePlanId)`: Returns detailed information about a care plan
- `getClinicalNotesHistory(opts)`: Returns chronological list of clinical notes
  - `opts.decode`: Boolean (default: true) - Whether to decode Base64-encoded content
- `calculateBMI()`: Calculates Body Mass Index
- `calculateEGFR()`: Calculates estimated Glomerular Filtration Rate
- `getProvenanceForResource(resourceId)`: Returns provenance information for a resource
- `getResourceTimeline(resourceId)`: Returns timeline of related resources
- `getRelatedResources(resourceId, relationshipType)`: Returns related resources
- `getPatientEventHistory()`: Returns a chronological timeline of all patient events, including:
  - Encounters with start and end dates
  - Procedures with performed dates
  - Conditions with onset and abatement dates
  - Clinical notes with author information
  - Diagnostic reports with results
  - Observations (labs, vitals, imaging) with values
  - Medications with status and timing
  - Immunizations with occurrence dates
  - Care plans with period information
  
  Each event includes:
  - start/end timestamps
  - event type and label
  - resource type and ID
  - metadata (encounter ID, status, age at event, etc.)
  - Additional context-specific information (e.g., lab results, medication status)

Go through the function showcase in [examples/function-showcase.html](examples/function-showcase.html)

## Error Handling

The library includes basic error handling for:
- Invalid file paths
- Malformed JSON
- Missing resources

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request or describe the features you're looking for in the issues section.

## License

Apache 2.0 License

## References

- [FHIR R5 Specification](https://hl7.org/fhir/R5/index.html)
- [FHIR Resource Index](https://hl7.org/fhir/resourcelist.html)
- [D3.js Documentation](https://d3js.org/) 