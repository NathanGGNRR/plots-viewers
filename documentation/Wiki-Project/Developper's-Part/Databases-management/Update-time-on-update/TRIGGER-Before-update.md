# **WORK ITEM MENTION:**
#65

# Create trigger on the table:

![trigger.png](/.attachments/trigger-7b8a4545-3df9-4040-9297-14f4fdf0f10e.png) 

The first line corresponds to the creation of the trigger, when it is executed and on which action. Here the trigger is called `trigger_update_time_communes` and it's triggered before update.
`public."cadastre-communes"` is the table to which the trigger is assigned.
For each row, the trigger execute the procedure `update_time_communes()`.
