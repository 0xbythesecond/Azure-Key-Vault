# Azure-Key-Vault

<img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/Key%20Vault%20Diagram.png?raw=true" height="100%" width="100%" alt="Key Vault Diagram"/>

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

- Sign in to the Azure portal ```https://portal.azure.com``` using an account with Owner or Contributor role in the target subscription. 
- Search for "Deploy a custom template" in the Azure portal and select the option to build your own template in the editor. <br />
  
  <img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/Build%20your%20own%20template%20in%20the%20editor.png?raw=true" height="60%" width=60% alt="build a template"/>
  
- Load the ARM template file provided in the lab resources. <br />
  
  <img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/load%20json%20template.png?raw=true" height="60%" width="60%" alt="load json file"/>
  
- Save the template and configure the deployment settings, such as the subscription, resource group, location, and admin credentials.<br />
  
  <img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/Create%20Resrouce%20Group%20for%20the%20Custom%20Template.png?raw=true" height="60%" width="60%" alt="create resource group for custom template"/>
  
- Review the settings and click Create to start the deployment.<br />

  >**Note**: The deployment may take around 20-25 minutes to complete. 
  
  </details>
  
  #
  
  <details>
  
  <summary>

### Exercise 2: Configure the Key Vault resource with a key and a secret
    
  </summary> 

In this exercise, we will create and configure an Azure Key Vault resource. We will also add a key and a secret to the Key Vault.

- Open the Azure Cloud Shell and select PowerShell.<br />
- Run the provided PowerShell commands to create an Azure Key Vault in the specified resource group.<br />
  
 ```powershell
 $kvName = 'az500kv' + $(Get-Random)

 $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

 New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location
 ```
  
- Take note of the Key Vault name and URI.<br />
  
  >**Note**: The output of the last command will display the vault name and the vault URI. The vault URI is in the format `https://<vault_name>.vault.azure.net/`
  
- In the Azure portal, navigate to the Key Vault resource and configure access policies to allow key, secret, and certificate management.<br />
  
|Setting |	Value|
|------- |--------|  
|Configure from template (optional) |	Key, Secret, & Certificate Management|
|Key permissions |	click Select all resulting in total of 9 selected permissions|
|Key permissions/Cryptographic Operations	| click Sign resulting in total of 1 selected permissions|
|Secret permissions |	click Select all resulting in total of 7 selected permissions|
|Certification permissions |	click Select all resulting in total of 15 selected permissions|
|Select principal |	click None selected, on the Principal blade, select your user account, and click Next|
|Application (optional) |	click Next|
|Review + create |	click Create|
  
- Add a key to the Key Vault using the PowerShell command provided and verify its creation.<br />
  
```powershell
 $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

 $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
```
  
- Add a secret to the Key Vault using the PowerShell command provided and verify its creation.<br />
  
``` powershell
Get-AZKeyVaultKey -VaultName $kv.VaultName
```
  
 ```ex
 $key.key.kid  
 ``` 
  
 <img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/Vault%20Name%20from%20PowerShell%20Command.png?raw=true" height="80%" width="80%" alt="vault name from powershell"/>
  
  >**Note**: You can reference any key by using the key identifier. To get the most current version, reference `https://<key_vault_name>.vault.azure.net/keys/MyLabKey` or get the specific version with: `https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`
  
  - Adding a Secret to the Key
  
```powershell
  $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
```  

```powershell
  $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
```
  
  >**Note**: The name of the secret is SQLPassword.

  - Verify Secret was Created
  
```powershell
  Get-AZKeyVaultSecret -VaultName $kv.VaultName
```
  
<img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/Secret%20Name%20SQLPassword.png?raw=true" height="80%" width="80%" alt="Secret SQL Password Name"/>
  
  </details> 
  
  #
  
  <details>
  
  <summary>

### Exercise 3: Configure an Azure SQL database and a data-driven application
    
  </summary>

In this exercise, we will enable a client application to access the Azure SQL Database service and configure the necessary permissions.

