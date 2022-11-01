# **WORK ITEM MENTION:**
#38
#24
#80

**All data are already linked in other wiki page.**

# Script sample to select the data we are interested in old tables and put them in new tables :
![image.png](/.attachments/image-27c2ed2f-75fe-4fe2-b709-ac41d5b8d7fd.png)

Here it's for country table. It's the same for all tables : we create a cursor to take data and then we go through this cursor to insert first into `geometry_tb` the corresponding geometry and finally data into the correspondig table.

![image.png](/.attachments/image-577aa168-2f80-40b9-a46e-30209c99d0c4.png)

`CURRENT_TIMESTAMP` get the current date (format : YYYY-mm-dd HH:ii:ss.u)

the `returning` instruction allows to save the ID of inserted line in the variable created above.

![image.png](/.attachments/image-41cba036-d370-45e1-9f92-291306177efc.png)

To understand how a cursor works : **[GO TO "How to add commune"](/Wiki-Project/Developper's-Part/Databases-management/Check-Data/How-to-add-commune)** (at the end of the page).