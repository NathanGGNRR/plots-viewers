# **WORK ITEM MENTION**:
#78

# Script to download data of internet 

Our goal was to download department by department each **commune**, **places** and **parcels**.
We decided to create a script that allowed us to get the download link for each file to do it automatically instead of manually (more than 100 departments).

To download all data of cadastre,
We use a script in batch, because it's more efficient.


```
cd C:\Users\maxime\Downloads\wget_1-19-2_en_11202_32
for /l %%v in (01,1, 95) do (
wget https://cadastre.data.gouv.fr/data/etalab-cadastre/2019-07-01/geojson/departements/%%v/cadastre-%%v-lieux_dits.json.gz
wget https://cadastre.data.gouv.fr/data/etalab-cadastre/2019-07-01/geojson/departements/%%v/cadastre-%%v-parcelles.json.gz
wget https://cadastre.data.gouv.fr/data/etalab-cadastre/2019-07-01/geojson/departements/%%v/cadastre-%%v-communes.json.gz
)
```
# RENDER:
![image.png](/.attachments/image-e4391d8c-9d3f-4ec2-b68a-c32290554365.png)
The CD command permit to locate the reception folder.
The for command permit to download all data in the tree of cadastre.
The wget command permit to download file with the URL Link.