- Register an application in Azure Active Directory (AD) to enable it to access the Azure SQL Database service. Note down the Application ID and create a client secret for the application.<br />
    
 >**Note**: Once the registration is completed, the browser will automatically redirect you to sqlApp blade.
    
Create client Secret  
    
<img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/New%20Client%20Secret%20(duration).png?raw=true" height="70%" width="70%" alt="create client secret"/>  
    
 Client Secret Value
    
 <img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/sql%20app%20client%20secret%20value.png?raw=true" height="70%" width="70%" alt="secret value for sql app registration"/>
    
  >**Note**: Record this value. You will need it in the next task.
    
  >**Note**: Make sure to copy the value before you navigate away from the blade. Once you do, it is no longer possible to retrieve its clear text value.  


    
- Grant the application permissions to access the Key Vault by running the provided PowerShell command.<br />
  Replace the <Azure_AD_Application_ID> placeholder with the value of the Application (client) ID):  
    
```powershell
   $applicationId = '<Azure_AD_Application_ID>'
```
    
   - To create a variable storing the Key Vault name.
    
```powershell
$kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName

$kvName
```    
    
   - Grant permissions on the Key Vault to the application you registered in the previous task:

```powershell
Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```    
 
    
- Retrieve the ADO.NET connection string for the Azure SQL Database.<br />
    
<img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/sql%20database%20connection%20string.png?raw=true" height="80%" width="80%" alt="ado.net connection string"/>    
    
  >**Note**: When you use the connection string, make sure to replace the {your_password} placeholder with the password that you configured with the deployment in Exercise 1.
    
- Log on to the Azure VM that was deployed in Exercise 1, which has Visual Studio 2019 and SQL Server Management Studio 2018 installed.<br />
    
In the Object Explorer pane, right-click the medical database and click New Query.

Paste the following code into the query window and click Execute. This will create a Patients table.    
   
```sql    
   CREATE TABLE [dbo].[Patients](
    [PatientId] [int] IDENTITY(1,1),
    [SSN] [char](11) NOT NULL,
    [FirstName] [nvarchar](50) NULL,
    [LastName] [nvarchar](50) NULL,
    [MiddleName] [nvarchar](50) NULL,
    [StreetAddress] [nvarchar](50) NULL,
    [City] [nvarchar](50) NULL,
    [ZipCode] [char](5) NULL,
    [State] [char](2) NULL,
    [BirthDate] [date] NOT NULL 
 PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );  
``` 
    
<img src="https://github.com/0xbythesecond/Azure-Key-Vault/blob/main/Execute%20SQL%20Query.png?raw=true" height="60%" width="60%" alt="execute sql query"/>
    
  After the table is created successfully, in the Object Explorer pane, expand the medical database node, the tables node, right-click the dbo.Patients node, and click Encrypt Columns.

  >**Note**: This will initiate the Always Encrypted wizard.

On the Introduction page, click Next.

On the Column Selection page, select the SSN and Birthdate columns, set the Encryption Type of the SSN column to Deterministic and of the Birthdate column to Randomized, and click Next.

  >**Note**: While performing the encryption if any error thrown like Exception has been thrown by the target of an innvocation related to Rotary(Microsoft.SQLServer.Management.ServiceManagement) then make sure the Key Permission’s values of Rotation Policy Operations are unchecked, if not in the Azure portal navigate to the Key Vault » Access Policies » Key Permissions » Uncheck all the values under the Rotation Policy Operations » Under Privileged Key Operations » Uncheck Release.

On the Master Key Configuration page, select Azure Key Vault, click Sign in, when prompted, authenticate by using the same user account you used to provision the Azure Key Vault instance earlier in this lab, ensure that that Key Vault appears in the Select an Azure Key Vault drop down list, and click Next.

On the Run Settings page, click Next.

On the Summary page, click Finish to proceed with the encryption. When prompted, sign in again by using the same user account you used to provision the Azure Key Vault instance earlier in this lab.

Once the encryption process is complete, on the Results page, click Close.

