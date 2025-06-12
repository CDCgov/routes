# 📡 Public Health Condition Routing Rules

This repository defines jurisdiction-specific and default routing rules for reportable public health conditions using structured JSON. These rules govern the timely electronic transmission of confirmed, suspected, and tested-for case reports, labs, ADT to appropriate public health recipients.

We are hoping for collaboratin from a broacd spectrum of stakeholders.  Pull requests and comments are welcome.  You cann add an Issue to comment without making a pull request.

---

## 📁 Repository Structure

- `default/` – Contains general rules applied when no state-specific override exists.
- `STLTs/` – Contains rules specific to U.S. states (e.g., `NC/` for North Carolina).

---

## Condition Routing Rule Format

These two files are examples of a structured, open JSON format for representing public health routing and trigger rules related to case reporting, lab, and ADT data.

* `example-mealses-condition-default.json` represents a the **U.S. Nationwide default for** Measles.  Its `common_name` is  `measles.cste.org` and type is `BASE-CONDITION`. 
* `example-measles-condition-north-carolina.json` represents a **state-specific extension**, in this case for **North Carolina**. It is illustrative only and does not reflect current state routing rules. Its common\_name is `measles.dph.ncdhhs.gov` and its type is `EXTENDED-CONDITION`.

Any field present in the default file is considered valid in a state-specific or custom file.

---

## Shared Top-Level Fields

| Field Name                  | Type            | Description                                                                               |
| --------------------------- | --------------- | ----------------------------------------------------------------------------------------- |
| `uuid`                      | String (UUID)   | Unique identifier for the rule definition.                                                |
| `name`                      | String          | Descriptive name of the routing rule.                                                     |
| `type`                      | String (enum)   | Either `base_routing_rule` for defaults or `custom_routing_rule` for STLT-specific logic. |
| `common_name`               | String (domain) | A user-friendly or domain-based identifier.                                               |
| `description`               | String          | Human-readable explanation of the rule set.                                               |
| `timliness_threshhold_days` | Integer         | Maximum number of days to consider a report timely.                                       |
| `timebox_description`       | String          | (Optional) Time-based deduplication note, e.g., “No resubmission within 180 days.”        |

---

## Trigger Fields (used to identify relevant data)

These are value-coded identifiers (ICD, LOINC, SNOMED, etc.) that determine when routing should be triggered.

| Field Name          | Type           | Description                                                     |
| ------------------- | -------------- | --------------------------------------------------------------- |
| `trigger_diagnosis` | Array\[String] | Code sets (e.g., ICD, SNOMED) indicating measles diagnoses.     |
| `trigger_labtest`   | Array\[String] | LOINC codes representing relevant lab tests.                    |
| `trigger_vaccine`   | Array\[String] | Vaccine administration codes that may contribute to case logic. |

---

## Value Set Definitions

These are groups of standardized codes used for evaluating public health case and routing logic.

### `reportable_value_sets`

Grouped by context:

* `clinical`
* `laboratory`
* `epidemiologic`

Each item includes:

* `name`: Human-readable name
* `oid`: Object identifier (e.g., for use with VSAC)
* `description`: Description of value set content

### `contextual_value_sets`

Additional sets that offer contextual relevance but are not themselves triggers.

Grouped by:

* `clinical_context`
* `lab_context`
* `epidemiological_context`

Each entry has the same structure as above.

---

## Routing Recipients (State/Custom File)

These fields specify **where to route data** depending on case classification:

| Field Name              | Type           | Description                                                 |
| ----------------------- | -------------- | ----------------------------------------------------------- |
| `confirmed_recipients`  | Array\[String] | FQDNs to send confirmed cases.                              |
| `suspected_recipients`  | Array\[String] | FQDNs for suspected cases.                                  |
| `tested_for_recipients` | Array\[String] | FQDNs to send data when tested but not confirmed/suspected. |

---

## Position Statement Logic (`cste_position_statement_logic`)

This logic provides criteria derived from CSTE position statements. It includes both clinical and laboratory rules that define reportability.

Each logic entry includes:

| Field Name      | Type   | Description                                                         |
| --------------- | ------ | ------------------------------------------------------------------- |
| `id`            | String | Unique rule ID (e.g., CLIN1, LAB2).                                 |
| `description`   | String | Explanation of the logic rule.                                      |
| `datatype`      | String | One of `CLINICAL`, `LABORATORY`, `EPIDEMIOLOGIC`.                   |
| `lab_reporting` | String | Indicator if lab data should be reported (e.g., `S` for suspected). |
| `dx`            | String | Diagnosis relevance (`S` for suspected, `C` for confirmed, etc.).   |
| `lab`           | String | Lab test indicator.                                                 |
| `clin_plus_lab` | String | Combination logic for clinical + lab presence (`O` for optional).   |
| `clin_plus_epi` | String | Combination logic for clinical + epidemiologic context.             |

