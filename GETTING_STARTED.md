# Complete CSV to Dataverse JSON Conversion - Getting Started Guide

## 🎯 What You Can Now Do

Your CSV will now be converted to **complete, production-ready Dataverse/Borealis JSON** with:

✅ All system fields (id, identifier, persistentUrl, etc.)  
✅ Complete datasetVersion information  
✅ License metadata  
✅ All metadata blocks (citation, geospatial, socialscience)  
✅ File information support  
✅ Auto-generated IDs and timestamps  
✅ Sensible defaults for missing fields  

## 📋 Quick Start

### Step 1: Install Dependencies
```bash
pip install -r requirements.txt
```

### Step 2: Prepare Your CSV

**Option A: Minimal CSV (Uses All Defaults)**
```csv
title,author: authorName; authorAffiliation,subject
"My Dataset","John Smith; Harvard","Social Sciences"
```

**Option B: Extended CSV (Customize System Fields)**
```csv
id,identifier,publisher,title,author: authorName; authorAffiliation
1000,FK2/CUSTOM001,My Organization,"My Dataset","John Smith; Harvard"
```

**Option C: Complete CSV (All Fields)**
See `TEMPLATE_CSV_WITH_ALL_COLUMNS.csv` for all available columns

### Step 3: Run the Converter
```bash
python csv_to_dataverse_json.py
```

Output: `output_metadata.json` (ready for Dataverse/Borealis!)

## 📊 CSV Column Reference

### System Fields (Optional - Have Defaults)

| Column | Purpose | Default |
|--------|---------|---------|
| `id` | Dataset ID | 1000 + row_index |
| `identifier` | Persistent identifier | FK2/{random_uuid} |
| `protocol` | DOI or HDL | "doi" |
| `authority` | Authority code | "10.70122" |
| `publisher` | Publisher name | "Dataverse" |
| `publicationDate` | Publication date (YYYY-MM-DD) | Today |
| `datasetType` | Type of dataset | "dataset" |
| `versionNumber` | Version number | 1 |
| `versionMinorNumber` | Minor version | 0 |
| `versionState` | DRAFT, RELEASED, DEACCESSIONED | "DRAFT" |
| `fileAccessRequest` | Allow requests? (true/false) | true |

### License Fields (Optional)

| Column | Default |
|--------|---------|
| `licenseName` | "CC0 1.0" |
| `licenseUri` | CC0 URI |
| `licenseIconUri` | CC0 icon URL |
| `rightsIdentifier` | "CC0-1.0" (SPDX code) |
| `rightsIdentifierScheme` | "SPDX" |
| `schemeUri` | SPDX URL |
| `languageCode` | "en" |

### Citation Metadata (Recommended)

These are the key fields for dataset description:

- `title` - Dataset title (REQUIRED for upload)
- `author: authorName; authorAffiliation` - Multiple authors separated by `|`
- `datasetContact: name; affiliation; email` - Contact person
- `subject` - Research subject (pipe-separated, controlled vocabulary)
- `keyword: keyword; vocabulary` - Keywords with optional vocabulary
- `dsDescription: description; date` - Dataset description
- `language` - Language (controlled vocabulary)
- `depositor` - Who deposited it
- `dateOfDeposit` - When deposited (YYYY)
- `publisher` - Publisher name
- `productionDate` - When produced (YYYY)
- `distributionDate` - When distributed (YYYY)
- `kindOfData` - Type of data (Quantitative, Qualitative, etc.)
- `series: name; information` - If part of a series
- `software: name; version` - Software used

### Relationship Fields

- `relatedMaterial` - Related materials (pipe-separated)
- `relatedDatasets` - Related datasets (pipe-separated)
- `dataSources` - Data sources (pipe-separated)
- `otherReferences` - Other references (pipe-separated)

### Complex/Compound Fields

Use semicolons (`;`) to separate subfields and pipes (`|`) for multiple entries:

```csv
author: authorName; authorAffiliation,datasetContact: contactName; affiliation; email
"John Smith; Harvard | Jane Doe; MIT","Contact; University; contact@email.com"
```

### Geographic Fields (Optional)

- `geographicCoverage` - Countries/regions covered (pipe-separated)
- `geographicUnit` - Geographic units (pipe-separated)

### Social Science Fields (Optional)

- `unitOfAnalysis` - Unit of analysis
- `universe` - Universe of study
- `timeMethod` - Time method
- `samplingProcedure` - Sampling procedure
- `frequencyOfDataCollection` - Collection frequency
- `collectionMode` - How collected (Online, In-person, etc.)

## 📤 Example Conversion

