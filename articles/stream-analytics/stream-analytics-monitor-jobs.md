---
title: Figyelése és felügyelete programozott módon az Azure Stream Analytics-feladatok
description: Ez a cikk ismerteti a REST API-k, az Azure SDK-t vagy a PowerShell segítségével létrehozott Stream Analytics-feladatok programozott figyelése.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: eaeb2b4decc7da4caa75cb2af68829b4bf7ce64d
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563846"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programozott módon létrehozhat egy Stream Analytics-feladat figyelése

Ez a cikk bemutatja, hogyan engedélyezze a monitorozást az egy Stream Analytics-feladatot. REST API-k, az Azure SDK-t vagy a PowerShell segítségével létrehozott Stream Analytics-feladatok nem rendelkezik a figyelés alapértelmezés szerint engedélyezve van. Manuálisan engedélyezheti azt az Azure Portalon történik a feladat figyelése oldalára, és az Engedélyezés gombra kattint, vagy ez a folyamat automatizálható ez a cikk lépéseit követve. A monitorozási adatok jelennek meg az Azure-portál a Stream Analytics-feladatot a metrikák területén.

## <a name="prerequisites"></a>Előfeltételek

Ez a folyamat megkezdése előtt kell rendelkeznie a következő előfeltételek vonatkoznak:

* A Visual Studio 2017 vagy 2015
* [Az Azure .NET SDK](https://azure.microsoft.com/downloads/) letöltése és telepítése
* Egy meglévő Stream Analytics-feladat engedélyezve van a figyelést igénylő

## <a name="create-a-project"></a>Projekt létrehozása

1. Hozzon létre egy Visual Studio C# nyelvet használó .NET-konzolalkalmazást.
2. A Package Manager konzol futtassa a következő parancsokat a NuGet-csomagok telepítéséhez. Az első rekordon az Azure Stream Analytics felügyeleti .NET SDK. A második érték az Azure Monitor SDK figyelés engedélyezéséhez használt. A rendszer az Azure Active Directory-ügyfél-hitelesítéshez használandó.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Adja hozzá a következő appSettings szakaszt az App.config fájlhoz.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Cserélje le az értékeket *SubscriptionId* és *ActiveDirectoryTenantId* az Azure-előfizetés és a bérlői azonosítókkal rendelkező. Ezeket az értékeket a következő PowerShell-parancsmag futtatásával kaphat:
   
   ```powershell
   Get-AzureAccount
   ```
4. Adja hozzá a következő using utasításokat a projekt forrásfájljához (Program.cs) való.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Adjon hozzá egy hitelesítési segédmetódus.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Hozzon létre a felügyeleti ügyfeleket

A következő kódot a szükséges változók és a felügyeleti ügyfeleket állítja be.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Engedélyezze a monitorozást az egy meglévő Stream Analytics-feladat

A következő kód lehetővé teszi a figyelést egy **meglévő** Stream Analytics-feladatot. A kód első része egy GET kérelmet a Stream Analytics szolgáltatás az adott Stream Analytics-feladat adatainak beolvasásához hajt végre. Használja a *azonosító* paraméterként a Put metódust a második fele a kódra, amely elküld egy PUT kérelmet a Insights szolgáltatásnak engedélyezze a monitorozást az a Stream Analytics-feladat számára (a GET-kérés lekért) tulajdonság.

> [!WARNING]
> Ha korábban engedélyezte a figyelést egy másik Stream Analytics-feladatot, vagy az Azure Portalon vagy programozott módon keresztül az alábbi kód, **javasoljuk, hogy adja meg az azonos tárfióknevet, amelyek akkor használhatók, ha előzőleg engedélyezni a figyelést.**
> 
> A storage-fiók van csatolva a régióban létrehozott a Stream Analytics-feladat, nem kifejezetten az magát a feladatot.
> 
> Minden Stream Analytics feladatok (és minden más Azure-erőforrások) ugyanabban a régióban ossza meg ezt a tárfiókot, a figyelési adatok tárolására. Ha megad egy másik tárfiókot, a többi Stream Analytics-feladatok vagy más Azure-erőforrások figyelési megakadályozhatja nem kívánt mellékhatásokkal.
> 
> A tárfiók nevét, amellyel cseréje `<YOUR STORAGE ACCOUNT NAME>` az alábbi kódot a storage-fiók, amely a Stream Analytics-feladat, amely engedélyezi a figyelést az azonos előfizetésben kell lennie.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Támogatás kérése

További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
