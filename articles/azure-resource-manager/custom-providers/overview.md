---
title: Egyéni szolgáltatók áttekintése
description: Ismerje meg az Azure egyéni erőforrás-szolgáltatók, és hogyan bővítheti az Azure API-sík, hogy illeszkedjen a munkafolyamatok.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: fd21117219ea3db6946e7a1b889d92702af65b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650486"
---
# <a name="azure-custom-resource-providers-overview"></a>Az Azure egyéni erőforrás-szolgáltatóinak áttekintése

Az Azure egyéni erőforrás-szolgáltatók az Azure bővíthetőségi platformja. Lehetővé teszi az alapértelmezett Azure-élmény gazdagítására használható egyéni API-k definiálását. Ez a dokumentáció a következőket írja le:

- Azure egyéni erőforrás-szolgáltató létrehozása és üzembe helyezése.
- Az Azure egyéni erőforrás-szolgáltatók használata a meglévő munkafolyamatok bővítéséhez.
- Hol találhat útmutatókat és kódmintákat a kezdéshez.

![Egyéni szolgáltató – áttekintés](./media/overview/overview.png)

> [!IMPORTANT]
> Az egyéni szolgáltatók jelenleg nyilvános előzetes verzióban vannak.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="what-can-custom-resource-providers-do"></a>Mire képesek az egyéni erőforrás-szolgáltatók?

Íme néhány példa arra, hogy mit érhet el az Azure egyéni erőforrás-szolgáltatókkal:

- Bővítse ki az Azure Resource Manager REST API-t a belső és külső szolgáltatásokra.
- Egyéni forgatókönyvek engedélyezése a meglévő Azure-munkafolyamatok on top.
- Az Azure Resource Manager-sablonok vezérlésének és hatásának testreszabása.

## <a name="what-is-a-custom-resource-provider"></a>Mi az egyéni erőforrás-szolgáltató?

Az Azure egyéni erőforrás-szolgáltatók az Azure és egy végpont közötti szerződés létrehozásával készülnek. Ez a szerződés egy új erőforrás és művelet listáját határozza meg egy új erőforráson, a **Microsoft.CustomProviders/resourceProviders szolgáltatáson**keresztül. Az egyéni erőforrás-szolgáltató ezután elérhetővé teszi ezeket az új API-kat az Azure-ban. Az Azure egyéni erőforrás-szolgáltatók három részből állnak: egyéni erőforrás-szolgáltató, **végpontok**és egyéni erőforrások.

## <a name="how-to-build-custom-resource-providers"></a>Egyéni erőforrás-szolgáltatók létrehozása

Az egyéni erőforrás-szolgáltatók az Azure és a végpontok közötti szerződések listáját tartalmazza. Ez a szerződés leírja, hogy az Azure-nak hogyan kell egy végpontot kezelnie. Az erőforrás-szolgáltató proxyként működik, és továbbítja a kérelmeket és válaszokat a megadott **végpontra**és apontról. Az erőforrás-szolgáltató kétféle egyezményt adhat meg: [**erőforrástípusok**](./custom-providers-resources-endpoint-how-to.md) at és [**műveleteket.**](./custom-providers-action-endpoint-how-to.md) Ezek végpontdefiníciókon keresztül engedélyezettek. A végpontdefiníció három mezőből áll: **név**, **routingType**és **végpont**.

Mintavégpont:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Tulajdonság | Kötelező | Leírás
---|---|---
név | *igen* | A végpontdefiníció neve. Az Azure ezt a nevet az API-n keresztül teszi elérhetővé a "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}"
routingType típus | *nem* | A **végponthoz**kötött szerződéstípust határozza meg. Ha nincs megadva, akkor alapértelmezés szerint "Proxy".
endpoint | *igen* | A végpont, amelyhez a kérelmeket továbbítani szeretné. Ez kezeli a választ, valamint a mellékhatások a kérelem.

### <a name="building-custom-resources"></a>Egyéni erőforrások létrehozása

**A ResourceTypes az Azure-hoz** hozzáadott új egyéni erőforrásokat írja le. Ezek teszik ki az alapvető RESTful CRUD módszereket. További információk [az egyéni erőforrások létrehozásáról](./custom-providers-resources-endpoint-how-to.md)

Példa egyéni erőforrás-szolgáltató **erőforrásokkal:**

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Api-k az Azure-hoz adva a fenti mintához:

HttpMódszer | URI-minta | Leírás
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Az Azure REST API-t egy új erőforrás létrehozásához.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Az Azure REST API-hívás egy meglévő erőforrás törléséhez.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Az Azure REST API-hívás egy meglévő erőforrás lekéréséhez.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Az Azure REST API-hívás a meglévő erőforrások listájának lekéréséhez.

### <a name="building-custom-actions"></a>Egyéni műveletek létrehozása

**A műveletek** az Azure-hoz hozzáadott új műveleteket írják le. Ezek az erőforrás-szolgáltató tetején vagy egy **resourceType**alá ágyazhatók. További információk [az egyéni műveletek létrehozásáról](./custom-providers-action-endpoint-how-to.md)

Példa egyéni erőforrás-szolgáltató **műveletek**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Api-k az Azure-hoz adva a fenti mintához:

HttpMódszer | URI-minta | Leírás
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Az Azure REST API-hívás a művelet aktiválásához.

## <a name="looking-for-help"></a>Segítségre van szüksége

Ha kérdései vannak az Azure egyéni erőforrás-szolgáltató fejlesztésével kapcsolatban, próbálja meg feltenni a [Veremtúlcsordulást.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Lehet, hogy egy hasonló kérdést már feltettek és megválaszoltak, ezért először ellenőrizze a feladás előtt. Add hozzá ```azure-custom-providers``` a címkét, hogy gyors választ kapj!

## <a name="next-steps"></a>További lépések

Ebben a cikkben az egyéni szolgáltatókról szerzett tudomást. Egyéni szolgáltató létrehozásához lépjen a következő cikkre.

- [Rövid útmutató: Hozzon létre egyéni erőforrás-szolgáltatót, és telepítsen egyéni erőforrásokat](./create-custom-provider.md)
- [Oktatóanyag: Egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Útmutató: Egyéni műveletek hozzáadása az Azure REST API-hoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST API-hoz](./custom-providers-resources-endpoint-how-to.md)
