# **WORK ITEM MENTION:**
#84
#57
#58
#59

# First, you will have to connect to the database :
To do that, I decided to create a function.

![image.png](/.attachments/image-0cbafa8d-784a-4a70-8cae-e9ad1c39b09f.png)

To connect only one time, we have the condition `($this->conn == null)`.
To call this function, you only have to create an object corresponding to the class and call it function :

![image.png](/.attachments/image-58192aa5-4f60-408a-9b28-13ec3d6c3e4f.png)


# Then, to make requests to the database:
To ask a basic request to the database (with a LIMIT to dodge overcharges), we can do it like this :

![image.png](/.attachments/image-88bd15de-33d4-4c25-b96d-8fdcbe3d0c41.png)

# Afterwards, stock the received data:

Received data is stock in objects corresponding to Plots or Cadaster, those objects are instantiated like the database :

![image.png](/.attachments/image-f3c43dcd-d9bf-4e9e-a125-c8165198909d.png) 

When this object is initialized, we will get data from database and while data is found, an item will be created for each data found and will be stocked in an array. This is stocked in an array which is printed to show JSON data.

![image.png](/.attachments/image-f109cc40-6ef7-40de-abca-cbec0c5f9f16.png)

The corresponding class is the following one :

![image.png](/.attachments/image-356674d1-93ab-4662-abdb-9c65a7d3ed30.png)

We have all the properties corresponding to the data table.

# To make a research, there are many steps :

- First, it will be a GET method which will see if the client added something to the URL.
Here, there is "appellation" because this request is only to search an "appellation".

![image.png](/.attachments/image-b4770a78-3581-4d58-b697-c9b8afbd05ab.png)

- To make a research with a value entered by the client, it will get a specific element from the URL :

http://10.5.3.1/API/Plots/Search.php?appellation=Ajaccio

The selected element is the string which follows the `=` , so here it is Ajaccio.

- After that, we call the following function :

![image.png](/.attachments/image-7326695a-157f-4f00-b3ff-2781d55755c9.png)

A copy of keywords is realized to be reused later.
And we have the query corresponding to the appellation only (temporary) and will search with $keywords.

But if we let it like this, an error will occur, `Object of class could not be converted to string`.
So there is the `function __toString` which will solve this error.

