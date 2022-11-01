
# **WORK ITEM MENTION:**
#88
#98


# To show multi polygons, we use JS:
![image.png](/.attachments/image-5310961b-a02e-4185-9528-68cfb55c7b60.png)

Each element is independent of the others.
For example, we call the file "country" of the API to display countries borders and the file "department" to display departments borders.

# Search

By default, after clicking on search button, if the search bar isn't empty, we call with ajax an API file with parameter search.

![image.png](/.attachments/image-220de676-c888-4a6f-90d2-456f2a47bf6b.png)

If the request is successful, we call the function showPlots() that takes in parameters the map and the response (response contains all the informations of plots).
Else, we display a pop-up which states that no data has been found and the error code.

# Function showPlots :

The function removes old data and adds a new source with plots data.
Moreover, we add two layers : one that defines the filling of plots and one that defines borders.

![image.png](/.attachments/image-974e9fa6-0fe5-4ae8-89ba-15a6d54cda6a.png)

In case the user chooses to filter only by denomination or appellation, we call the same API file but with a different parameter.

![image.png](/.attachments/image-6edfad9f-4a76-4b76-b138-dbaa3fdb318c.png)

(here, it's in the case of filter denomination)