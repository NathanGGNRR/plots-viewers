# **WORK ITEM MENTION**:
#84
#76
#33

# For retrieve all data of all departement from france:
**[DEPARTEMENT OF FRANCE](https://cadastre.data.gouv.fr/data/etalab-cadastre/2019-07-01/geojson/departements/)**

# Why we do this:
As explained in the wiki of the script to retrieve the files from the internet, we had to retrieve each file department by department.

With this in mind we thought of creating a procedure with the URL of the folder containing all the files sorted by department as parameter.

![urlfolder.png](/.attachments/urlfolder-5dd543e8-0b07-4437-9926-df02a01e860f.png)
![folderpath.png](/.attachments/folderpath-39179622-b9b7-42fe-977e-9acdde8bcff7.png)

#We retrieve all data of JSON Files with this method.

# First step: Declare variable:
![declarecadastre.png](/.attachments/declarecadastre-3f6c809d-5f33-46fa-b704-428bf1f09d99.png)
Counter variable start at **1** and will finish at **976**. Counter corresponding to the number of each departement.
Counter_corse variable start at **1** and will finish at **2**. **1** corresponding to **2A** and **2** to **2B**.

Each url_entire corresponding to text concat more later :
![url_entire.png](/.attachments/url_entire-4ebf58a4-58f2-4c99-bf4d-26ac2712d459.png)

`commune_lieux_dits` and `commune_parcelles` variables are affected a every communes thanks to the cursor `curs_commune_lieux_dits` and `curs_commune_lieux_parcelles`.
This cursor contains all communes. See below for more details about cursor.

# Second step: Create temporary table for every table:
Three table :
- `cadastre_communes`
- `cadastre_lieux_dits`
- `cadastre-parcelles`

For this three tables, we do that:

![createtemporarycadastre.png](/.attachments/createtemporarycadastre-d180d777-8fbc-4a44-b58e-b029c64df29f.png)

We create a temporary table to stock each row of the json files.
For example:

`{"type":"Feature","id":"01008","geometry":{INFO GEOM},"properties":{"id":"01008","nom":"AMBUTRIX","created":"2005-06-20","updated":"2017-02-08"}}`
Every row corresponding to 1 occurrence.

A temporary table is a table for stock data and must be deleted after use. (ON COMMIT DROP)

COPY temp_json_communes_france(values) allows to put data of the file into the temporary table.

# Third step: filter data of temporary table:
![filtercadastre.png](/.attachments/filtercadastre-11196e2c-d6d5-43bc-abab-942a5e3dea45.png)
Delete first row is used for delete the first row of json files :
`{"type":"FeatureCollection", "features": [`
All UPDATE Request are used for delete virgule , at every end of row, delete ]} at the last row and finally the last update request is used for remove or update all escape character.

# Four step: browser all row of json file:
See the wiki **[How to check add commune](/Wiki-Project/Developper's-Part/Databases-management/Check-Data/How-to-add-commune)**

# Five step: Insert Into Table
![insertinto.png](/.attachments/insertinto-7c2c4ff0-e6e8-4281-ab97-382e24e4870e.png)
We insert all data of the temporary table into the target table.
All data are selected and filtered.

# Finally check if data are valid:
See the wiki **[How to check if geometry data are valid](/Wiki-Project/Developper's-Part/Databases-management/Check-Data/How-to-check-if-geometry-data-are-valid)**

## In the database, all the column are selected and we call all the tables where the data of files is located `"contentold"`.