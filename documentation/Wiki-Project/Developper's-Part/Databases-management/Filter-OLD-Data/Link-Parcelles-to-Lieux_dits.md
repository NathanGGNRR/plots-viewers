# **WORK ITEM MENTION:**
#101

# First you need to have lieux_dits and parcelles data: 
**[GO TO "How to retrieve data from JSON Files"](https://dev.azure.com/Diiage2022DatabaseGROUPEBA/PlotsViewers/_wiki/wikis/PlotsViewers.wiki/40/How-to-retrieve-data-from-JSON-Files)**

# Why we do this:
In the file `cadastre_parcelle`, no information corresponds to the above-mentioned places. It is therefore not known where the plot is located.

So, the only rental information we have is geometry, we decided to use `ST_INTERSECTS()` to affected lieux_dits to parcels.

# Then when retrieve data:
![link_lieux_parcelles.png](/.attachments/link_lieux_parcelles-ceb984ec-7d53-4be1-9fb9-35e9aff7a519.png)

 Thanks to the procedure, we browse all parcelles then all lieux_dits and we check if their geometry crosses each other.

![intersect.png](/.attachments/intersect-10594da2-bc2d-451c-8be8-1b9b7d062203.png)

This line do the job. If intersect is equal to `t` correspond to `true`.