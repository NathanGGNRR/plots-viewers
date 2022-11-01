# **WORK ITEM MENTION**:
#86

# For add all the commune of france with this link: 
**[COMMUNE OF FRANCE](https://cadastre.data.gouv.fr/data/etalab-cadastre/2019-07-01/geojson/france/)**

# Why we do this:
Sometimes, in a department's locality file, a locality referred to a locality that did not exist in the locality file and therefore was not stored in the database.

To counter this, we downloaded the file of all the communes in France and then when a commune was not found we added it thanks to this procedure.

# Then create procedure calling when communes is missing:
![proccommune.png](/.attachments/proccommune-06fb37d3-784f-4d1b-9f59-3dddf928ec21.png) 

So, we create procedure when communes is missing for `cadastre-lieux_dits` and `cadastre-parcelles`.
The first parameter of the procedure correspond to the local url where `communes.json` is located.
The second correspond to the id of the missing commune.

# First step: Declare variable:
![declare.png](/.attachments/declare-44bed3d6-a49e-4e60-95e3-9cfc524e38d0.png)

First variable is affected a every communes thanks to the cursor `curs_commune_france`.
This cursor contains all communes. See below for more details about cursor.

# Second step: Create temporary table:

![createtemporary.png](/.attachments/createtemporary-6cb1a551-247c-4610-8dee-a0b632f4a547.png)

We create a temporary table to stock each row of the json files. 
For example:

```
{"type":"Feature","geometry":{"INFO ABOUT GEOM"},"properties":{"insee":"97223","nom":"Saint-Esprit","wikipedia":"fr:Saint-Esprit (Martinique)","surf_ha":2318}}
{"type":"Feature","geometry":{"INFO ABOUT GEOM"},"properties":{"insee":"97233","nom":"Le Morne-Vert","wikipedia":"fr:Le Morne-Vert","surf_ha":1325}}
```
Every row corresponding to 1 occurrence.

A temporary table is a table for stock data and must be deleted after use. (`ON COMMIT DROP`)

`concat(url,'\commune-france\communes.json', '''')` allows to concatenate two text. Here the parameter text corresponding to the url and end of the path of the files where contains all communes.
`''''` correspondig to a text like `'hello'` but with only one single quote. For put single quote into text variable, we must put an other single quote before.
`COPY temp_json_communes_france(values)` allows to put data of the file into the temporary table.

# Third step: filter data of temporary table:
![filter.png](/.attachments/filter-bb9f0616-ca92-41ff-9548-004b39509b82.png)
Delete first row is used for delete the first row of json files :
`{"type":"FeatureCollection", "features": [`
All UPDATE Request are used for delete virgule `,` at every end of row, delete `]}` at the last row and finally the last update request is used for remove or update all escape character.

# Last step: browser all row of json file:
![cursor.png](/.attachments/cursor-83b21480-b8ad-42a9-9cdb-6dc18640e945.png)
Like you can see, we open the cursor `curs_commune_france`, create loop and fetch the next row at every loop.
We browser all row for search id of communes go through parameter thanks to FETCH.
If result is found, we add the commune to the table `cadastre-communes`.
To finish, we close the loop and the cursor.

