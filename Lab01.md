# SQL Deployment

1. Create a SQL database project named 'DemoDatabase'.

2. Add below folders and files.
  ```
    - dbo
      - Tables
          - Users.sql (Col: Id, Name)
          - DemoDatabaseVersion.sql  (Col: Id, Desc)
      - StoredProcedures
          - SPGetAllUsers.sql
  ```
  
3. Create an Azure SQL database.
  
4. Set up YAML pipeline for database deployment.
 
5. Create another SQL project named 'DemoDatabase.Migration'
 
6. Add below files and folders
    ```
      - Script
        - v001
          - Script1.sql (Not in build)
    ```
    
7. Add a postdeployment file.

8. Insert a record in Users table in DemoDatabase with values (Id: 1, Name: 'Bob').

9. Add a new NOT NULL column 'Department' to Users table.

10. Try to deploy DemoDatabse on server.

11. It will fail because of new schema changes.

12. Update script/v001/script1.sql from DemoDatabase.Migration project with below code.
  ```
    ALTER TABLE Users ADD Department VARCHAR(100) NULL

    EXEC sp_executesql N'Update Users SET Department = ''Dept'''

    ALTER TABLE Users ALTER COLUMN Department VARCHAR(100) NOT NULL
  ```

13. Update Script.Post deployment with below code:
  ```
    DECLARE @currentDBVersion BIGINT = NULL;
    -- get max version from version table

    IF @currentDBVersion is null goto script1
    else if @currentDBVersion = 1 goto script2
    else return

    script1:
    :r.\scripts\v001\script1.sql
    -- insert an entry into version table 

    Script2:
    :r.\scripts\v002\script1.sql
    -- insert an entry into version table 
  ```
13. Update your deployment pipeline by adding another stage `DeployMigration` before Database deployment.

14. Deploy `DemoDatabase.Migration` under `DeployMigration` stage in your pipeline.

15. Run your end to end multistage deployment pipeline.
