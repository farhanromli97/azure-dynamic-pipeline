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
a. Create a SQL Server and Database
 - Under 'Additional settings' tab, select 'Sample'
<img width="627" alt="image" src="https://github.com/user-attachments/assets/3d06f77d-5e09-4bec-9f5b-4860d70e9066" />

b. Create a storage account

c. Create an Azure data factory

### 2. 


```sql
CREATE USER [YourDataFactoryName] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [YourDataFactoryName];
```

## Architecture Diagram

<img width="663" alt="image" src="https://github.com/user-attachments/assets/167d2cb2-d626-469c-af39-ba0845bf3c4b" />


## Resources

- [DP-203: 11 - Dynamic Azure Data Factory](https://www.youtube.com/watch?v=BSQ8rRZUno0&list=PLuQSde7Xvu7DCRenR1otgxAplTtnzKO9e&index=12&ab_channel=TybulonAzure)
