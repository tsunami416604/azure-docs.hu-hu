---
title: "Az Azure Service Bus-kezelési kódtárakat |} Microsoft Docs"
description: "A Service Bus-névterek és az üzenetküldési entitások, a .NET-kezelése."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: sethm
ms.openlocfilehash: 3b7096a073b509217a6ed29b53f88f912e6613f6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="service-bus-management-libraries"></a>A Service Bus kezelési kódtárakat

Az Azure Service Bus-kezelési kódtárakat dinamikusan építhető ki a Service Bus-névterek és az entitásokat. Lehetővé teszi összetett központi telepítések és üzenetkezelési forgatókönyveket, és milyen entitások kiépítését programozott módon meghatározásához lehetővé teszi. Ezek a kódtárak jelenleg érhetők el a .NET-hez.

## <a name="supported-functionality"></a>Támogatott funkciók

* Namespace létrehozási, frissítési, törlési
* Várólista létrehozása, frissítés, törlés
* A témakör létrehozását, frissítés, törlés
* Előfizetés létrehozása, frissítés, törlés

## <a name="prerequisites"></a>Előfeltételek

Első lépésként használatával a Service Bus-kezelési kódtárakat, hitelesítenie kell az Azure Active Directory (AAD) szolgáltatással. Az AAD megköveteli, hogy hitelesítse magát egy egyszerű szolgáltatást, amely az Azure-erőforrások hozzáférést tesz lehetővé. Egyszerű szolgáltatás létrehozása kapcsolatos információkért tekintse meg a következő cikkeket:  

* [Active Directory-alkalmazás és az erőforrások eléréséhez egyszerű szolgáltatás létrehozása az Azure-portál használatával](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Ezek az oktatóanyagok biztosítanak egy `AppId` (ügyfél-azonosító), `TenantId`, és `ClientSecret` (hitelesítési kulcs), amelyek használnak a hitelesítéshez a felügyeleti függvénytárai. Rendelkeznie kell **tulajdonos** engedéllyel ahhoz az erőforráscsoporthoz, amelyen futtatni szeretné.

## <a name="programming-pattern"></a>Programozási minta

A minta segítségével kezelheti a Service Bus-erőforrásoknál egy közös protokollt követi:

1. Az Azure Active Directory használatával jogkivonat beszerzése az **Microsoft.IdentityModel.Clients.ActiveDirectory** könyvtárban.
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Hozzon létre a `ServiceBusManagementClient` objektum.

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Állítsa be a `CreateOrUpdate` paramétereit, és a megadott értékeket.

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. A hívás végrehajtása.

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>Következő lépések
* [.NET felügyeleti minta](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.Management.ServiceBus API-referencia](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
