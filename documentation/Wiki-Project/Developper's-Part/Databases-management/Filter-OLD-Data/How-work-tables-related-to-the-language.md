# **WORK ITEM MENTION:**
#100

# First, download data of different translations:
**[TRANSLATIONS](/.attachments/sql-pays-73b7aa28-a142-47cf-bf02-d9e2b6ca62a8.csv)**

# The relationship between language and translation is done using 3 tables

# **First Table**
=> The "Langue" table 
![image.png](/.attachments/image-1dd29e5b-2467-456e-8f9d-0f8d89d45c82.png)

This table contains all the languages available for translation.
![image.png](/.attachments/image-c0bf7e66-52db-4837-864e-9368076fae06.png)
Here, we have 10 languages available, so 10 translations available.


# **Second Table**
=> The "translation" table
![image.png](/.attachments/image-4c23e8da-d70a-4057-905b-209657dd11f4.png)

This table contains all translations in all languages contained in the "Language" table
![image.png](/.attachments/image-b71eced7-2e6f-48a4-8ac7-31ec10829f0b.png)


# **Third Table**
=> The "Link_Translation" Table
![image.png](/.attachments/image-2a7ad535-b2aa-4268-9a65-b0313a69f415.png)

This table contains all the translation links between the Language table and the Translation table. It allows you to link these tables and thus translate them into different languages.
![image.png](/.attachments/image-07176560-46c0-42b9-970f-7f644d397a62.png)

----------------

# **STEP 1**
in order to translate a word into different languages, the language table is directly linked with its ID which corresponds to the language code.

# **STEP 2**
This ID is used by the "Link_Translation" table to designate the transduction in question. That is, according to the ID of the initial language, the translation of the word into the final language (target language).

# **STEP 3**
once the link is made with this table, the "Link_Translation" table uses the "Language" table to translate the word. It contains all the translations of the languages contained in the "Languages" table.
Thus, with the ID of the target language, it searches with the ID_translation for correspondence to this word in this language.

