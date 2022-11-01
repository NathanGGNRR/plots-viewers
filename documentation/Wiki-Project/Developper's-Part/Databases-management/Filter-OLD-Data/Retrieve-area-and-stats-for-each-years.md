# WORK ITEM MENTION :
#94

# Why we do this ?

This is a script which will get data from corresponding tables, so here there are a production table and a superficie table which will contain all data from the files corresponding to the following links.
In a first time it has been inserted in the database with one table for one file. But it make a lot of tables not really useful. So, to regroup data, we make those global tables.

# Treated data come from those links :

## Superficies :

[**Superficie 2017**](https://www.douane.gouv.fr/sites/default/files/openData/files/2017.07.31-total-par-commune-des-superficies-plantees-en-vigne-au-31-juillet-2017.xls) 

[**Superficie 2018**](https://www.douane.gouv.fr/sites/default/files/openData/files/2018.07.31-total-par-commune-des-superficies-plantees-en-vigne-au-31-juillet-2018.xls)

## Productions :

[**Production 2009**](https://www.douane.gouv.fr/sites/default/files/openData/files/2009-stats-recolte.xls) 

[**Production 2010**](https://www.douane.gouv.fr/sites/default/files/openData/files/2010-stats-recolte.xls) 

[**Production 2011**](https://www.douane.gouv.fr/sites/default/files/openData/files/2011-stats-recolte.xls) 

[**Production 2012**](https://www.douane.gouv.fr/sites/default/files/openData/files/2012-stats-recolte.xls) 

[**Production 2013**](https://www.douane.gouv.fr/sites/default/files/openData/files/2013-stats-recolte.xls) 

[**Production 2014**](https://www.douane.gouv.fr/sites/default/files/openData/files/2014-stats-recolte.xls) 

[**Production 2015**](https://www.douane.gouv.fr/sites/default/files/openData/files/2015-stats-recolte.xls) 

[**Production 2016**](https://www.douane.gouv.fr/sites/default/files/openData/files/2016-stats-recolte.xls) 

[**Production 2017**](https://www.douane.gouv.fr/sites/default/files/openData/files/2017-releve-par-departement-de-la-production-des-vins.xls) 

[**Production 2018**](https://www.douane.gouv.fr/sites/default/files/uploads/files/2019-06/2018-releve-par-departement-de-la-production-des-vins.xls) 

# To get data from original tables, those scripts are executed

## The first one is corresonding to tables production

In a first time, you need to declare variables and cursors which will be used.

![image.png](/.attachments/image-b8e8eb8b-955a-4efa-b773-38695b9bf330.png)

There are a lot of tables corresponding to it because we have each year that you can find above.
So there are one cursor/record for each table. Now let's see for the executed code.

![image.png](/.attachments/image-b195c3f2-dc06-4de8-bcf7-72f4d7f1c3f4.png)

There is an insert into query which will get only data corresponding to the content of the original table. So to get specific data and set the other values as NULL. Then we add year corresponding to the table manually.
There is one loop for each cursor so for each table.

## The second one is corresponding to tables superficie

This is the same thing than for productions, but we use one more variable :

![image.png](/.attachments/image-91fdeaac-c761-472b-a06d-67a5ced3b27a.png)

This new variable will allow us to correct a problem of INSEE because in this table an INSEE contain 6 characters but in other tables there are only 5 characters. So we add a 0 between the second and the third character.

![image.png](/.attachments/image-5638fbc6-3177-45a2-b800-178087cd6819.png)