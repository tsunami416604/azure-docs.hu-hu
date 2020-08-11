---
title: Azure Service Bus felügyeleti kódtárak | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure Service Bus felügyeleti kódtárak Service Bus névterek és entitások dinamikus kiépítéséhez.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 63a8f0f8ef299f9d27d3c1be2746052536c4e5e8
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066168"
---
# <a name="service-bus-management-libraries"></a>A Service Bus felügyeleti könyvtárai

A Azure Service Bus felügyeleti kódtárak dinamikusan tudnak kiépíteni Service Bus névtereket és entitásokat. Ez összetett központi telepítéseket és üzenetkezelési forgatókönyveket tesz lehetővé, és lehetővé teszi, hogy programozott módon határozza meg, hogy milyen entitásokat kell kiépíteni. Ezek a kódtárak jelenleg a .NET-hez érhetők el.

## <a name="supported-functionality"></a>Támogatott funkciók

* Névtér létrehozása, frissítése, törlése
* Üzenetsor létrehozása, frissítése, törlése
* Témakör létrehozása, frissítése, törlése
* Előfizetés létrehozása, frissítése, törlése

## <a name="prerequisites"></a>Előfeltételek

A Service Bus felügyeleti kódtárak használatának megkezdéséhez hitelesítenie kell magát a Azure Active Directory (Azure AD) szolgáltatással. Az Azure AD-ben az Azure-erőforrásokhoz való hozzáférést biztosító egyszerű szolgáltatásként kell hitelesítenie magát. Az egyszerű szolgáltatásnév létrehozásával kapcsolatos információkért tekintse meg a következő cikkek egyikét:  

* [A Azure Portal használatával hozzon létre Active Directory alkalmazást és egyszerű szolgáltatást, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

Ezek az oktatóanyagok egy `AppId` (ügyfél-azonosító), `TenantId` és `ClientSecret` (hitelesítési kulcs) használatát teszik lehetővé, amelyek mindegyike a felügyeleti kódtárak általi hitelesítéshez használatos. Legalább [**Azure Service Bus adattulajdonosi**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) vagy [**közreműködői**](../role-based-access-control/built-in-roles.md#contributor) engedélyekkel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyen futtatni szeretné a szolgáltatást.

## <a name="programming-pattern"></a>Programozási minta

A Service Bus-erőforrások kezelésére szolgáló minta egy közös protokollt követ:

1. Szerezze be a tokent az Azure AD-ből a **Microsoft. IdentityModel. clients. ActiveDirectory** könyvtár használatával:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Hozza létre az `ServiceBusManagementClient` objektumot:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Állítsa be a `CreateOrUpdate` paramétereket a megadott értékekre:

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
> Teljes példaként tekintse meg a [.net-felügyeleti mintát a githubon](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>További lépések
[Microsoft. Azure. Management. ServiceBus API-dokumentáció](/dotnet/api/Microsoft.Azure.Management.ServiceBus)