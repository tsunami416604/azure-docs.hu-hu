---
title: Azure Service Bus felügyeleti kódtárak | Microsoft Docs
description: Service Bus névterek és üzenetküldési entitások kezelése a .NET-ből.
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
ms.date: 06/05/2019
ms.author: aschhab
ms.openlocfilehash: faf0a5893b7de276b9a411745500daef4d39da6b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816075"
---
# <a name="service-bus-management-libraries"></a>A Service Bus felügyeleti könyvtárai

A Azure Service Bus felügyeleti kódtárak dinamikusan tudnak kiépíteni Service Bus névtereket és entitásokat. Ez összetett központi telepítéseket és üzenetkezelési forgatókönyveket tesz lehetővé, és lehetővé teszi, hogy programozott módon határozza meg, hogy milyen entitásokat kell kiépíteni. Ezek a kódtárak érhetők el jelenleg a .NET-hez.

## <a name="supported-functionality"></a>Támogatott funkciók

* Namespace létrehozás, frissítés, törlés
* Üzenetsor létrehozása, frissítése, törlése
* Témakör létrehozása, frissítése, törlése
* Előfizetés létrehozása, frissítése, törlése

## <a name="prerequisites"></a>Előfeltételek

A Service Bus felügyeleti kódtárak használatának megkezdéséhez hitelesítenie kell magát a Azure Active Directory (Azure AD) szolgáltatással. Az Azure AD-ben az Azure-erőforrásokhoz való hozzáférést biztosító egyszerű szolgáltatásként kell hitelesítenie magát. Egyszerű szolgáltatás létrehozása kapcsolatos információkért tekintse meg az alábbi cikkek:  

* [Active Directory-alkalmazás és az erőforrások elérésére képes egyszerű szolgáltatás létrehozása az Azure portal használatával](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Ezek az oktatóanyagok, és adjon meg egy `AppId` (ügyfél-azonosító), `TenantId`, és `ClientSecret` (hitelesítési kulcs), mindegyike által a kezelési kódtárakat hitelesítéshez használhatók. A futtatni kívánt erőforráscsoport tulajdonosi engedélyekkel kell rendelkeznie.

## <a name="programming-pattern"></a>Programozási minta

A Service Bus-erőforrások kezelésére szolgáló minta egy közös protokollt követ:

1. Szerezze be a tokent az Azure AD-ből a **Microsoft. IdentityModel. clients. ActiveDirectory** könyvtár használatával:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Hozza létre `ServiceBusManagementClient` az objektumot:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Állítsa be `CreateOrUpdate` a paramétereket a megadott értékekre:

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

## <a name="complete-code-to-create-a-queue"></a>A várólista létrehozásához szükséges kód végrehajtása
A Service Bus üzenetsor létrehozásához a teljes kód a következő: 

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
> Teljes példaként tekintse meg a [.net-felügyeleti mintát](https://github.com/Azure-Samples/service-bus-dotnet-management/)a githubon. 

## <a name="next-steps"></a>További lépések
[Microsoft. Azure. Management. ServiceBus API-dokumentáció](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
