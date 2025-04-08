# Azure: Dynamic Pipeline

## Introduction

This project documents how to create a dynamic pipeline in Azure Data Factory by the use of parameters. 
This project uses Azure SQL Database as source and Azure Data Lake Storage Gen 2 as target.

## Services Used

- Azure Data Factory
- Azure SQL Database
- Azure Data Lake Storage Gen 2
- Azure Key Vault

## Getting Started

### 1. Provisioning Azure Resources
- Create a SQL Server and Database
  - Under 'Additional settings' tab, select 'Sample'
<img width="627" alt="image" src="https://github.com/user-attachments/assets/3d06f77d-5e09-4bec-9f5b-4860d70e9066" />

- Create a storage account
- Create an Azure data factory

### 2. Setting up SQL Database
- Execute below query
```sql
CREATE USER [DataFactoryManagedIdentity] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [DataFactoryManagedIdentity];
```

### 3. Setting up Key Vault
- Assign Key Vault Administrator role to yourself
- Create a secret to store the storage account access key
- Assign Key Vault Secrets User role to Data Factory Managed Identity

### 4. Configuring Linked Services in Data Factory
- Create a Linked Service to the Azure Key Vault
- Create a generic Linked Service to the Azure SQL Database
  - Create a managed virtual network Azure Integration runtime 
  - Create parameters for the SQL server and database name
![image](https://github.com/user-attachments/assets/38fb5695-3368-45bb-a392-1e1cfd5048f7)

- Create a managed private endpoint to the Azure SQL Database and approve it
<img width="456" alt="image" src="https://github.com/user-attachments/assets/393789c0-f321-4a39-9c89-efe853691771" />


- Create a generic Linked Service to the Data Lake Storage Gen 2
  - Create a parameter for the storage account name and use an expression to create the storage acccount URL
  - Select Azure Key Vault and choose the correct secret
![image](https://github.com/user-attachments/assets/d8c26eb7-9fed-44a5-b1f4-a3fe9b7b508a)

### 5. Configuring Datasets in Data Factory
- Create a generic dataset for the SQL Database
  - Create parameters for the SQL server and database name
![image](https://github.com/user-attachments/assets/31c80aee-4ec5-4627-86c3-6809ca2c332b)

- Create a generic dataset for the Data Lake Storage Gen 2
  - Create a parameter for the storage account name
  - For the path file, create parameters accordingly. Example below:

  a. subdirectory path
   ```adflang
    @concat(
    dataset().serverName,
    '/',
    dataset().databaseName,
    '/',
    dataset().schemaName,
    '/',
    dataset().tableName,
    '/Year=',
    formatDateTime(utcNow(), 'yyyy'),
    '/Month=',
    formatDateTime(utcNow(), 'MM'),
    '/Day=',
    formatDateTime(utcNow(), 'dd')
    )
   ```
  b. target file name
   ```adflang
   @concat(dataset().tableName,'.csv')
   ```

![image](https://github.com/user-attachments/assets/12356354-8296-4109-8b2c-becc23116ae0)

### 6. Configuring Pipeline in Data Factory
- Create a Lookup activity
- Create a ForEach activity
- Link the two activities on success
- Create parameters as seen below

![image](https://github.com/user-attachments/assets/4c483cea-5ec3-4257-a3d9-b827201da4e4)


### 7. Setting up the Lookup activity
- Set the generic SQL Database dataset as the source dataset
- Pass below query
```sql
SELECT t.name AS tableName, 
SCHEMA_NAME(t.schema_id) AS schemaName
FROM sys.tables as t
```
![image](https://github.com/user-attachments/assets/8b183938-c8a6-4b91-bb38-f5d577b929ae)

### 8. Setting up the ForEach activity
- Set up the Items in the settings as seen below

![image](https://github.com/user-attachments/assets/bdd73285-dc34-4d0f-bd65-22515785efe0)

- Create the Copy activity

![image](https://github.com/user-attachments/assets/4414adf8-8a91-4dc7-9c87-3f24ad16f67f)

### 9. Setting up the Copy activity
- Set the generic SQL Database dataset as the source dataset
  - Pass below query
    ```adflang
    @concat('SELECT * FROM ', item().schemaName, '.', item().tableName)
    ```   
![image](https://github.com/user-attachments/assets/c6197363-5979-4eff-b3e4-41cfe73276ab)

- Set the generic Data Lake Gen 2 dataset as sink dataset

![image](https://github.com/user-attachments/assets/b3a29379-e55c-4c0e-bd14-0720646c7316)

## Architecture Diagram
<img width="663" alt="image" src="https://github.com/user-attachments/assets/167d2cb2-d626-469c-af39-ba0845bf3c4b" />

## Resources

- [DP-203: 11 - Dynamic Azure Data Factory](https://www.youtube.com/watch?v=BSQ8rRZUno0&list=PLuQSde7Xvu7DCRenR1otgxAplTtnzKO9e&index=12&ab_channel=TybulonAzure)
