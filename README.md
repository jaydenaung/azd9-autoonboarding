What does this script do?
-------------------------

It logs into an Azure AD tenant, obtains a list of all subscriptions attached to it and then adds them one by one to CloudGuard in read only mode. It can be used as an Azure Function to run daily to sweep up any new subscriptions not onboarded.

![alt text](https://github.com/chrisbeckett/azd9-autoonboarding/blob/master/onboard.png "Onboarder screenshot")

What's New? (6th January 2021)
------------------------------

   - Updated core libraries to latest version, resolving security issue in cryptography library
   - Changed references from Dome9 to CloudGuard 
   - Changed environment variables from **D9_API_KEY** and **D9_API_SECRET** to **CG_API_KEY** and **CG_API_SECRET** respectively
   - Updated code to leverage current authentication library from Azure, removing deprecated method
   - Improved logging in Azure Function code
   - Updated standalone Windows executable
   - New MacOS standalone executable


Standalone executable
-----------------------------

If you need to run the tool as a one off and don't want the faff of all the steps below, you can download the ZIP archive. This is a portable version of the tool for Windows or Mac, all batteries included. Download and extract the ZIP and run the executable.

Remember to set environment variables prior to running the tool (see below).

Pre-requisites
--------------
To run this script, you will need the following:-

1) **Python 3.6** (or newer)

2) An Azure AD **Application Registration** (see *https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal*)

3) Azure environment information
    - Azure AD **tenant ID**
    - Azure **Application (Client) ID**
    - Azure **Application (Client) Secret Key**
    
4) CloudGuard API key with admin permissions to add subscriptions (*https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk144514&partition=General&product=CloudGuard*)
    - CloudGuard **API key**
    - CloudGuard **API secret**
    
5) You will need the **Azure Management Group** construct configured against your Azure AD (see *https://docs.microsoft.com/en-us/azure/governance/management-groups/overview* for further information). This is so that any new subscriptions added against the Azure AD tenant are visible to the onboarding tool. **Adding the Application ID as a Contributor in the IAM Access Control blade in the Tenant Root Group will provide this capability.**

6) Run **git clone https://github.com/chrisbeckett/azd9-autoonboarding.git**

7) Run **python -m venv azd9-autoonboarding**

8) Run **scripts\activate.bat** to enable the Python virtual environment (Windows) or **source bin/activate** on Mac/Linux

9) Run **pip install -r requirements.txt** to install required Python modules
    
Setup
-----
To run the script locally, you need to set several environment variables which are then read in by the script. This prevents any secret keys being hard coded into the script. Set the following:-

- SET CG_API_KEY=xxxxxxxxxxx
- SET CG_API_SECRET=xxxxxxxxxxxx
- SET AZURE_TENANT_ID=xxxxxxxxxx
- SET AZURE_CLIENT_ID=xxxxxxxxxxx
- SET AZURE_CLIENT_SECRET=xxxxxxxxxxxxxxx

Running the script
------------------
Simply run the script **onboard.py** from the command line 

Deploying as an Azure Function 
------------------------------

- Click the "Deploy to Azure" button and fill out the deployment form
- Both the **Azure Function** name and the **Storage Account** name **must be globally unique or deployment will fail (if a new storage account is created)**
- Once the ARM template deployment is complete, open a command prompt and navigate to the **azd9-autoonboarding** folder
- Install the Azure Functions command line tools (*https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cbash*)
- Run **func init**
- Run **func azure functionapp publish *functname*** where the functname is your function name from the "**Deploy to Azure**" workflow
- By default, the function uses a timer trigger set to run at 0600 daily, you may alter this to suit your needs
    
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fchrisbeckett%2Fazd9-autoonboarding%2Fmaster%2Ftemplate.json)


---
Note: if you have any issues with python version older than 3.6, let us know.

