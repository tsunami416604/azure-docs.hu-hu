---
title: Felügyeleti könyvtárak – Azure Event Hubs| Microsoft dokumentumok
description: Ez a cikk az Azure Event Hubs névterek és entitások .NET-ből történő kezeléséhez használható tárral kapcsolatos információkat tartalmaz.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60746658"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs felügyeleti könyvtárak

Az Azure Event Hubs felügyeleti kódtárak segítségével dinamikusan kiépítheti az Event Hubs névtereket és entitásokat. Ez a dinamikus jellegű összetett központi telepítések és üzenetküldési forgatókönyvek, így programozott módon meghatározhatja, hogy mely entitások kiépítése. Ezek a tárak jelenleg a .NET-hez érhetők el.

## <a name="supported-functionality"></a>Támogatott funkciók

* Névtér létrehozása, frissítése, törlése
* Az Eseményközpontok létrehozása, frissítése, törlése
* Fogyasztói csoport létrehozása, frissítése, törlése

## <a name="prerequisites"></a>Előfeltételek

Az Event Hubs felügyeleti kódtárak használatának megkezdéséhez hitelesítenie kell magát az Azure Active Directoryval (AAD). Az AAD megköveteli, hogy egyszerű szolgáltatásként hitelesítse magát, amely hozzáférést biztosít az Azure-erőforrásokhoz. Az egyszerű szolgáltatás létrehozásáról az alábbi cikkek egyikében talál tájékoztatást:  

* [Az Azure Portal használatával hozzon létre erőforrásokat elérő Active Directory-alkalmazást és egyszerű szolgáltatást](../active-directory/develop/howto-create-service-principal-portal.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure CLI használatával](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Ezek az oktatóanyagok `AppId` egy (ügyfélazonosítót), `ClientSecret` és (hitelesítési kulcsot) `TenantId`biztosítanak, amelyek mindegyike a felügyeleti kódtárak hitelesítéséhez használatos. Tulajdonosi **Owner** engedélyekkel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyen futni szeretne.

## <a name="programming-pattern"></a>Programozási minta

Az Event Hubs-erőforrások kezelésére szolgáló minta egy közös protokollt követ:

1. Szerezzen be egy jogkivonatot `Microsoft.IdentityModel.Clients.ActiveDirectory` az AAD-től a tár használatával.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Hozza `EventHubManagementClient` létre az objektumot.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Állítsa `CreateOrUpdate` be a paramétereket a megadott értékekre.
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
* [.NET Felügyeleti minta](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub – referencia](/dotnet/api/Microsoft.Azure.Management.EventHub) 
