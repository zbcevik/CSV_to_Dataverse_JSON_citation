# CSV to Dataverse DDI JSON Converter

Convert CSV metadata files to Dataverse/Borealis-compatible DDI JSON format.

## Installation

1. **Install required dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

## Usage

### Basic Usage

1. **Prepare your CSV file** with the proper column headers (see CSV Format section below)

2. **Run the conversion script:**
   ```bash
   python csv_to_dataverse_json.py
   ```

   This will:
   - Read `Csv_to_json - Citation.csv`
   - Generate `output_metadata.json`

### Custom File Paths

Edit the main execution section in `csv_to_dataverse_json.py`:

```python
if __name__ == "__main__":
    csv_input = 'your_input_file.csv'      # Change input filename
    json_output = 'your_output_file.json'   # Change output filename
    
    csv_to_dataverse_json(csv_input, json_output)
```

Then run:
```bash
python csv_to_dataverse_json.py
```

## CSV Format

Your CSV should have the following column headers for citation metadata:

### Basic Fields
- `title` - Dataset title
- `subtitle` - Dataset subtitle
- `alternativeTitle` - Alternative title (use `|` for multiple values)
- `notesText` - Additional notes
- `language` - Language (use `|` for multiple; controlled vocabulary)

### People/Organizations (Compound Fields)
Use semicolon (`;`) to separate subfields and pipe (`|`) for multiple entries:

- `author: authorName; authorAffiliation` 
  - Example: `John Smith; Harvard University | Jane Doe; MIT`

- `datasetContact: datasetContactName; datasetContactAffiliation; datasetContactEmail`
  - Example: `Contact Person; University; contact@email.com`

- `producer: producerName; producerAffiliation; producerAbbreviation`
  - Example: `Statistics Canada; Statistics; StatCan`

- `contributor: contributorType; contributorName`
  - Example: `Researcher; John Smith`

### Description Fields
- `dsDescription: dsDescriptionValue; dsDescriptionDate`
  - Example: `This is a dataset description; 2025-01-01`

### Keywords & Subjects
- `subject` - Subject category (use `|` for multiple; controlled vocabulary)
  - Example: `Social Sciences | Medicine, Health and Life Sciences`

- `keyword: keywordValue; keywordVocabulary`
  - Example: `sample; | test; | data`

- `topicClassification: topicClassValue; topicClassVocab`
  - Example: `survey; | statistics;`

### Dates
- `productionDate` - Year produced
- `distributionDate` - Year distributed
- `dateOfDeposit` - Deposit date
- `timePeriodCovered: timePeriodCoveredStart; timePeriodCoveredEnd`
  - Example: `2025-01-01; 2025-12-31`

- `dateOfCollection: dateOfCollectionStart; dateOfCollectionEnd`
  - Example: `2024-01-01; 2024-12-31`

### Data Information
- `kindOfData` - Type of data (use `|` for multiple)
  - Example: `Quantitative | Qualitative`

- `software: softwareName; softwareVersion`
  - Example: `R; 4.0.0`

- `series: seriesName; seriesInformation`
  - Example: `Annual Statistics; Volume 5`

### Related Materials
- `relatedMaterial` - Related materials (use `|` for multiple)
- `relatedDatasets` - Related datasets (use `|` for multiple)
- `otherReferences` - Other references (use `|` for multiple)
- `dataSources` - Data sources (use `|` for multiple)

### Source Information
- `originOfSources` - Origin of sources
- `characteristicOfSources` - Characteristics of sources
- `accessToSources` - Access to sources

### Publication Information
- `publication: publicationRelationType; publicationCitation; publicationIDType; publicationIDNumber`
  - Example: `IsCitedBy; Smith et al. 2024; DOI; 10.1234/example`

### Funding & Distribution
- `grantNumber: grantNumberAgency; grantNumberValue`
  - Example: `NSERC; GRANT-12345`

- `distributor: distributorName; distributorAffiliation; distributorAbbreviation; distributorURL`

### Other Fields
- `depositor` - Person depositing the dataset
- `doi` - Digital Object Identifier (optional, for reference)
- `citation` - Full citation (optional, for reference)

## Example CSV Entry

```csv
title,author: authorName; authorAffiliation,subject,keyword: keywordValue; keywordVocabulary,dsDescription: dsDescriptionValue; dsDescriptionDate
"My Research Dataset","John Smith; University of Toronto | Jane Doe; MIT","Social Sciences | Computer Science","research; | data; | survey;","This dataset contains survey responses; 2025"
```

## Output JSON Format

The script generates Dataverse-compatible DDI JSON with the following structure:

```json
{
  "metadataBlocks": {
    "citation": {
      "displayName": "Citation Metadata",
      "name": "citation",
      "fields": [
        {
          "typeName": "title",
          "multiple": false,
          "typeClass": "primitive",
          "value": "My Research Dataset"
        },
        {
          "typeName": "author",
          "multiple": true,
          "typeClass": "compound",
          "value": [
            {
              "authorName": {...},
              "authorAffiliation": {...}
            }
          ]
        }
      ]
    }
  }
}
```

## Importing into Dataverse/Borealis

1. **In Dataverse:**
   - Go to your dataset → Edit → Metadata
   - Use the JSON API endpoint or direct metadata upload
   - POST the generated JSON to: `/api/datasets/{id}/metadata`

2. **In Borealis:**
   - Follow similar import procedures for DDI JSON formats

## Troubleshooting

### "File not found" error
- Ensure your CSV file is in the same directory as the script, or provide the full path

### Missing or incorrect values in output
- Check that your CSV headers exactly match the field names
- Verify semicolon and pipe delimiters are used correctly
- Empty cells are automatically skipped

### Date format issues
- Dates are converted to year-only format (YYYY) by default
- Ensure dates contain a valid 4-digit year

## File Structure

```
csv_to_DDI_json/
├── csv_to_dataverse_json.py      # Main conversion script
├── Csv_to_json - Citation.csv    # Input CSV file
├── output_metadata.json           # Generated output (after running)
├── requirements.txt               # Python dependencies
├── README.md                      # This file
└── samplemetadata.json            # Reference sample output
```

## License

This project is open source and available for research and data curation purposes.
