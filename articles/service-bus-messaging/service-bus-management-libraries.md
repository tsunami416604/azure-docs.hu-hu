---
title: Az Azure Service Bus felügyeleti könyvtárak |} A Microsoft Docs
description: A Service Bus-névtér és üzenetküldési entitások, a .NET használatával kezelheti.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: spelluru
ms.openlocfilehash: a959687fbf6e296cab7e0d8ca49ae97a005622cf
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696575"
---
# <a name="service-bus-management-libraries"></a>A Service Bus felügyeleti könyvtárai

Az Azure Service Bus felügyeleti könyvtárak dinamikusan helyezhet üzembe Service Bus-névterek és entitásokat. Ez lehetővé teszi az összetett és üzenetküldési forgatókönyvre, és lehetővé teszi a programozott módon határozza meg az üzembe helyezni entitásokat. Ezek a kódtárak érhetők el jelenleg a .NET-hez.

## <a name="supported-functionality"></a>Támogatott funkciók

* Namespace létrehozás, frissítés, törlés
* Üzenetsor-létrehozás, frissítés, törlés
* Témakör-létrehozás, frissítés, törlés
* Előfizetés létrehozása, frissítése, törlése

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a Service Bus felügyeleti könyvtárak használatával az Azure Active Directory (Azure AD) szolgáltatásban kell hitelesítenie. Az Azure AD szükséges, hogy hitelesítse magát egy egyszerű szolgáltatást, amely az Azure-erőforrásokhoz való hozzáférést biztosít. Egyszerű szolgáltatás létrehozása kapcsolatos információkért tekintse meg az alábbi cikkek:  

* [Active Directory-alkalmazás és az erőforrások elérésére képes egyszerű szolgáltatás létrehozása az Azure portal használatával](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Ezek az oktatóanyagok, és adjon meg egy `AppId` (ügyfél-azonosító), `TenantId`, és `ClientSecret` (hitelesítési kulcs), mindegyike által a kezelési kódtárakat hitelesítéshez használhatók. Rendelkeznie kell **tulajdonosa** , amelyen futtatni szeretné az erőforráscsoporthoz tartozó engedélyeket.

## <a name="programming-pattern"></a>Programozási minta

A minta segítségével kezelheti a Service Bus-erőforrásoknál egy közös protokollt követi:

1. Jogkivonat beszerzése az Azure AD-t a **Microsoft.IdentityModel.Clients.ActiveDirectory** könyvtár:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Hozzon létre a `ServiceBusManagementClient` objektum:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Állítsa be a `CreateOrUpdate` a megadott értékek a paramétereket:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. A hívás hajtható végre:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>További lépések

* [A minta eszközfelügyeleti .NET](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.Management.ServiceBus API-referencia](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
