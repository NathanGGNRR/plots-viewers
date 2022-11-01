# **WORK ITEM MENTION**:
#36
#86
#37

# First you need to know ST_IsValid() and ST_MakeValid() functions: 

ST_IsValid : **[https://postgis.net/docs/ST_IsValid.html](https://postgis.net/docs/ST_IsValid.html)**
ST_MakeValid : **[https://postgis.net/docs/ST_MakeValid.html](https://postgis.net/docs/ST_MakeValid.html)**

# Why we do this:
Sometimes the geometric data are not in the right format or its invalid. It was therefore necessary to find a way to correct this error. We found the `ST_IsValid()` and `ST_MakeValid()` functions of the PostGIS extension.

# Then you can update values:
![makevalidcommunes.png](/.attachments/makevalidcommunes-64ac08af-3c6a-458c-8874-cea6896dfd23.png)
We update the value if `ST_IsValid(geom)` is equivalent to `true`. The `ST_MakeValid()` function return Geometry value as MultiPolygon.

# For table `cadastre-lieux_dits` and table `cadastre-parcelles`
![makevalid.png](/.attachments/makevalid-17d35e2b-b951-4d39-bd41-5b9d1479809c.png)
It's the same that table `cadastre-communes` but `ST_SetSRID(ST_PolygonFromText(ST_AsText(ST_CollectionExctract(ST_MakeValid(),3))))` allows to Convert MultiPolygon to Simple Polygon, the CollectionExtract parameter 3 correspond to Polygon format.
