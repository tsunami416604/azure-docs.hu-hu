---
title: Az Azure Event Hubs felügyeleti könyvtárak |} A Microsoft Docs
description: Az Event Hubs-névterek és az entitások kezelése a .NET használatával
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 06/13/2018
ms.author: shvija
ms.openlocfilehash: 1b158fd3ac3f36b71a76c149e8b6815f576be1c5
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006451"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs felügyeleti könyvtárak

Az Azure Event Hubs felügyeleti könyvtárak használatával dinamikusan telepíthet az Event Hubs-névterek és entitásokat. A dinamikus jellegű összetett és üzenetküldési forgatókönyvre, lehetővé teszi, hogy programozott módon meghatározhatja az üzembe helyezni entitásokat. Ezek a kódtárak érhetők el jelenleg a .NET-hez.

## <a name="supported-functionality"></a>Támogatott funkciók

* Namespace létrehozás, frissítés, törlés
* Event Hubs létrehozási, frissítési, törlési
* Fogyasztói csoport létrehozása, frissítése, törlése

## <a name="prerequisites"></a>Előfeltételek

Ismerkedés az Event Hubs felügyeleti könyvtárak használatával, hitelesítenie kell magát az Azure Active Directory (AAD). Aad-ben van szükség, hogy hitelesítse magát egy egyszerű szolgáltatást, amely az Azure-erőforrásokhoz való hozzáférést biztosít. Egyszerű szolgáltatás létrehozása kapcsolatos információkért tekintse meg az alábbi cikkek:  

* [Active Directory-alkalmazás és az erőforrások elérésére képes egyszerű szolgáltatás létrehozása az Azure portal használatával](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Ezek az oktatóanyagok, és adjon meg egy `AppId` (ügyfél-azonosító), `TenantId`, és `ClientSecret` (hitelesítési kulcs), mindegyike által a kezelési kódtárakat hitelesítéshez használhatók. Rendelkeznie kell **tulajdonosa** , amelyen futtatni szeretné az erőforráscsoporthoz tartozó engedélyeket.

## <a name="programming-pattern"></a>Programozási minta

A minta segítségével kezelheti az összes Event Hubs-erőforrást egy közös protokollt követi:

1. Szerezze be a jogkivonatot az AAD használatával a `Microsoft.IdentityModel.Clients.ActiveDirectory` könyvtár.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Hozzon létre a `EventHubManagementClient` objektum.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Állítsa be a `CreateOrUpdate` paraméterek a megadott értékekre.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. A hívás hajtható végre.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>További lépések
* [.NET – felügyelet minta](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub referencia](/dotnet/api/Microsoft.Azure.Management.EventHub) 
