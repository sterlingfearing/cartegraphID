# Generate Cartegraph ID Field in ArcPro

## Cartegraph ID's
Cartegraph ID's are unique identifiers used in Open Gov's Facility Asset Management Software.

Five components separated by underscores
1. Park name abbreviation- can be found in excel file FAMSParkGrounds_PY
2. Building or Structure identifier- written as Bldg or Struc
3. Functional name of asset
4. Campsite number (if applicable)
5. Location ID- based on lat and long of centroid, first four digits following the decimal place of each.
   Ex. 30.4987243 N 98.8187818 W is written as 4987_8187

## Arcade Expression
Note: Park abbreviations and LoCodes must be added or updated for given park
```// Define abbreviation for Park Code
var loCode = $feature["LoCode"];
var parkAbbr = "";

if (IsEmpty(loCode)) {
    return "Error: Park Code is missing";
}

if (loCode == "P151") {
    parkAbbr = "DvlRNort";
} else if (loCode == "P119") {
    parkAbbr = "ERock";
} else {
    parkAbbr = Text(Left(loCode, 4));
}

// Determine Bldg or Struc
var bldgStruc = $feature["BldgStruc"];
var secondPart = "";

if (IsEmpty(bldgStruc)) {
    return "Error: Building or Structure type is missing";
}

if (bldgStruc == "Building") {
    secondPart = "Bldg";
} else if (bldgStruc == "Structure") {
    secondPart = "Struc";
}

// Get third part from Functional Name and remove spaces
var functionalName = $feature["NameFunction"];
var thirdPart = "";
if (!IsEmpty(functionalName)) {
    thirdPart = Replace(functionalName, " ", "");
}

// Calculate the centroid of the polygon
var centroid = Centroid($feature);

// Extract the longitude and latitude of the centroid
var longitude = Abs(centroid.X); // Ensure positive value
var latitude = Abs(centroid.Y);  // Ensure positive value

// Format the latitude and longitude to get the first four digits after the decimal place
var latStr = Text(Floor(latitude * 10000) % 10000, '0000');
var longStr = Text(Floor(longitude * 10000) % 10000, '0000');

// Combine latitude and longitude parts
var uniqueValue = Concatenate(latStr, "_", longStr);

// Check for SiteNumber
var siteNumber = $feature["SiteNumber"];
var sitePart = "";
if (!IsEmpty(siteNumber)) {
    sitePart = "_" + siteNumber;
}

// Combine for final ID
var CartegraphID = Concatenate(parkAbbr, "_", secondPart, "_", thirdPart, sitePart, "_", uniqueValue);

// Return final ID
return CartegraphID;
```

## Create in ArcPro
Follow the steps below to generate the Cartegraph ID field in ArcPro for buildings and structures 
1. Add a field called "CartegraphID" to the attribute table
   - Data Type = Text
   - Length = 50 character max
3. Identify the abbreviated park name from excel file
   Note: Code will need to be edited for park of interest
4. Import attribute rule:
   - Right click on layer in contents pane
   - Select Data Design -> Attribute Rules
   - Select Import Rules
   - Navigate to PGR -> _FAMS -> attributeRules -> cartegraphID.csv
5. Edit expression based on park abbreviation- change loCode and parkAbbr or add else if statement 

```
// Define abbreviation for Park Code
var loCode = $feature["LoCode"];
var parkAbbr = "";

if (IsEmpty(loCode)) {
    return "Error: Park Code is missing";
}

if (loCode == "P151") { 
    parkAbbr = "DvlRNort";
} else if (loCode == "P119") {
    parkAbbr = "ERock";
} else {
    parkAbbr = Text(Left(loCode, 4))
```
Note that attribute rule will apply to new data added to table. 

## Generate cartegraph ID's for existing data 
1. Copy expression from attribute rule
2. Right click CartegraphID field in buildings attribute table
3. Select Calculate Field
4. Verify
  - Input table
  - Field Name = CartegraphID
  - Expression type = Arcade
5. Edit loCode and park abbreviation if needed
6. Apply
