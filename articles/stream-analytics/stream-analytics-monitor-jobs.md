---
title: Az Azure Stream Analytics-feladatok programozási programozási figyelése és kezelése
description: Ez a cikk ismerteti, hogyan programozott módon figyelheti a REST API-kon, az Azure SDK-n vagy a PowerShellen keresztül létrehozott Stream Analytics-feladatokat.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 23c0cc0d0e4a007fdf46021f857b559266f6a193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431669"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programozott módon létrehoz egy Stream Analytics-feladatfigyelőt

Ez a cikk bemutatja, hogyan engedélyezheti a figyelést egy Stream Analytics-feladathoz. A REST API-kon, az Azure SDK-n vagy a PowerShellen keresztül létrehozott Stream Analytics-feladatok alapértelmezés szerint nincs engedélyezve a figyelés. Manuálisan engedélyezheti az Azure Portalon a feladat figyelője lapon, és kattintson az Engedélyezés gombra, vagy automatizálhatja ezt a folyamatot a jelen cikkben leírt lépések végrehajtásával. A figyelési adatok megjelennek az Azure Portal Metrika területén a Stream Analytics-feladathoz.

## <a name="prerequisites"></a>Előfeltételek

A folyamat megkezdése előtt a következő előfeltételekkel kell rendelkeznie:

* Visual Studio 2019 vagy 2015
* [Az Azure .NET SDK](https://azure.microsoft.com/downloads/) letöltve és telepítve
* Meglévő Stream Analytics-feladat, amelynek engedélyeznie kell a figyelést

## <a name="create-a-project"></a>Projekt létrehozása

1. Hozzon létre egy Visual Studio C# .NET konzolalkalmazást.
2. A Package Manager konzolon futtassa a következő parancsokat a NuGet csomagok telepítéséhez. Az első az Azure Stream Analytics Management .NET SDK. A második az Azure Monitor SDK, amely a figyelés engedélyezéséhez lesz használva. Az utolsó az Azure Active Directory-ügyfél, amely hitelesítéshez lesz használva.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Adja hozzá a következő appBeállítások szakaszt az App.config fájlhoz.
   
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
   A *SubscriptionId* és az *ActiveDirectoryTenantId* értékeit az Azure-előfizetésés a bérlői azonosítók helyére cserélheti. Ezeket az értékeket a következő PowerShell-parancsmag futtatásával szerezheti be:
   
   ```powershell
   Get-AzureAccount
   ```
4. Adja hozzá az alábbi using utasításokat a projekt forrásfájljához (Program.cs).
   
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
5. Adjon hozzá egy hitelesítési segítő módszert.

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

## <a name="create-management-clients"></a>Felügyeleti ügyfelek létrehozása

A következő kód beállítja a szükséges változókat és felügyeleti ügyfeleket.

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Figyelés engedélyezése meglévő Stream Analytics-feladathoz

A következő kód lehetővé teszi egy **meglévő** Stream Analytics-feladat figyelését. A kód első része GET-kérést hajt végre a Stream Analytics szolgáltatással szemben az adott Stream Analytics-feladattal kapcsolatos információk lekéréséhez. Az *azonosító* tulajdonságot (a GET-kérelemből lekért) használja a kód második felében a Put metódus paramétereként, amely put-kérést küld az Insights szolgáltatásnak a Stream Analytics-feladat figyelésének engedélyezéséhez.

> [!WARNING]
> Ha korábban engedélyezte a figyelést egy másik Stream Analytics-feladathoz, akár az Azure Portalon keresztül, akár programozott módon az alábbi kódon keresztül, **azt javasoljuk, hogy adja meg ugyanazt a tárfiók nevet, amelyet korábban a figyelés engedélyezésekor használt.**
> 
> A tárfiók ahhoz a régióhoz kapcsolódik, amelyben létrehozta a Stream Analytics-feladatot, nem pedig magához a feladathoz.
> 
> Az ugyanabban a régióban található összes Stream Analytics-feladat (és minden más Azure-erőforrás) megosztja ezt a tárfiókot a figyelési adatok tárolásához. Ha egy másik tárfiókot ad meg, előfordulhat, hogy nem kívánt mellékhatásokat okozhat a többi Stream Analytics-feladat vagy más Azure-erőforrások figyelése során.
> 
> A tárfiók nevét, amelyet a következő kódban cserélni `<YOUR STORAGE ACCOUNT NAME>` kell egy tárfiók, amely ugyanabban az előfizetésben van, mint a Stream Analytics-feladat, amely lefigyelje a figyelést.
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

További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