---

## Notes

* All fields used in the **default file** (`example-mealses-condition-default.json`) may appear in any **extension or custom file** (like the NC-specific file).
* This format is designed to be interoperable, machine-readable, and human-verifiable.
* Intended for use in national public health surveillance and STLT-specific routing logic scenarios.


## 🧪 Example: Default/Base Routing Rule (Measles)

Located at: `default/default_measles_routing.json`

```json
{
    "uuid": "3234-AEFB-5678-9ABC-DEF01234568",
    "name": "Measles Condition",
    "type": "BASE-CONDITION",
    "common_name": "measles.cste.org",
    "description": "Measles Condition - US Default",
    "timliness_threshhold_days": 7,
    "timebox_description": "Do not submit for same individual more than 1x every 180 days.",
    "trigger_diagnosis": [
        "ICD9|0.0550",
        "ICD9|055.1",
        "ICD9|055.2",
        "ICD9|055.8",
        "ICD9|055.9",
        "ICD10|B05.0",
        "ICD10|B05.1",
        "ICD10|B05.2",
        "ICD10|B05.3",
        "ICD10|B05.8",
        "ICD10|B05.9",
        "LOINC|24315-0",
        "LOINC|24316-8",
        "LOINC|32065-1",
        "LOINC|32066-9",
        "SNOMEDCT|14189004",
        "SNOMEDCT|36971009",
        "SNOMEDCT|36971009",
        "SNOMEDCT|312850006",
        "CPT|90707",
        "HCPCS|Q2036",
        "ICD10|3E0G3HZ",
        "SNOMEDCT|442805005",
        "CVX|03"
    ],
    "trigger_labtest": [
        "LOINC|24315-0",
        "LOINC|24316-8",
        "LOINC|24317-6",
        "LOINC|24318-4",
        "LOINC|32065-1",
        "LOINC|32066-9",
        "LOINC|32067-7"
    ],
    "trigger_vaccine": [
        "CPT|90705",
        "HCPCS|Q2037",
        "ICD10PCS|3E0G39Z",
        "SNOMEDCT|16988000",
        "CVX|05"
    ],
    "reportable_value_sets": {
        "clinical": [
            {
                "name": "Measles (Disorders) (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.127",
                "description": "SNOMED codes for Measles as a diagnosis or active problem"
            },
            {
                "name": "Measles (Disorders) (ICD10CM)",
                "oid": "2.16.840.1.113762.1.4.1146.126",
                "description": "ICD-10-CM codes for Measles as a diagnosis or active problem"
            },
            {
                "name": "Measles [Suspected] (Disorders) (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.1436",
                "description": "SNOMED codes for suspected Measles"
            },
            {
                "name": "Measles (Koplik Spots) (Disorders) (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.51",
                "description": "Measles-specific clinical observations like Koplik spots"
            }
        ],
        "laboratory": [
            {
                "name": "Measles (Tests for measles virus by Culture and Identification Method)",
                "oid": "2.16.840.1.113762.1.4.1146.296",
                "description": "Tests for identifying Measles virus by culture"
            },
            {
                "name": "Measles (Tests for measles virus Nucleic Acid)",
                "oid": "2.16.840.1.113762.1.4.1146.297",
                "description": "Nucleic acid tests for Measles"
            },
            {
                "name": "Measles (Tests for measles virus Antigen)",
                "oid": "2.16.840.1.113762.1.4.1146.298",
                "description": "Antigen tests for Measles"
            },
            {
                "name": "Measles (Tests for measles virus IgM Antibody)",
                "oid": "2.16.840.1.113762.1.4.1146.299",
                "description": "IgM antibody tests for Measles"
            },
            {
                "name": "Measles (Tests for measles virus IgG Antibody [Ratio] in Serum)",
                "oid": "2.16.840.1.113762.1.4.1146.818",
                "description": "IgG ratio tests (acute vs. convalescent serum)"
            },
            {
                "name": "Measles (Test Panels for measles virus Nucleic Acid)",
                "oid": "2.16.840.1.113762.1.4.1146.759",
                "description": "Multi-test panels for Measles nucleic acid"
            },
            {
                "name": "Measles (Test Panels for measles virus IgM Antibody)",
                "oid": "2.16.840.1.113762.1.4.1146.369",
                "description": "Multi-test panels for IgM antibodies to Measles"
            },
            {
                "name": "Measles (Organism or Substance in Lab Results)",
                "oid": "2.16.840.1.113762.1.4.1146.292",
                "description": "SNOMED codes indicating lab-detected Measles organisms"
            }
        ],
        "epidemiologic": [
            {
                "name": "Exposure to Measles (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.2341",
                "description": "SNOMED codes for confirmed exposure to Measles"
            },
            {
                "name": "Increased Risk for Exposure to Measles (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.2342",
                "description": "SNOMED codes indicating increased risk of exposure"
            }
        ]
    },
    "measles_contextual_value_sets": {
        "clinical_context": [
            {
                "name": "Measles (History of Disease) (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.2004",
                "description": "SNOMED codes indicating a patient’s past history of Measles"
            },
            {
                "name": "Measles (Vaccine Administered) (CVX)",
                "oid": "2.16.840.1.113762.1.4.1146.125",
                "description": "CVX codes for Measles-containing vaccines administered"
            },
            {
                "name": "Measles (Vaccine Administered) (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.60",
                "description": "SNOMED codes for Measles vaccine products given"
            },
            {
                "name": "Measles (Vaccination Procedure) (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.58",
                "description": "SNOMED codes for procedures related to Measles vaccination"
            },
            {
                "name": "Measles (Adverse Reactions) (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.59",
                "description": "Adverse reactions related to Measles vaccination or infection"
            }
        ],
        "lab_context": [
            {
                "name": "Measles (Tests for measles virus IgG Antibody [Presence])",
                "oid": "2.16.840.1.113762.1.4.1146.300",
                "description": "IgG presence tests used to determine immunity, not for case confirmation"
            },
            {
                "name": "Measles (Test Panels for measles virus IgG Antibody)",
                "oid": "2.16.840.1.113762.1.4.1146.370",
                "description": "Panels for IgG antibodies, often used for immunity screening"
            },
            {
                "name": "Measles (Tests for measles virus Ab [Presence])",
                "oid": "2.16.840.1.113762.1.4.1146.301",
                "description": "Tests checking general antibody presence, not case-defining"
            },
            {
                "name": "Measles (Test Panels for measles virus Ab)",
                "oid": "2.16.840.1.113762.1.4.1146.371",
                "description": "General panels for measles antibodies (IgG/IgM), not specific"
            }
        ],
        "epidemiologic_context": [
            {
                "name": "Travel or Residence in Country with Endemic Measles (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.2343",
                "description": "Travel or residence in countries where Measles is endemic"
            },
            {
                "name": "Travel or Residence in U.S. State with Endemic Measles (SNOMED)",
                "oid": "2.16.840.1.113762.1.4.1146.2344",
                "description": "Travel or residence in U.S. states/territories with endemic Measles"
            }
        ]
    },
    "cste_position_statement_logic": [
        {
            "id": "CLIN1",
            "description": "CLINICAL|Measles (as a diagnosis or active problem)*",
            "datatype": "CLINICAL",
            "lab_reporting": "",
            "dx": "S",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "CLIN2",
            "description": "CLINICAL|Suspected Measles (as a diagnosis or active problem)*",
            "datatype": "CLINICAL",
            "lab_reporting": "",
            "dx": "S",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "CLIN3",
            "description": "CLINICAL|Koplik spots (for Measles)*",
            "datatype": "CLINICAL",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "CLIN4",
            "description": "CLINICAL|Rash (any)",
            "datatype": "CLINICAL",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "N",
            "clin_plus_epi": "N"
        },
        {
            "id": "CLIN5",
            "description": "CLINICAL|Fever (temperature ≥ 38°C [≥ 100.4°F])",
            "datatype": "CLINICAL",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB1",
            "description": "LABORATORY|Identification of Measles virus in a clinical specimen by culture method, including identification tests performed on an isolate - include preliminary results",
            "datatype": "LABORATORY",
            "lab_reporting": "S",
            "dx": "",
            "lab": "S",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB2",
            "description": "LABORATORY|Detection of Measles virus nucleic acid in a clinical specimen by any method",
            "datatype": "LABORATORY",
            "lab_reporting": "S",
            "dx": "",
            "lab": "S",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB3",
            "description": "LABORATORY|Detection of Measles virus antigen in a clinical specimen by any method",
            "datatype": "LABORATORY",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB4",
            "description": "LABORATORY|Detection of Measles virus IgM antibody in a clinical specimen by any method",
            "datatype": "LABORATORY",
            "lab_reporting": "S",
            "dx": "",
            "lab": "S",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB5",
            "description": "LABORATORY|Detection of 4-fold or greater increase in Measles virus IgG antibody titer between paired acute and convalescent serum specimens (collected at least 10 days but no more than 180 days apart) by any method",
            "datatype": "LABORATORY",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "O",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB6",
            "description": "LABORATORY|Lab test ordered for identification of Measles virus in a clinical specimen by culture method, including identification tests performed on an isolate",
            "datatype": "LABORATORY",
            "lab_reporting": "S",
            "dx": "",
            "lab": "S",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB7",
            "description": "LABORATORY|Lab test ordered for detection of Measles virus nucleic acid in a clinical specimen by any method",
            "datatype": "LABORATORY",
            "lab_reporting": "S",
            "dx": "",
            "lab": "S",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB8",
            "description": "LABORATORY|Lab test ordered for detection of Measles virus antigen in a clinical specimen by any method",
            "datatype": "LABORATORY",
            "lab_reporting": "S",
            "dx": "",
            "lab": "S",
            "clin_plus_lab": "",
            "clin_plus_epi": ""
        },
        {
            "id": "LAB9",
            "description": "LABORATORY|Lab test ordered for detection of Measles virus IgM antibody in a clinical specimen by any method",
            "datatype": "LABORATORY",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "O",
            "clin_plus_epi": ""
        },
        {
            "id": "EPI1",
            "description": "EPIDEMIOLOGIC|Exposure to Measles",
            "datatype": "EPIDEMIOLOGIC",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": "O"
        },
        {
            "id": "EPI2",
            "description": "EPIDEMIOLOGIC|Increased risk for exposure to Measles",
            "datatype": "EPIDEMIOLOGIC",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": "O"
        },
        {
            "id": "EPI3",
            "description": "EPIDEMIOLOGIC|Patient has or had contact with a person diagnosed with Measles",
            "datatype": "EPIDEMIOLOGIC",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": "O"
        },
        {
            "id": "EPI4",
            "description": "EPIDEMIOLOGIC|Patient is a member of a risk group as defined by public health authorities during an outbreak of Measles",
            "datatype": "EPIDEMIOLOGIC",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": "O"
        },
        {
            "id": "EPI5",
            "description": "EPIDEMIOLOGIC|Patient resides in a geographic area where Measles is endemic or an outbreak of Measles is occurring",
            "datatype": "EPIDEMIOLOGIC",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": "O"
        },
        {
            "id": "EPI6",
            "description": "EPIDEMIOLOGIC|Patient travelled during past 21 days to a geographic area where Measles is endemic or an outbreak of Measles is occurring",
            "datatype": "EPIDEMIOLOGIC",
            "lab_reporting": "",
            "dx": "",
            "lab": "",
            "clin_plus_lab": "",
            "clin_plus_epi": "O"
        }
    ]
}

## 🧪 Example: Default Extended Condition Routing Rule (Measles for North Carolina)


In this example the state is sending to AIMS and to surrounding state public health authorities.
Located at: `STLTs/NC/measles.json`
```json
{
    "uuid": "3234-AEFB-5678-9ABC-DEF01234568",
    "name": "Measles Condition Routing for NC Department of Public Health",
    "type": "EXTENDED-CONDITION",
    "common_name": "measles.dph.ncdhhs.gov",
    "description": "Routing for the condition Measles for North Carolina Department of Public Health",
    "timliness_threshhold_days": 7,
    "confirmed_recipients": [
        "FQDN|aimsplatform.com",
        "FQDN|dph.ncdhhs.gov",
        "FQDN|vdh.virginia.gov",
        "FQDN|health.tn.gov",
        "FQDN|dph.sc.gov",
        "FQDN|tn.gov"
    ],
    "suspected_recipients": [
        "FQDN|aimsplatform.com",
        "FQDN|dph.ncdhhs.gov",
        "FQDN|vdh.virginia.gov",
        "FQDN|health.tn.gov",
        "FQDN|dph.sc.gov",
        "FQDN|tn.gov"
    ],
    "tested_for_recipients": [
        "FQDN|aimsplatform.com",
        "FQDN|dph.ncdhhs.gov",
        "FQDN|vdh.virginia.gov",
        "FQDN|health.tn.gov",
        "FQDN|dph.sc.gov",
        "FQDN|tn.gov"
    ]
}