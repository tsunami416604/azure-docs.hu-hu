---
title: Esemény tartományok az Azure Event Grid
description: Ismerteti a esemény tartományok használata kezelheti az Azure Event Grid témaköreiben.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 4e21b1bb85ba91266370d89ec8dbd0fae84ef197
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299792"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Event Grid-témakörök kezelésére szolgáló esemény tartományok ismertetése

Ez a cikk ismerteti az esemény tartományok használata kezelheti a különböző szervezetek üzleti, felhasználók vagy alkalmazások egyéni eseményeit a folyamatot. Az esemény-tartományok használata:

* Kezelheti a több-bérlős eseménykezelési architektúrák ipari méretekben.
* Az engedélyezés és hitelesítés kezeléséhez.
* A témakörök partíció mindegyike külön-külön kezelése nélkül.
* Kerülje el a témakör végpontok mindegyike külön-külön közzététele.

Ez a funkció előzetes verzióban érhető el. A használatához telepítenie kell egy minta-kiterjesztés vagy a modul. Útmutatásért lásd: [témakörök kezelése és az esemény tartományok használatával közzé az eseményeket](how-to-event-domains.md).

## <a name="event-domain-overview"></a>Esemény tartományi áttekintése

Egy esemény-tartomány egy felügyeleti eszköz, az Event Grid-témakörök ugyanazzal az alkalmazással kapcsolatos nagy számú. Felfogható, rendelkezhet több ezer, az egyes témakörök meta-témakör.

Esemény tartományok elérhetővé, az események közzététele az Azure-szolgáltatások (például a Storage és az IoT Hub) által használt azonos architektúrája. Lehetővé teszik, hogy tegye közzé az eseményeket témakörök ezer. Tartományok is lehetővé teszik, minden a témakör a hitelesítési és engedélyezési felett, így a bérlők particionáló.

### <a name="example-use-case"></a>Példa használati esetekhez

Esemény tartományok a legkönnyebben magyarázata példa használatával. Tegyük fel, Contoso konstrukció gép, ahol gyártott traktorok, eszközök és egyéb gyakori gépek digging futtatja. Egy futtatásának részeként az üzleti valós idejű információkat ügyfeleinek, berendezések karbantartási, helyrendszerek és szerződés frissítések leküldése. Különböző végpontok, beleértve az alkalmazás, ügyfél-végpontok és más ügyfelek beállított infrastruktúra az összes fenti információ irányul.

Esemény tartományok lehetővé teszik a modell Contoso konstrukció gépek eseménykezelési egyetlen egységként. Az ügyfelek mindegyike jelenik meg a témakör a tartományon belül. Hitelesítés és engedélyezés kezelése Azure Active Directory használatával. Az ügyfelek mindegyike feliratkozunk a témakörre, és azok szállított események működnek beolvasása. Az esemény tartományon keresztül felügyelt hozzáférést biztosítja, hogy csak azok a témakör eléréséhez.

Azt is lehetővé teszi egy végpontot, amely minden, az ügyfél közzéteheti. Event Grid gondoskodik a gondoskodik róla, hogy minden a témakör csak a bérlő hatóköre események tisztában.

![Contoso konstrukció példa](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Hozzáférés-kezelés

Az egy tartományi szolgáltatás részletes hitelesítési és engedélyezési vezérlése az Azure szerepköralapú hozzáférés-vezérlés (RBAC) keresztül minden egyes témakör alatt. Ezek a szerepkörök segítségével minden bérlő korlátozása csak a kívánt hozzáférést biztosít nekik a témakörök az alkalmazásban.

Esemény tartományokban RBAC ugyanúgy működik [hozzáférés-vezérlés által felügyelt](security-authentication.md#management-access-control) Event Grid és az Azure többi működik. Az RBAC használatával hozzon létre és érvényesítsen az egyéni szerepkör-definíciók esemény tartományokban.

### <a name="built-in-roles"></a>Beépített szerepkörök

Event Grid két beépített szerepkör-definíciók, hogy könnyebben esemény tartományok használata az RBAC rendelkezik. Ezek a szerepkörök felelnek **EventGrid EventSubscription Közreműködője (minta)** és **EventGrid EventSubscription olvasója (minta)**. Ezek a szerepkörök hozzárendelése az esemény tartományban témákra iratkozhat fel felhasználóknak. Csak a felhasználók igénylő előfizetni a témakör a szerepkör-hozzárendelés hatókörét.

Ezek a szerepkörök kapcsolatos információkért lásd: [beépített szerepkörök az Event Gridhez](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Való előfizetéssel kapcsolatos témakörök

Feliratkozik az eseményekre a témakör egy esemény-tartományon belül pedig ugyanaz, mint [egy esemény-előfizetést egy egyéni témakör létrehozása](./custom-event-quickstart.md) vagy feliratkozik egy eseményt az Azure-szolgáltatások.

### <a name="domain-scope-subscriptions"></a>Tartományi hatókör előfizetések

Esemény tartományok is lehetővé teszik, tartomány-hatókör előfizetés esetében. Egy esemény-előfizetést egy esemény tartományban a tartományhoz, függetlenül a témakör az események küldhetők az összes esemény fog kapni. Tartományi hatókör-előfizetések kezelése és naplózási célokra hasznos lehet.

## <a name="publishing-to-an-event-domain"></a>Egy esemény-tartományhoz való közzététel

Amikor létrehoz egy esemény-tartomány, felhőszolgáltatására egy hasonló közzététel végpontja, ha a Event Grid-témakör hozta. 

Események közzétételére bármelyik témakör egy esemény-tartományban, elküldi az eseményeket a tartomány-végpontra a [ugyanúgy egy egyéni témakör](./post-to-custom-topic.md). Az egyetlen különbség, hogy meg kell adnia az eseményt kell továbbítani szeretné a témakörben.

Például az alábbi tömböt események közzététele küld el esemény `"id": "1111"` témakörbe `foo` az esemény, miközben `"id": "2222"` küldi el a témakör `bar`:

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

Esemény tartományok kezeléséhez, témakörökre való közzététel. Minden egyes külön-külön kezelt témakörhöz közzétételi események helyett közzétehet minden, a tartomány-végpontra. Event Grid biztosítja, hogy minden a rendszer a megfelelő témakört.

## <a name="limits-and-quotas"></a>Korlátok és kvóták

### <a name="control-plane"></a>Vezérlősík

Az előzetes verzióban event tartományokban korlátozva, 1000 témakörök egy tartományon belül, és a egy tartományon belüli témakör 50 eseményfeliratkozások. Tartományi hatókör eseményelőfizetések 50 is korlátozódnak.

### <a name="data-plane"></a>Adatsík

Az előzetes verzióban event tartomány sebességű korlátozódik második betöltési arány, amely az egyéni témakörök korlátozottak száma összesen 5 000 ugyanazokat az eseményeket.

## <a name="pricing"></a>Díjszabás

Az előzetes verzióban event tartományok használata azonos [díjszabás operations](https://azure.microsoft.com/pricing/details/event-grid/) , amely az Event Grid összes többi funkció használata.

Műveletek azonos esemény tartományokban működik, mint az egyéni témaköröket is. Minden belépő esemény tartományhoz egy esemény-művelet, és minden egyes kézbesítési kísérlet egy esemény egy művelet.

## <a name="next-steps"></a>További lépések

* Esemény tartományok beállításával kapcsolatos további információkért témakörök létrehozására, az esemény-előfizetések létrehozása és események, közzétételéhez lásd [esemény tartományok kezelése](./how-to-event-domains.md).