---
title: Azure egyéni szolgáltatók erőforrás-előkészítés
description: Ismerje meg, hogy az Azure egyéni szolgáltatók használatával hogyan alkalmazhat erőforrás-előkészítést más Azure-erőforrástípusok felügyeletének és konfigurálásának elvégzéséhez.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609150"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Az Azure egyéni szolgáltatók erőforrás-előkészítésének áttekintése

Az Azure Custom Providers erőforrás-előkészítés az Azure-erőforrástípusok bővíthető modellje. Lehetővé teszi, hogy a meglévő Azure-erőforrások széles skálán alkalmazza a műveleteket vagy a felügyeletet. További információ: Hogyan bővíthető az Azure-beli [Egyéni szolgáltatók az Azure](./custom-providers-overview.md)-ban. Ez a dokumentáció a következőket ismerteti:

- Mit tehet az erőforrás-előkészítés.
- Erőforrások bevezetésének alapjai és használatuk módja.
- Hol találhatók útmutatók és kód minták az első lépésekhez.

> [!IMPORTANT]
> Az egyéni szolgáltatók jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Mit tehet az erőforrás-előkészítés?

Az egyéni [Azure-szolgáltatóhoz](./custom-providers-resources-endpoint-how-to.md)hasonlóan az egyéni erőforrások, az erőforrás-előkészítés egy szerződést határoz meg, amely a "bevezetési" kérelmeket egy végpontra küldi. Az egyéni erőforrásoktól eltérően az erőforrás-előkészítés nem hoz létre új erőforrástípust, hanem lehetővé teszi a meglévő erőforrástípusok kiterjesztését. Emellett az erőforrás-előkészítés Azure Policy is működik, így az erőforrások kezelése és konfigurálása a skálán végezhető el. Néhány példa az erőforrás-bevezetési munkafolyamatokra:

- Virtual Machines bővítmények telepítése és kezelése.
- Az alapértelmezett beállítások feltöltése és konfigurálása az Azure Storage-fiókokban.
- Az alapkonfiguráció diagnosztikai beállításainak méretezése.

## <a name="resource-onboarding-basics"></a>Az erőforrás-előkészítés alapjai

Az erőforrás-előkészítés az Azure-beli egyéni szolgáltatókon keresztül, a "Microsoft. CustomProviders/resourceProviders" és a "Microsoft. CustomProviders/Associations" típusú erőforrásokkal van konfigurálva. Ha engedélyezni szeretné az erőforrás-előkészítést egy egyéni szolgáltató számára, a konfigurációs folyamat során hozzon létre egy "társítások" nevű **resourceType** egy "bővítmény" kifejezést tartalmazó **routingType** . Emellett a "Microsoft. CustomProviders/Associations" és a "Microsoft. CustomProviders/resourceProviders" nem kell ugyanahhoz az erőforráscsoporthoz tartoznia.

Példa Azure-beli egyéni szolgáltatóra:

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

Tulajdonság | Kötelező | Leírás
---|---|---
név | *igen* | A végpont definíciójának neve. Az erőforrás-bevezetéshez a névnek "társítás"-nek kell lennie.
routingType | *igen* | Meghatározza a szerződés típusát a **végponttal**. Az erőforrások bevezetéséhez az érvényes **routingTypes** a következők: "proxy, cache, Extension" és "webhook, gyorsítótár, bővítmény".
endpoint | *igen* | A végpont, amelybe a kérelmeket át kell irányítani. Ez kezeli a választ, valamint a kérés bármely mellékhatását.

Miután létrehozta a "társítások" típusú egyéni szolgáltatót, megcélozhatja a "Microsoft. CustomProviders/Associations" kifejezést. A "Microsoft. CustomProviders/Associations" olyan kiterjesztési erőforrás, amely bármely más Azure-erőforrást kiterjesztheti. A "Microsoft. CustomProviders/Associations" nevű példány létrehozásakor a rendszer a **targetresourceid azonosítója**tulajdonságot használja, amelynek érvényes "Microsoft. CustomProviders/resourceProviders" vagy "Microsoft. Solutions/Applications" erőforrás-azonosítónak kell lennie. Ezekben az esetekben a rendszer továbbítja a kérést a "Microsoft. CustomProviders/resourceProviders" példány "társítások" erőforrástípus számára.

> [!Note]
> Ha a "Microsoft. Solutions/Applications" erőforrás-azonosító a **targetresourceid azonosítója**van megadva, akkor a felügyelt erőforráscsoporthoz a "Public" névvel telepített "Microsoft. CustomProviders/resourceProviders" értéknek kell szerepelnie.

Példa az Azure egyéni szolgáltatói társítására:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Tulajdonság | Kötelező | Leírás
---|---|---
Targetresourceid azonosítója | *igen* | A "Microsoft. CustomProviders/resourceProviders" vagy "Microsoft. Solutions/Applications" erőforrás-azonosítója.

## <a name="how-to-use-resource-onboarding"></a>Az erőforrás-előkészítés használata

Az erőforrás-előkészítés úgy működik, hogy más erőforrásokat is kiterjeszt a "Microsoft. CustomProviders/Associations" kiterjesztési erőforrással. A következő példában a rendszer egy virtuális gépre vonatkozó kérelmet küld, de az erőforrások bővíthetők.

Először létre kell hozni egy egyéni szolgáltatói erőforrást "társítás" típusú erőforrással. Ez deklarálja azt a visszahívási URL-címet, amelyet a rendszer a megfelelő "Microsoft. CustomProviders/Associations" erőforrás létrehozásakor fog használni, amely az egyéni szolgáltatóra irányul.

Minta: "Microsoft. CustomProviders/resourceProviders" Create kérelem:

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

Az egyéni szolgáltató létrehozása után már megcélozhat más erőforrásokat, és alkalmazhatja az egyéni szolgáltató mellékhatásait is.

Minta "Microsoft. CustomProviders/Associations" Create kérelem:

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

Ezt a kérést a rendszer a kezdeti létrehozott egyéni szolgáltatóban megadott végpontra továbbítja, amelyre a "Targetresourceid azonosítója" hivatkozik az űrlapon:

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

A végpontnak az "Application/JSON" `Content-Type` és érvényes JSON-válasz törzstel kell válaszolnia. A JSON "Properties" objektumában visszaadott mezők a társítás visszatérési válaszához lesznek hozzáadva.

## <a name="looking-for-help"></a>Segítség keresése

Ha kérdése van az Azure egyéni erőforrás-szolgáltatói fejlesztéséhez, próbálkozzon a [stack Overflowával](https://stackoverflow.com/questions/tagged/azure-custom-providers). Előfordulhat, hogy egy hasonló kérdést már megtettek és megválaszoltak, ezért először A feladás előtt érdemes megnézni. A címke ```azure-custom-providers``` hozzáadásával gyorsan választ kaphat!

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta az egyéni szolgáltatókat. Az egyéni szolgáltató létrehozásához nyissa meg a következő cikket.

- [Oktatóanyag: erőforrás-előkészítés egyéni szolgáltatókkal](./tutorial-custom-providers-resource-onboarding.md)
- [Oktatóanyag: egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-custom-providers-101.md)
- [Rövid útmutató: Azure egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Útmutató: egyéni műveletek hozzáadása az Azure REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: egyéni erőforrások hozzáadása az Azure REST APIhoz](./custom-providers-resources-endpoint-how-to.md)
