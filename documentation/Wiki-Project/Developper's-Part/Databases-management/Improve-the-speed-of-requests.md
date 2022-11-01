# **WORK ITEM MENTION:**
#103

# Why we do this:
When working on very large data, the slightest request can take several hours. Several solutions exist to accelerate them. We have chosen to use indexes to make queries faster. Other solutions are not necessary for our data.
**[Utilization of INDEX](https://www.postgresql.org/docs/9.1/sql-createindex.html)**
**[List of optimization](https://wiki.postgresql.org/wiki/Performance_Optimization)**

# First, create index on the necessary column:
![index.png](/.attachments/index-b30b7669-89ad-4ad8-a6f7-6120708cf059.png)

`USING gist(geom)` corresponds to putting an index on the geometry type of the postgis extension.

# Example:
## With index:
![withindex.png](/.attachments/withindex-51b2ebc9-8bd1-4ffa-855a-e4a975273f0c.png)
As can be seen, the request takes **18 milliseconds** to find **63 lines**.

## Without index:
![withoutindex.png](/.attachments/withoutindex-5aea6ef1-60d2-47eb-b135-145b60a5e3ef.png)
As can be seen, the request takes **26 milliseconds** to find **63 lines**.

This example is functional but a query with more data would be more representative on time saving. For time reasons, we have decided to show you the use of the index with a simpler query.

#We also regularly use **`VACUUM`** and **`ANALYZE`** into the database to remove obsolete data.