---
title: Eseménytartományok az Azure Eseményrácsban
description: Ez a cikk azt ismerteti, hogy miként használhatja az eseménytartományokat az egyéni események különböző üzleti szervezetekhez, ügyfelekhez vagy alkalmazásokhoz történő áramlásának kezeléséhez.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f6698f91d7659f9fc2c314a9291380301146f8ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898862"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Az Eseményrács témaköreikezelésének ismertetése

Ez a cikk azt ismerteti, hogy miként használhatja az eseménytartományokat az egyéni események különböző üzleti szervezetekhez, ügyfelekhez vagy alkalmazásokhoz történő áramlásának kezeléséhez. Az eseménytartományok használata:

* Több-bérlős eseménykezelési architektúrák kezelése nagy méretekben.
* Az engedélyezés és a hitelesítés kezelése.
* Particionálja a témaköröket anélkül, hogy egyenként kezelne.
* Kerülje az egyes témakörvégpontok egyéni közzétételét.

## <a name="event-domain-overview"></a>Eseménytartomány – áttekintés

Az eseménytartomány az ugyanahhoz az alkalmazáshoz kapcsolódó event grid-témakörök nagy számához tartozó felügyeleti eszköz. Azt gondolhatunk rá, mint egy meta-téma, amely több ezer egyedi témákat.

Az eseménytartományok ugyanazt az architektúrát teszik elérhetővé Az Azure-szolgáltatások (például a Storage és az IoT Hub) számára az események közzétételéhez. Ezek lehetővé teszik, hogy több ezer témakörben tegyen közzé eseményeket. A tartományok is ad engedélyezési és hitelesítési vezérlést az egyes témakörök, így particionálja a bérlők.

### <a name="example-use-case"></a>Példa használati eset

Az eseménytartományokat a legkönnyebben egy példa segítségével lehet megmagyarázni. Tegyük fel, hogy a Contoso Építőipari Gépeket üzemelteti, ahol traktorokat, ásóberendezéseket és más nehézgépeket gyárt. A vállalkozás működtetésének részeként valós idejű információkat kell elvinnie az ügyfeleknek a berendezések karbantartásáról, a rendszerek állapotáról és a szerződésfrissítésekről. Ezek az információk különböző végpontokra vonatkoznak, beleértve az alkalmazást, az ügyfélvégpontokat és az ügyfelek által beállított egyéb infrastruktúrát.

Az eseménytartományok lehetővé teszik a Contoso Construction Machinery egyetlen eseményentitásként történő modellezését. Minden ügyfél a tartományon belüli témakörként jelenik meg. A hitelesítés és az engedélyezés az Azure Active Directory használatával történik. Minden ügyfél feliratkozhat a témára, és megkaphatja az eseményeit. Az eseménytartományon keresztüli felügyelt hozzáférés biztosítja, hogy csak a témájukhoz férhessenek hozzá.

Emellett egyetlen végpontot is biztosít, amelyen közzéteheti az összes ügyféleseményt. Event Grid gondoskodik arról, hogy minden témakör csak a bérlő hatókörét.

