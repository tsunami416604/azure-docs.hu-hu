---
title: Erőforrások előkészítése
description: Ismerje meg az erőforrás-bevezetés tanusítását az Azure Egyéni szolgáltatók használatával a felügyelet vagy a konfiguráció más Azure-erőforrástípusokra való alkalmazásához.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650408"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Az Azure egyéni szolgáltatók erőforrás-bevezetés – áttekintés

Az Azure Custom Providers erőforrás-bevezetés az Azure-erőforrástípusok bővíthetőségi modellje. Lehetővé teszi, hogy műveleteket vagy felügyeleteket alkalmazzon a meglévő Azure-erőforrások között nagy méretekben. További információ: [Hogyan terjeszthetik ki az Azure egyéni szolgáltatóit](overview.md)az Azure.For more information, in how Azure Custom Providers can extend Azure. Ez a cikk a következőket ismerteti:

- Milyen erőforrás-bevezetéstehető.
- Az erőforrások bevezetésének alapjai és használata.
- Hol találhat útmutatókat és kódmintákat a kezdéshez.

> [!IMPORTANT]
> Az egyéni szolgáltatók jelenleg nyilvános előzetes verzióban vannak.
> Ez az előzetes verzió szolgáltatásszintű szerződés nélkül érhető el, és nem javasoljuk éles számítási feladatokhoz. Előfordulhat, hogy bizonyos szolgáltatások nem támogatottak, vagy korlátozott képességekkel rendelkeznek.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="what-can-resource-onboarding-do"></a>Mire használható az erőforrás-bevezetés?

Az [Azure Custom Providers egyéni erőforrásokhoz](./custom-providers-resources-endpoint-how-to.md)hasonlóan az erőforrás-bevezetés egy olyan szerződést határoz meg, amely proxy "bevezetési" kérelmeket küld egy végpontra. Az egyéni erőforrásokkal ellentétben az erőforrás-bevezetés nem hoz létre új erőforrástípust. Ehelyett lehetővé teszi a meglévő erőforrástípusok kiterjesztését. Az erőforrás-bevezetés pedig az Azure Policy-del működik, így az erőforrások kezelése és konfigurálása nagy méretekben végezhető el. Néhány példa az erőforrás-bevezetési munkafolyamatokra:

- Telepítse és kezelje a virtuálisgép-bővítményeket.
- Töltse fel és konfigurálja az alapértelmezett azure-tárfiókok.
- Az alapdiagnosztikai beállítások engedélyezése nagy léptékben.

## <a name="resource-onboarding-basics"></a>Az erőforrások bevezetésének alapjai

Az Azure egyéni szolgáltatókon keresztül konfigurálhatja az erőforrás-bevezetést a Microsoft.CustomProviders/resourceProviders és a Microsoft.CustomProviders/associations erőforrástípusok használatával. Egyéni szolgáltató erőforrás-bevezetésengedélyezéséhez a konfigurációs folyamat során hozzon létre egy "társítások" nevű **resourceType-ot** egy "bővítményt" tartalmazó **routingType típussal.** A Microsoft.CustomProviders/associations és a Microsoft.CustomProviders/resourceProviders nem kell ugyanahhoz az erőforráscsoporthoz tartoznia.

Íme egy minta Az Azure egyéni szolgáltató:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Tulajdonság | Kötelező? | Leírás
---|---|---
név | Igen | A végpontdefiníció neve. Az erőforrás-bevezetéshez a névnek "társítások" kell lennie.
routingType típus | Igen | A végpontdal kötött szerződés típusát határozza meg. Az erőforrás-bevezetés esetén az érvényes **úttervtípusok** a "Proxy,Cache,Extension" és a "Webhook,Cache,Extension".
endpoint | Igen | A végpont, amelyhez a kérelmeket továbbítani szeretné. Ez kezeli a választ, és minden mellékhatása a kérelem.

Miután létrehozta az egyéni szolgáltatót a társítások erőforrástípussal, a Microsoft.CustomProviders/associations használatával célozhat. A Microsoft.CustomProviders/associations egy olyan bővítményerőforrás, amely bármely más Azure-erőforrást kiterjeszthet. A Microsoft.CustomProviders/associations egy példányának létrehozásakor egy **tulajdonság targetResourceId**azonosítót kap, amely érvényes Microsoft.CustomProviders/resourceProviders vagy Microsoft.Solutions/applications erőforrás-azonosító lesz. Ezekben az esetekben a kérelem a Microsoft.CustomProviders/resourceProviders által létrehozott példány társítási erőforrástípusához lesz továbbítva.

> [!NOTE]
> Ha a Microsoft.Solutions/applications erőforrásazonosító t a **targetResourceId**azonosítóként adja meg, akkor a felügyelt erőforráscsoportban "nyilvános" nevű Microsoft.CustomProviders/resourceProviders kell lennie.

Minta Az Azure egyéni szolgáltatók társítása:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Tulajdonság | Kötelező? | Leírás
---|---|---
targetResourceId azonosító | Igen | A Microsoft.CustomProviders/resourceProviders vagy a Microsoft.Solutions/applications erőforrásazonosítója.

## <a name="how-to-use-resource-onboarding"></a>Az erőforrás-bevezetés használata

Az erőforrás-bevezetés úgy működik, hogy más erőforrásokat is kiterjeszt a Microsoft.CustomProviders/associations bővítményerőforrással. A következő mintában a kérelem egy virtuális gépre, de bármely erőforrás bővíthető.

Először létre kell hoznia egy egyéni szolgáltatói erőforrást társítások erőforrástípussal. Ez deklarálja a visszahívási URL-címet, amelyet a rendszer a megfelelő Microsoft.CustomProviders/associations erőforrás létrehozásakor fog használni, amely az egyéni szolgáltatót célozza meg.

Minta Microsoft.CustomProviders/resourceProviders create request:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Az egyéni szolgáltató létrehozása után más erőforrásokat is megcélozhat, és rájuk alkalmazhatja az egyéni szolgáltató mellékhatásait.

Minta Microsoft.CustomProviders/associations create request:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Ezt a kérést a rendszer továbbítja a létrehozott egyéni szolgáltatóban megadott végpontra, amelyre a **targetResourceId** hivatkozik ezen a képernyőn:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

A végpontnak egy alkalmazás/json `Content-Type` nal és egy érvényes JSON választörzskel kell válaszolnia. A JSON **tulajdonságobjektuma** alatt visszaadott mezők hozzáadódnak a társítási visszatérési válaszhoz.

## <a name="getting-help"></a>Segítség kérése

Ha kérdése van az Azure egyéni erőforrás-szolgáltatók fejlesztésével kapcsolatban, próbálja meg feltenni őket a [Veremtúlcsordulás szolgáltatásban.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Lehet, hogy egy hasonló kérdésre már válaszoltak, ezért először a feladás előtt ellenőrizze. Add hozzá ```azure-custom-providers``` a címkét, hogy gyors választ kapj!

## <a name="next-steps"></a>További lépések

Ebben a cikkben az egyéni szolgáltatókról szerzett tudomást. További információ:

- [Oktatóanyag: Erőforrás-bevezetés egyéni szolgáltatókkal](./tutorial-resource-onboarding.md)
- [Oktatóanyag: Egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Rövid útmutató: Egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Útmutató: Egyéni műveletek hozzáadása azure REST API-hoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása Azure REST API-hoz](./custom-providers-resources-endpoint-how-to.md)
