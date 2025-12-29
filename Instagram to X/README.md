## Description

### How it works
* Runs on a schedule, checks Instagram API for the latest post, validates against the Data Table to see if it's already been shared, and if not, posts it to X and stores the ID so it doesn't post it again.

### Setup Steps
* Set your  credentials for **Bearer Auth** in the **HTTP Request** node
* Create and associate a Data Table in the **Get row(s)** node
  * Add a string field called **postId**
  * Select the same table for the **Insert row** node
* Set your X Credentials in the **Create Tweet** node

### Additional Links
* Getting Instagram Posts with N8N (The Simple Way Part 1)(https://medium.com/@mister.funable/getting-instagram-posts-with-n8n-the-simple-way-part-1-c66cb5217054)
* N8N Medium List (https://medium.com/@mister.funable/list/n8n-fc9fe38c3afe)