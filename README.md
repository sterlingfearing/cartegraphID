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

## Create in ArcPro
Follow the steps below to generate the Cartegraph ID field in ArcPro for buildings and structures 
1. Add a field called "CartegraphID" to the attribute table
2. Identify the abbreviated park name from excel file
   Note: Code will need to be edited for park of interest
4. Import attribute rule:
   - Right click on layer in contents pane
   - Select Data Design -> Attribute Rules
   - Select Import Rules
   - Navigate to PGR -> _FAMS -> attributeRules -> cartegraphID.csv
5. Edit expression based on park abbreviation


