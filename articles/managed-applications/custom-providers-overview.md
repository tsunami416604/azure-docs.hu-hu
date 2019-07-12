---
title: Az Azure egyéni erőforrás-szolgáltatók áttekintése
description: További tudnivalók az Azure egyéni erőforrás-szolgáltatók és bővítése az Azure API adatsík a munkafolyamatok megfelelően.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796087"
---
# <a name="azure-custom-resource-providers-overview"></a>Az Azure egyéni erőforrás-szolgáltatók áttekintése

Az Azure egyéni erőforrás-szolgáltatók a egy bővíthetőség platformja, az Azure-bA. Ez lehetővé teszi, hogy megadhat egyéni API-k, az alapértelmezett feldúsítani használható Azure-élményt. Ez a dokumentáció részletesen ismerteti:

- Hogyan készíthet és helyezhet üzembe egy Azure egyéni erőforrás-szolgáltató.
- Hogyan hasznosítható Azure egyéni erőforrás-szolgáltatók kiterjesztése a meglévő munkafolyamatokba.
- Hol található a kezdéshez útmutatók és Kódminták.

![Egyéni szolgáltatók áttekintése](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Egyéni szolgáltatók jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Mit lehet egyéni erőforrás-szolgáltatók?

Íme néhány példa a Mit érhet el az Azure egyéni erőforrás-szolgáltatók:

- Terjessze ki az Azure Resource Manager REST API-t a belső és külső szolgáltatások közé tartozik.
- Engedélyezze a meglévő Azure-munkafolyamatok épülő egyéni példahelyzetekre.
- Testre szabhatja az Azure Resource Manager-sablonok vezérlő és a hatása.

## <a name="what-is-a-custom-resource-provider"></a>Mi az egyéni erőforrás-szolgáltató

Az Azure egyéni erőforrás-szolgáltató között az Azure és a végpont szerződés létrehozásával történik. Ez a Szerződés meghatározása új erőforráshoz és művelethez egy új erőforrást keresztül listáját **Microsoft.CustomProviders/resourceProviders**. Az egyéni erőforrás-szolgáltató lesz majd elérhetővé ezen új API-k az Azure-ban. Az Azure egyéni erőforrás-szolgáltató három részből állnak: egyéni erőforrás-szolgáltató, **végpontok**, és az egyéni erőforrásokat.

## <a name="how-to-build-custom-resource-providers"></a>Hogyan hozhat létre egyéni erőforrás-szolgáltatók

Egyéni erőforrás-szolgáltatók a következők: Azure és a végpontok közötti szerződések listáját. A jelen szerződés ismerteti, hogyan kell használják az Azure-végpont. Az erőforrás-szolgáltató működik, mint a proxy, és továbbítja a kérelmeket és válaszokat, és a megadott **végpont**. Erőforrás-szolgáltató két típusú szerződéseket is megadhatja: [ **erőforrástípusok** ](./custom-providers-resources-endpoint-how-to.md) és [ **műveletek**](./custom-providers-action-endpoint-how-to.md). Ezek segítségével szolgáltatásvégpont-definíció engedélyezve vannak. Egy végpont-definíció három mezőből áll: **neve**, **routingType**, és **végpont**.

Minta-végpont:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Tulajdonság | Szükséges | Leírás
---|---|---
name | *igen* | A végpont-definíció neve. Azure fogja elérhetővé tenni ezt a nevet, a saját API-n keresztül "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Meghatározza, hogy a Szerződéstípus a **végpont**. Ha nincs megadva, az alapértelmezés lesz az "Proxyszolgáltató".
endpoint | *igen* | A végpontot irányíthatja a kérelmeket. Ez a válasz, valamint a kérés ügyféloldali eredő esetleges kezeli.

### <a name="building-custom-resources"></a>Egyéni erőforrások készítése

**Erőforrástípusok** ismertetik az Azure-hoz hozzáadott új egyéni erőforrásokat. Ezek teszik elérhetővé a alapszintű CRUD REST-alapú módszerek. Lásd: [egyéni erőforrások létrehozásával kapcsolatos további](./custom-providers-resources-endpoint-how-to.md)

Egyéni erőforrás-szolgáltató minta **erőforrástípusok**:

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

API-k a következőkhöz az Azure-bA a fenti példa:

HttpMethod | Minta URI | Leírás
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Az új erőforrás létrehozása az Azure REST API-hívással.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Az Azure REST API-hívás egy meglévő erőforrás törléséhez.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Az Azure REST API-hívás egy meglévő erőforrás lekérése.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Az Azure REST API-hívás a meglévő erőforrások listájának beolvasása.

### <a name="building-custom-actions"></a>Az egyéni műveletek létrehozása

**Műveletek** az Azure-hoz hozzáadott új műveleteit. Ezen felül az erőforrás-szolgáltató közzétett vagy beágyazva egy **resourceType**. Lásd: [további információk az egyéni műveletek létrehozása](./custom-providers-action-endpoint-how-to.md)

Egyéni erőforrás-szolgáltató minta **műveletek**:

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

API-k a következőkhöz az Azure-bA a fenti példa:

HttpMethod | Minta URI | Leírás
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Az Azure REST API-hívás aktiválása a műveletet.

## <a name="looking-for-help"></a>Segítségre van szüksége

Ha kérdései vannak az Azure egyéni erőforrás-szolgáltató fejlesztéshez, próbálja meg kéri [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Hasonló kérdés előfordulhat, hogy már megtörtént ismételt és válaszolt, nézzen könyvelési előtt először. A címke hozzáadása ```azure-custom-providers``` gyors válaszol!

## <a name="next-steps"></a>További lépések

Ebből a cikkből megismerhette egyéni szolgáltatókat. Nyissa meg a következő cikk az egyéni szolgáltató létrehozásához.

- [Rövid útmutató: Az Azure egyéni erőforrás-szolgáltató létrehozása és telepítése az egyéni erőforrások](./create-custom-provider.md)
- [Oktatóanyag: Egyéni műveletek és az erőforrások létrehozása az Azure-ban](./tutorial-custom-providers-101.md)
- [Útmutató: Az egyéni műveletek hozzáadása az Azure REST API-val](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST API-val](./custom-providers-resources-endpoint-how-to.md)