### Input CSV:
```csv
id,identifier,title,author: authorName; authorAffiliation,subject,keyword: keywordValue; keywordVocabulary,depositor,dateOfDeposit
1001,FK2/MYORG123,"Climate Study Dataset","Sarah Chen; MIT | Tom Brown; Harvard","Earth and Environmental Sciences","climate; | earth; ","Dr. Sarah Chen",2025
```

### Output JSON Structure:
```json
{
  "id": 1001,
  "identifier": "FK2/MYORG123",
  "persistentUrl": "https://doi.org/10.70122/FK2/MYORG123",
  "protocol": "doi",
  "authority": "10.70122",
  "publisher": "Dataverse",
  "publicationDate": "2025-12-11",
  "datasetType": "dataset",
  "datasetVersion": {
    "id": 2001,
    "datasetId": 1001,
    "versionNumber": 1,
    "versionState": "DRAFT",
    "license": {
      "name": "CC0 1.0",
      "uri": "http://creativecommons.org/publicdomain/zero/1.0",
      ...
    },
    "metadataBlocks": {
      "citation": {
        "fields": [
          {
            "typeName": "title",
            "value": "Climate Study Dataset"
          },
          {
            "typeName": "author",
            "value": [
              {
                "authorName": {"value": "Sarah Chen"},
                "authorAffiliation": {"value": "MIT"}
              },
              {
                "authorName": {"value": "Tom Brown"},
                "authorAffiliation": {"value": "Harvard"}
              }
            ]
          },
          ...
        ]
      }
    }
  }
}
```

## 🔄 Uploading to Dataverse

1. Navigate to your dataset in Dataverse
2. Click **"Edit Metadata"**
3. Use the JSON import feature to upload `output_metadata.json`
4. Or use the API: `POST /api/datasets/{id}/metadata`

## 🔄 Uploading to Borealis

1. In Borealis, create a new dataset
2. Use the **"Import Metadata"** feature
3. Select the JSON file
4. Map any required fields
5. Submit

## ✅ Validation

To verify your JSON is valid:

```bash
python -m json.tool output_metadata.json > /dev/null
echo "✓ JSON is valid!"
```

## 📝 Tips & Best Practices

1. **Always include title** - Required for upload
2. **Use pipe (`|`) for multiple values** - Not commas
   - ✅ `Social Sciences | Computer Science`
   - ❌ `Social Sciences, Computer Science`

3. **Use semicolon (`;`) for compound fields**
   - ✅ `John Smith; Harvard | Jane Doe; MIT`
   - ❌ `John Smith, Harvard`

4. **Dates can be YYYY or YYYY-MM-DD** - Converted automatically

5. **Controlled vocabulary** - Use standard terms from Dataverse
   - Subject, Language, etc.

6. **Leave system fields empty** - They'll be auto-generated if needed
   - `id`, `identifier`, `versionId`, etc.

7. **One row = one dataset** - Each CSV row creates one JSON file

## 📁 File Structure

```
csv_to_DDI_json/
├── csv_to_dataverse_json.py         ← Main converter
├── Csv_to_json - Citation.csv       ← Your input CSV
├── TEMPLATE_CSV_WITH_ALL_COLUMNS.csv ← Template with all fields
├── output_metadata.json             ← Generated output
├── output_template_test.json        ← Test output
├── samplemetadata.json              ← Reference sample
├── requirements.txt                 ← Dependencies
├── README.md                        ← Full documentation
├── UPDATE_SUMMARY.md                ← What changed
└── GETTING_STARTED.md              ← This file
```

## 🆘 Troubleshooting

### Error: "File not found"
- Check CSV filename matches in the Python script
- Use absolute paths if needed

### Error: "invalid literal for int()"
- Ensure numeric fields (id, versionNumber) don't have spaces or special characters
- Quote CSV values with commas: `"Smith, John"`

### Missing fields in output
- Check CSV header names exactly match field names
- Empty cells are skipped automatically

### JSON validation fails
- Ensure all quotes are matched
- Check for special characters in values

## 📞 Next Steps

1. ✅ Prepare your CSV using the template
2. ✅ Run `python csv_to_dataverse_json.py`
3. ✅ Validate with `python -m json.tool output_metadata.json`
4. ✅ Upload to Dataverse or Borealis
5. ✅ Success! 🎉

## 📚 Resources

- [Dataverse Metadata Documentation](https://guides.dataverse.org/)
- [Borealis Documentation](https://www.borealisdata.ca/)
- [DDI Standard](https://ddialliance.org/)
- [SPDX License List](https://spdx.org/licenses/)