In the SQL Server Management Studio console, in the Object Explorer pane, under the medical node, expand the Security and Always Encrypted Keys subnodes.

  >**Note**: The Always Encrypted Keys subnode contains the Column Master Keys and Column Encryption Keys subfolders.  
    
  </details>
  
  #
  
  <details> 
  
  <summary>   

### Exercise 4: Demonstrate the use of Azure Key Vault in encrypting the Azure SQL database

  </summary> 
    
In this exercise, we will demonstrate the use of Azure Key Vault in encrypting the Azure SQL database.

- Using Visual Studio 2019 on the Azure VM, open the provided program.cs file.<br />
    - From the RDP session to the az500-10-vm1, launch Visual Studio 2019 from the Start menu.

    - Switch to the window displaying Visual Studio 2019 welcome message, click the Sign in button and, when prompted, provide the credntials you used to authenticate to the Azure subscription you are using in this lab.

    - On the Get started page, click Create a new project.

    - In the list of project templates, search for Console App (.NET Framework), in the list of results, click Console App (.NET Framework) for C#, and click Next.

    - On the Configure your new project page, specify the following settings (leave other settings with their default values), then click Create:

| Setting	| Value |
|-------- | ------ |    
| Project name |	OpsEncrypt|
| Solution name |	OpsEncrypt|
|Framework |	.NET Framework 4.7.2|
    
   1. In the Visual Studio console, click the Tools menu, in the drop down menu, click NuGet Package Manager, and, in the cascading menu, click Package Manager Console.

   2. In the Package Manager Console pane, run the following to install the first required NuGet package:

```powershell
Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
```
    
   3. In the Package Manager Console pane, run the following to install the second required NuGet package:

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```
    
- Replace the connection string with the ADO.NET connection string retrieved in Exercise 3.<br />
- Follow the code comments to implement the necessary code for encrypting and decrypting sensitive data using Always Encrypted.<br />
    
```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
``` 
    
- Build and run the application to see how Azure Key Vault is used to encrypt and decrypt data in the Azure SQL database.<br />
    - Minimize the RDP session to your Azure virtual machine, then navigate to \Allfiles\Labs\10\program.cs, open it in Notepad, and copy its content into Clipboard.

    - Return to the RDP session, and in the Visual Studio console, in the Solution Explorer window, click Program.cs and replace its content with the code you copied into Clipboard.

    - In the Visual Studio window, in the Program.cs pane, in line 15, replace the <connection string noted earlier> placeholder with the Azure SQL database ADO.NET connection string you recorded earlier in the lab. In the connection string, replace the {your_password} placehodler, with the password that you specified in the deployment in Exercise 1. If you saved the string on the lab computer, you may need to leave the RDP session to copy the ADO string, then return to the Azure virtual machine to paste it in.
    
```
ADO.NET (SQL authentication)
Server=tcp:sqlserverm3ywpe4yaiydo.database.windows.net,1433;Initial Catalog=medical;Persist Security Info=False;User ID=Student;Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

   1. In the Visual Studio window, in the Program.cs pane, in line 16, replace the <client id noted earlier> placeholder with the value of Application (client) ID of the registered app you recorded earlier in the lab.

   2. In the Visual Studio window, in the Program.cs pane, in line 17, replace the <key value noted earlier> placeholder with the the value of Key1 of the registered app you recorded earlier in the lab.

   3. In the Visual Studio console, click the Start button to initiate the build of the console application and start it.

   4. The application will start a Command Prompt window. When prompted for password, type the password that you specified in the deployment in Exercise 1 to connect to Azure SQL Database.

   5. Leave the console app running and switch to the SQL Management Studio console.

   6. In the Object Explorer pane, right-click the medical database and, in the right-click menu, click New Query.

   7. From the query window, run the following query to verify that the data that loaded into the database from the console app is encrypted.


    
   

## Reflection:
In this lesson, we have learned how to create a proof of concept application that utilizes Azure SQL Database's Always Encrypted functionality with the support.




