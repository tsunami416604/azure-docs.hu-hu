---
title: Felügyeleti könyvtárak – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure Event Hubs névterek és entitások .NET-beli kezeléséhez használható könyvtárról nyújt információkat.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f3129ae5586a3096dda89eea3af21eefd1606f30
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85312978"
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

Ezek az oktatóanyagok egy `AppId` (ügyfél-azonosító), `TenantId` és `ClientSecret` (hitelesítési kulcs) használatát teszik lehetővé, amelyek mindegyike a felügyeleti kódtárak általi hitelesítéshez használatos. A futtatni kívánt erőforráscsoport **tulajdonosi** engedélyekkel kell rendelkeznie.

## <a name="programming-pattern"></a>Programozási minta

A Event Hubs-erőforrások kezelésére szolgáló minta egy közös protokollt követ:

1. Szerezze be a tokent a HRE a `Microsoft.IdentityModel.Clients.ActiveDirectory` könyvtár használatával.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Hozza létre az `EventHubManagementClient` objektumot.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Állítsa be a `CreateOrUpdate` paramétereket a megadott értékekre.
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
