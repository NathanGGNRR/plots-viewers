# **WORK ITEM MENTION:**
#90

# First you need to have appellation's area and all appellation's name: 
**[APPELLATION'S AREA](https://www.data.gouv.fr/fr/datasets/r/ad6e471e-8b53-4151-b37e-7ce5a8fe868f)**
**[APPELLATION'S NAME](/.attachments/Export%20appellation%20hiérarchie%20vignoble-3e80f322-b217-461c-bd6a-27952edff262.csv)**

# Why we do this:
The client requested to have the parcel boundaries. In the file `APPELLATION'S AREA`, there is several information including appellation's name like `Côte de Beaune` however there is denomination's name which is more accurate like `Santenay Premier Cru`. This means that denominations are included in appellations.

We therefore decided to create a table denominations linked to the area of appellations and then a table apellations linked to the denomination.

Knowing that in the name file there are just delimitation's name file and in the other file, there are denomation's name and appellation's name.

We decided to create script to link both appellations informations.


# Then when retrieve data:

![link-denomination-appellation.png](/.attachments/link-denomination-appellation-f2dee384-0b82-45fa-91da-32740b629a86.png)

We check if appellation is already in the database, if this is not the case we add it.
After that, we check correspondence between denominations and appellations with the `WHERE` clause and we affected `id_appellation`.