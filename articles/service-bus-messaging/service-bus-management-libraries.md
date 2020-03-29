---
title: Azure Service Bus felügyeleti kódtárak| Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan használhatja az Azure Service Bus felügyeleti kódtárak dinamikus kiépítéséhez Service Bus névterek és entitások dinamikusan kiépítése.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: d0e90d9278ede97de04ad8efeaa59d94a4567f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756266"
---
# <a name="service-bus-management-libraries"></a>A Service Bus felügyeleti könyvtárai

Az Azure Service Bus felügyeleti kódtárak dinamikusan kiépítheti a Service Bus névtereket és entitásokat. Ez lehetővé teszi az összetett központi telepítések és üzenetküldési forgatókönyvek, és lehetővé teszi, hogy programozott módon határozza meg, milyen entitásokat kell kiépíteni. Ezek a tárak jelenleg a .NET-hez érhetők el.

## <a name="supported-functionality"></a>Támogatott funkciók

* Névtér létrehozása, frissítése, törlése
* Várólista létrehozása, frissítése, törlése
* Téma létrehozása, frissítése, törlése
* Előfizetés létrehozása, frissítése, törlése

## <a name="prerequisites"></a>Előfeltételek

A Service Bus felügyeleti kódtárak használatának megkezdéséhez hitelesítenie kell magát az Azure Active Directory (Azure AD) szolgáltatással. Az Azure AD megköveteli, hogy egyszerű szolgáltatásként hitelesítse magát, amely hozzáférést biztosít az Azure-erőforrásokhoz. Az egyszerű szolgáltatás létrehozásáról az alábbi cikkek egyikében talál tájékoztatást:  

* [Az Azure Portal használatával hozzon létre erőforrásokat elérő Active Directory-alkalmazást és egyszerű szolgáltatást](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Ezek az oktatóanyagok `AppId` egy (ügyfélazonosítót), `ClientSecret` és (hitelesítési kulcsot) `TenantId`biztosítanak, amelyek mindegyike a felügyeleti kódtárak hitelesítéséhez használatos. Tulajdonosi **Owner** engedélyekkel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyen futni kíván.

## <a name="programming-pattern"></a>Programozási minta

A Service Bus-erőforrások kezelésére szolgáló minta egy közös protokollt követ:

1. Token beszerzése az Azure AD-től a **Microsoft.IdentityModel.Clients.ActiveDirectory** könyvtár használatával:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Hozza `ServiceBusManagementClient` létre az objektumot:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Állítsa `CreateOrUpdate` be a paramétereket a megadott értékekre:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. A hívás végrehajtása:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Teljes kód a várólista létrehozásához
Itt van a service bus-várólista létrehozásának teljes kódja: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> A teljes példát a [.NET felügyeleti minta a GitHubon című témakörben található.](https://github.com/Azure-Samples/service-bus-dotnet-management/) 

## <a name="next-steps"></a>További lépések
[Microsoft.Azure.Management.ServiceBus API-hivatkozás](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