![Contoso építési példa](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Hozzáférés-kezelés

A tartomány használatával az Azure szerepköralapú hozzáférés-vezérlésével (RBAC) finomszemcsés engedélyezési és hitelesítési vezérlést kaphat az egyes témakörök felett. Ezekkel a szerepkörökben korlátozhatja az alkalmazás minden bérlőjét csak azokra a témakörökre, amelyekhez hozzáférést kíván biztosítani számukra.

Az RBAC az eseménytartományokban ugyanúgy működik a [felügyelt hozzáférés-vezérlési](security-authorization.md) műveletekben az Event Grid és az Azure többi részében. Az RBAC használatával egyéni szerepkör-definíciókat hozhat létre és kényszeríthet ki eseménytartományokban.

### <a name="built-in-roles"></a>Beépített szerepkörök

Az Event Grid két beépített szerepkör-definícióval rendelkezik, amelyek megkönnyítik az RBAC-t az eseménytartományok használata érdekében. Ezek a szerepkörök **az EventGrid EventSubscription közreműködő (előzetes verzió)** és **az EventGrid EventSubscription-olvasó (előzetes verzió)**. Ezeket a szerepköröket olyan felhasználókhoz rendeli hozzá, akiknek elő kell fizetnie az eseménytartomány témaköreire. A szerepkör-hozzárendelést csak arra a témakörre kell áttérnie, amelyre a felhasználóknak elő kell fizetnie.

Ezekről a szerepkörökről az [Event Grid beépített szerepkörei](security-authorization.md#built-in-roles)című témakörben talál további információt.

## <a name="subscribing-to-topics"></a>Feliratkozás témákra

Egy eseménytartományon belüli témaköreseményeire való feliratkozás ugyanaz, mint egy [esemény-előfizetés létrehozása egy egyéni témakörben,](./custom-event-quickstart.md) vagy egy Azure-szolgáltatásból egy eseményre való feliratkozás.

### <a name="domain-scope-subscriptions"></a>Tartományi hatókör-előfizetések

Az eseménytartományok tartományszintű előfizetéseket is lehetővé teszik. Egy eseménytartományesemény-előfizetés e témakörtől függetlenül megkapja a tartománynak küldött összes eseményt. A tartományhatókör-előfizetések felügyeleti és naplózási célokra lehetnek hasznosak.

## <a name="publishing-to-an-event-domain"></a>Közzététel eseménytartományban

Amikor létrehoz egy eseménytartományt, akkor kap egy közzétételi végpontot, amely hasonló ahhoz, mint ha létrehozott egy témakört az Event Grid ben. 

Ha eseményeket szeretne közzétenni egy eseménytartomány bármely témakörében, az eseményeket ugyanúgy tolja a tartomány [végpontjára, mint egy egyéni témakört.](./post-to-custom-topic.md) Az egyetlen különbség az, hogy meg kell adnia azt a témakört, amelyhez az eseményt kézbesíteni szeretné.

Például a következő eseménytömb közzététele az `"id": "1111"` eseményt `foo` a témára küldi, míg az esemény `"id": "2222"` a témakörbe `bar`kerül:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Az eseménytartományok kezelik a témakörökben való közzétételt. Ahelyett, hogy minden egyes, egyenként kezelt témakörben közzétenné az eseményeket, közzéteheti az összes eseményt a tartomány végpontján. Az Event Grid gondoskodik arról, hogy minden esemény a megfelelő témakörbe kerüljön.

## <a name="limits-and-quotas"></a>Korlátok és kvóták
Az eseménytartományokhoz kapcsolódó korlátok és kvóták a következők:

- 100 000 témakör eseménytartományonként 
- 100 eseménytartomány Azure-előfizetésenként 
- 500 esemény-előfizetés témánként egy eseménytartományban
- 50 tartományhatókör-előfizetés 
- 5000 esemény másodpercenkénti betöltési arány (tartományba)

Ha ezek a korlátok nem felelnek meg Önnek, a támogatási jegy megnyitásával vagy e-mail küldésével szólítsa meg a termékcsapatot a rendszernek. [askgrid@microsoft.com](mailto:askgrid@microsoft.com) 

## <a name="pricing"></a>Díjszabás
Az eseménytartományok ugyanazokat a [műveleti árképzést](https://azure.microsoft.com/pricing/details/event-grid/) használják, mint az Event Grid összes többi szolgáltatása.

A műveletek ugyanúgy működnek az eseménytartományokban, mint az egyéni témakörökben. Egy esemény minden egyes be- és be- és bekerülési száma egy eseménytartományba egy művelet, és egy esemény minden kézbesítési kísérlete művelet.

## <a name="next-steps"></a>További lépések

* Az eseménytartományok beállításáról, a témakörök létrehozásáról, az esemény-előfizetések létrehozásáról és az események közzétételéről az [Eseménytartományok kezelése című](./how-to-event-domains.md)témakörben olvashat.
