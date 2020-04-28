---
title: Felügyeleti könyvtárak – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure Event Hubs névterek és entitások .NET-beli kezeléséhez használható könyvtárról nyújt információkat.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "60746658"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs felügyeleti könyvtárak

Az Azure Event Hubs felügyeleti kódtárak használatával dinamikusan kiépítheti Event Hubs névtereket és entitásokat. Ez a dinamikus természet összetett központi telepítéseket és üzenetkezelési forgatókönyveket tesz lehetővé, így programozott módon meghatározhatja, hogy milyen entitásokat kell kiépíteni. Ezek a kódtárak jelenleg a .NET-hez érhetők el.

## <a name="supported-functionality"></a>Támogatott funkciók

* Névtér létrehozása, frissítése, törlése
* Event Hubs létrehozás, frissítés, törlés
* Fogyasztói csoport létrehozása, frissítése, törlése

## <a name="prerequisites"></a>Előfeltételek

A Event Hubs felügyeleti kódtárak használatának megkezdéséhez hitelesítenie kell magát a Azure Active Directoryval (HRE). A HRE megköveteli, hogy a hitelesítést egy egyszerű szolgáltatásként hitelesítse, amely hozzáférést biztosít az Azure-erőforrásokhoz. Az egyszerű szolgáltatásnév létrehozásával kapcsolatos információkért tekintse meg a következő cikkek egyikét:  

* [A Azure Portal használatával hozzon létre Active Directory alkalmazást és egyszerű szolgáltatást, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Ezek az oktatóanyagok egy `AppId` (ügyfél-azonosító), `TenantId`és `ClientSecret` (hitelesítési kulcs) használatát teszik lehetővé, amelyek mindegyike a felügyeleti kódtárak általi hitelesítéshez használatos. A futtatni kívánt erőforráscsoport **tulajdonosi** engedélyekkel kell rendelkeznie.

## <a name="programming-pattern"></a>Programozási minta

A Event Hubs-erőforrások kezelésére szolgáló minta egy közös protokollt követ:

1. Szerezze be a tokent a HRE `Microsoft.IdentityModel.Clients.ActiveDirectory` a könyvtár használatával.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Hozza létre `EventHubManagementClient` az objektumot.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Állítsa be `CreateOrUpdate` a paramétereket a megadott értékekre.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Hajtsa végre a hívást.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>További lépések
* [.NET-felügyeleti minta](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft. Azure. Management. EventHub – dokumentáció](/dotnet/api/Microsoft.Azure.Management.EventHub) 
