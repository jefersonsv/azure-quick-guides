# Audit Log to Azure Functions

## Goal

Stream _Azure Active Directory - Audit Logs_ to a Node.js _Azure Functions_

### Resume

1. Setup Event Hubs namespace
2. Setup Event Hubs
3. Setup Azure Active Directory
4. Setup Azure Function App
5. Create a Node.js function
6. Configure the trigger

#### Setup Event Hubs namespace

1. Access Home > Event Hubs
2. Click **Add**
3. Choose a unique event hub namespace
4. Select Basic princing tier
5. Select Subscription
6. Select or create a Resource group
7. Select a near Location
8. Click **Create**

### Setup Event Hub

1. Access Home > Event Hubs
2. Click the Event Hub namespace created before
3. Inside Overview item click **+ Event Hub**
4. Choose a unique event hub name
5. Click **Create**

### Setup Azure Active Directory

1. Access Home > <your-directory> - Overview Azure Active Directory
2. Open **Audit logs** inside of Monitoring section
3. Click **Export Data Settings**
4. Click **+ Add diagnostic setting**
5. Choose unique diagnistic settings name 
6. Check the option Stream to an event hub
7. Check the option AuditLogs under LOG section
8. Click **Configure** under Stream to an event hub
9. Select a Subscription
10. Select the event hub namespace created previously
11. Select the event hub name created previously
12. Select the event hub policy name
13. Click **Save**

### Setup Azure Functions

1. Access Home > Function App
2. Click **Add**
3. Choose a unique App name
4. Select a subscription
5. Create or select a Resource Group
6. Select the **Windows** OS 
   1. It is a important step to run the stable version of Azure Functions
7. Select the **Comsumption plan** of Hosting Plan
8. Select a near Location
9. Select **Node.js** in Runtime Stack
10. Create or select a Storage
11. Keep the **Application Insights** enabled
12. Click **Create**

### Create Function

1. Access Home > Function App
2. Click in Function App created previously
3. Click **Functions** inside Function Apps section
4. Click **New function***
5. Choose the template **Azure Event Hub trigger**
    1. The first time that you to use this feature you must to install the extension **Microsoft.Azure.WebJobs.Extensions.EventHubs** or see section __EventHubs extension manual installation__
    2. Then click **Install** to proceed with the instalation 
    3. Click **Continue** to finish these step
6. Choose a unique function name
7. Create or select a new Event Hub connection
   1. Under **Event Hub** tab
   2. Select the Event Hub Namespace created previously
   3. Select the Event Hub Name created previosly
   4. Select the default **RootManageSharedAccessKey** under Policy
   5. Click **Select**
8. Choose a **$Default** in Event Hub consumer group
9. Type the name of Event Hub created previously in section **Setup Event Hub**
10. Click **Create**

### Configure the trigger

1. Access Home > Function App
2. Click in Function App created previously
3. Click in the function created previously under the Functions section
4. Click in **View files** to expand right panel
5. Click in **function.json** file
6. Change the json content file to configure the trigger name
   1. Append the Event Hub Namespace inside value of key **eventHubName** and slash before the Event Hub Name
   2. The value of this property must be {Event Hub Namespace}/{Event Hub Name}
   3. Click **Save**

## Test these steps

1. Access Home > <your-directory> - Overview Azure Active Directory
2. Do any operation that write in Audit Log like: create a new group or invite a new guest user
3. Wait the latency of write operations in Audit logs
   1. Click in **Audit logs** in Activity section
   2. Click **Refresh** to update the last entries of log
4. Access Home > Event Hubs
5. Click the Event Hub namespace created before
6. Inside **Overview** scrool down the content and item click in Event Hub created previously
7. Inside **Overview** find for the Messages graph and see the Incoming Messages (sum) is a number greater then zero
8.  Access Home > Function App
9. Click in Function App created previously
10. Click **Functions** inside Function Apps section
11. Click in function name created previously
12. Click **Monitor** item and you will see all events triggered by Azure Active Directory - Audit Log

## EventHubs extension manual installation

1. Access Home > Function App
2. Click in Function App created previously
3. Click **Platform features** tab item at the right panel
4. Click **Advanced tools (Kudu)**
5. Click **site** folder name
6. Click **wwwroot** folder name
7. Create (clicking +) or edit (clicking pencil) on site **extensions.csproj""
8. Type the below content and click **Save** 
```Xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.EventHubs" Version="3.0.3" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```
9.  At the black terminal __Kudu Remote Execution Console__ type **dotnet build extensions.csproj -o bin --no-incremental** to build bin folder

# Notes

* Sometimes the azure will delay around 2 minutes to write a new line into Audit Log
* The Event Hub will start only after the line be write into Audit Log
* The EventHubs extension template or manual installation must be done only once