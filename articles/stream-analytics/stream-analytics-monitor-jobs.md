---
title: Megfigyelés és kezelés programozott módon Azure Stream Analytics-feladatok
description: Ez a cikk ismerteti a programozott módon a REST API-k, az Azure SDK-t vagy a PowerShell segítségével létrehozott Stream Analytics-feladatok figyelése.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 2688f148185b1c1523178d190a7a2a76e6ceabef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30908785"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>A Stream Analytics-feladat figyelő létrehozása programmal.

Ez a cikk bemutatja, hogyan használatával engedélyezi a Stream Analytics-feladat. REST API-k, az Azure SDK-t vagy a PowerShell létrehozott Stream Analytics-feladatok nincs figyelés alapértelmezés szerint engedélyezett. Manuálisan engedélyezheti azt az Azure portálon Ugrás a feladat figyelő oldalra, majd kattintson az Engedélyezés gombra, vagy ez a cikk utasításait követve automatizálhatja is a folyamatot. A figyelési adatok az Azure-portál a Stream Analytics-feladat a metrikák területén fog megjelenni.

## <a name="prerequisites"></a>Előfeltételek

Ez a folyamat elkezdéséhez az alábbiakkal kell rendelkeznie:

* Visual Studio 2017 or 2015
* [Az Azure .NET SDK](https://azure.microsoft.com/downloads/) letöltése és telepítése
* Egy meglévő Stream Analytics-feladatban kell tartoznia a figyelő engedélyezve

## <a name="create-a-project"></a>Projekt létrehozása

1. Hozzon létre egy Visual Studio C# .NET konzolalkalmazást.
2. A Package Manager-konzolon, a következő parancsokat a NuGet-csomagok. Az első címtárra az Azure Stream Analytics felügyeleti .NET SDK. A második érték az Azure-figyelő SDK használatával engedélyezi a használandó. Legutóbb az Azure Active Directory-ügyfél-hitelesítéshez használandó.
   
   ```
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Adja hozzá a következő appSettings szakaszt az App.config fájlhoz.
   
   ```
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
   Cserélje le az értékeket *SubscriptionId* és *ActiveDirectoryTenantId* a Azure-előfizetés és a bérlői azonosítók. A következő PowerShell-parancsmag futtatásával kaphatunk ezekkel az értékekkel:
   
   ```
   Get-AzureAccount
   ```
4. Adja hozzá a következő using utasításokat a forrásfájl (Program.cs) a projektben.
   
   ```
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
5. Adja hozzá a segítő hitelesítési módszert.
   
     nyilvános, statikus karakterlánc GetAuthorizationHeader()
   
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

## <a name="create-management-clients"></a>Hozzon létre felügyeleti ügyfelek

A következő kódot a szükséges változók és a felügyeleti ügyfeleket beállításához.

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Egy meglévő Stream Analytics-feladat figyelésének engedélyezése

Az alábbi kód lehetővé teszi, hogy a figyelés egy **meglévő** Stream Analytics-feladat. A kód első része egy a Stream Analytics szolgáltatás GET kérelmet az adott Stream Analytics-feladat adatainak lekérésére hajt végre. Használja a *azonosító* tulajdonság (beolvasva a GET kérelmet a) a Put metódust a második fele a kódot, PUT, amely a Insights szolgáltatás kérése használatával engedélyezi a Stream Analytics-feladat paramétereként.

>[!WARNING]
>Ha már engedélyezte különböző Stream Analytics-feladat, az Azure-portálon vagy programozottan keresztül figyelés az alábbi kód, **azt javasoljuk, hogy megadja a ugyanazon tárfiók neve, amely akkor használhatók, ha korábban engedélyezve van a figyelést.**
> 
> A tárfiók kapcsolódik a régióban létrehozott a Stream Analytics-feladat, nem kifejezetten az maga a feladat.
> 
> Az összes Stream Analytics feladatok (és minden más Azure-erőforrások) ugyanabban a régióban megosztani ezt a tárfiókot, a figyelési adatok tárolására. Ha egy másik tárolási fiókot ad meg, nem kívánt hatásai megakadályozhatja a más Stream Analytics-feladatok vagy más Azure-erőforrások figyelése.
> 
> A tárfiók neve, amellyel cserélje le `<YOUR STORAGE ACCOUNT NAME>` a következő kódot kell egy tárfiókot, amely ugyanazt az előfizetést, a Stream Analytics-feladat, amely engedélyezi a figyelést.
> 
> 

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Támogatás kérése

Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

