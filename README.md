# Azure-Key-Vault

<!-- <a href="https://www.flaticon.com/free-icons/c-sharp" title="c sharp icons">C sharp icons created by Iconika - Flaticon</a>
<a href="https://www.flaticon.com/free-icons/password" title="password icons">Password icons created by Freepik - Flaticon</a>-->

## Introduction

## Objective
In this lesson, we will learn how to create a proof of concept application that utilizes Azure SQL Database's Always Encrypted functionality. We will store the encryption keys and secrets in Azure Key Vault and register the application in Azure Active Directory to enhance security. The lesson will cover the following exercises:

- Exercise 1: Deploy the base infrastructure from an ARM template
- Exercise 2: Configure the Key Vault resource with a key and a secret
- Exercise 3: Configure an Azure SQL database and a data-driven application
- Exercise 4: Demonstrate the use of Azure Key Vault in encrypting the Azure SQL database

<details>
  
 <summary> 

### Exercise 1: Deploy the base infrastructure from an ARM template

  </summary>   
   
In this exercise, we will deploy an Azure virtual machine (VM) and an Azure SQL database using an ARM template. The VM will be pre-configured with Visual Studio 2019 and SQL Server Management Studio 2018.

- Sign in to the Azure portal using an account with Owner or Contributor role in the target subscription. 
- Search for "Deploy a custom template" in the Azure portal and select the option to build your own template in the editor. <br />
- Load the ARM template file provided in the lab resources. <br />
- Save the template and configure the deployment settings, such as the subscription, resource group, location, and admin credentials.<br />
- Review the settings and click Create to start the deployment.<br />

  >**Note**: that the deployment may take around 20-25 minutes to complete. 
  
  </details>
  
  #
  
  <details>
  
  <summary>

### Exercise 2: Configure the Key Vault resource with a key and a secret
    
  </summary> 

In this exercise, we will create and configure an Azure Key Vault resource. We will also add a key and a secret to the Key Vault.

- Open the Azure Cloud Shell and select PowerShell.<br />
- Run the provided PowerShell commands to create an Azure Key Vault in the specified resource group.<br />
- Take note of the Key Vault name and URI.<br />
- In the Azure portal, navigate to the Key Vault resource and configure access policies to allow key, secret, and certificate management.<br />
- Add a key to the Key Vault using the PowerShell command provided and verify its creation.<br />
- Add a secret to the Key Vault using the PowerShell command provided and verify its creation.<br />
  
  </details> 
  
  #
  
  <details>
  
  <summary>

### Exercise 3: Configure an Azure SQL database and a data-driven application
    
  </summary>

In this exercise, we will enable a client application to access the Azure SQL Database service and configure the necessary permissions.

- Register an application in Azure Active Directory (AD) to enable it to access the Azure SQL Database service. Note down the Application ID and create a client secret for the application.<br />
- Grant the application permissions to access the Key Vault by running the provided PowerShell command.<br />
- Retrieve the ADO.NET connection string for the Azure SQL Database.<br />
- Log on to the Azure VM that was deployed in Exercise 1, which has Visual Studio 2019 and SQL Server Management Studio 2018 installed.<br />
  
  </details>
  
  #
  
  <details> 
  
  <summary>   

### Exercise 4: Demonstrate the use of Azure Key Vault in encrypting the Azure SQL database

  </summary> 
    
In this exercise, we will demonstrate the use of Azure Key Vault in encrypting the Azure SQL database.

- Using Visual Studio 2019 on the Azure VM, open the provided program.cs file.<br />
- Replace the connection string with the ADO.NET connection string retrieved in Exercise 3.<br />
- Follow the code comments to implement the necessary code for encrypting and decrypting sensitive data using Always Encrypted.<br />
- Build and run the application to see how Azure Key Vault is used to encrypt and decrypt data in the Azure SQL database.<br />

## Reflection:
In this lesson, we have learned how to create a proof of concept application that utilizes Azure SQL Database's Always Encrypted functionality with the support.




