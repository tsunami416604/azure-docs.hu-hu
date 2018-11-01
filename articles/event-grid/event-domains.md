---
title: Esemény tartományok az Azure Event Grid
description: Ismerteti a esemény tartományok használata kezelheti az Azure Event Grid témaköreiben.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634056"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Event Grid-témakörök kezelésére szolgáló esemény tartományok ismertetése

Ez a cikk ismerteti az esemény tartományok használata kezelheti a különféle üzleti cégek vagy intézmények, ügyfelek és alkalmazások egyéni eseményeit a folyamatot. Az esemény-tartományok használata:

* Kezelheti a több-bérlős eseménykezelési architektúrák ipari méretekben.
* Az engedélyezés és hitelesítés kezeléséhez.
* A témakörök partíció mindegyike külön-külön kezelése nélkül.
* Kerülje el a témakör végpontok mindegyike külön-külön közzététele.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>Esemény tartományok áttekintése

Egy esemény-tartomány egy felügyeleti eszköz, az Event Grid-témakörök ugyanazzal az alkalmazással kapcsolatos nagy számú. Ön felfoghatjuk úgy, mint metaadat-Ez a témakör az egyes témakörök ezer tartalmazhat.

Esemény tartományok győződjön meg arról, az architektúra az Azure services, Storage és az IoT Hub használatával elvégezhető a közzététel használhatja a saját rendszerben elérhető eseményeket. Lehetővé teszik, hogy tegye közzé az eseményeket témakörök ezer. Tartományok is lehetővé teszik, minden a témakör a hitelesítési és engedélyezési felett, így a bérlők particionáló.

### <a name="example-use-case"></a>Példa használati esetekhez

Esemény tartományok a legkönnyebben magyarázata példa használatával. Tegyük fel, Contoso konstrukció gép, ahol gyártott traktorok, eszközök és egyéb gyakori gépek digging futtatja. Egy futtatásának részeként az üzleti, valós idejű információk leküldése ügyfelek kapcsolatos berendezések karbantartás, rendszerek állapot-szerződést kötöttek a frissítéseknek stb. Az összes ezeket az adatokat különböző végpontok, beleértve az alkalmazás, ügyfél-végpontok kerül, és más infrastruktúra-ügyfelek beállítása után.

Esemény tartományok lehetővé teszi a modell Contoso konstrukció gépek eseménykezelési egyetlen egységként. Az ügyfelek mindegyike jelenik meg a témakör a tartományban / hitelesítési és engedélyezési kezelése Azure Active Directory használatával. Az ügyfelek mindegyike feliratkozunk a témakörre, és azok szállított események működnek beolvasása. Az esemény tartományon keresztül felügyelt hozzáférést biztosítja, hogy csak azok a témakör eléréséhez.

Azt is lehetővé teszi egy végpontot, amely minden, az ügyfél közzéteheti. Event Grid gondoskodik a gondoskodik róla, hogy minden a témakör csak a bérlő hatóköre események tisztában.

![Contoso konstrukció példa](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Hozzáférés-kezelés

 Egy tartomány első részletes hitelesítési és engedélyezési vezérlése minden témakörhöz keresztül az Azure szerepköralapú hozzáférés ellenőrzése (RBAC) keresztül. Ezek a szerepkörök segítségével minden bérlő korlátozása csak a kívánt hozzáférést biztosít nekik a témakörök az alkalmazásban.

Szerepköralapú hozzáférés ellenőrzése (RBAC) esemény tartományokban ugyanúgy működik [hozzáférés-vezérlés által felügyelt](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control) Event Grid és az Azure többi működik. Az RBAC használatával hozzon létre és érvényesítsen az egyéni szerepkör-definíciók esemény tartományokban.

### <a name="built-in-roles"></a>Beépített szerepkörök

Event Grid könnyebbé tenni az RBAC két beépített szerepkör-definíciók rendelkezik:

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>EventGrid EventSubscription Közreműködője (minta)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

#### <a name="eventgrid-eventsubscription-reader-preview"></a>EventGrid EventSubscription olvasója (minta)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="subscribing-to-topics"></a>Való előfizetéssel kapcsolatos témakörök

Feliratkozik az eseményekre a témakör egy esemény-tartományon belül pedig ugyanaz, mint [egy esemény-előfizetést egy egyéni témakör létrehozása](./custom-event-quickstart.md) vagy esemény-közzétevők Azure kínálja a beépített előfizetés.

### <a name="domain-scope-subscriptions"></a>Tartományi hatókör előfizetések

Esemény tartományok is lehetővé teszik a tartományi hatókör előfizetésekhez. Egy esemény-előfizetést egy esemény tartományban a tartományhoz, függetlenül a témakör az események küldhetők az összes esemény fog kapni. Tartományi hatókör-előfizetések kezelése és naplózási célokra hasznos lehet.

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

Esemény tartományok kezeléséhez, témakörökre való közzététel. Helyett minden egyes felügyelt külön-külön témakör eseményeket tesz közzé, közzéteheti minden, a tartomány-végponthoz, és Event Grid gondoskodik arról, hogy minden a rendszer a megfelelő témakörben.

## <a name="limits-and-quotas"></a>Korlátok és kvóták

### <a name="control-plane"></a>Vezérlősík

Az előzetes verzióban Event tartományok csak egy tartományon belüli 1000 témakörök és a egy tartományon belüli témakör 50 eseményfeliratkozások lesz. Tartományi hatókör eseményelőfizetések legfeljebb 50-re is.

### <a name="data-plane"></a>Adatsík

Az előzetes verzióban Event tartomány sebességű korlátozódik második betöltési arány, amely az egyéni témakörök korlátozottak száma összesen 5 000 ugyanazokat az eseményeket.

## <a name="pricing"></a>Díjszabás

Az előzetes verzióban Event tartományok használandó azonos [díjszabás operations](https://azure.microsoft.com/pricing/details/event-grid/) , amely az Event Grid összes többi funkció használata.

Műveletek azonos esemény tartományokban működik, mint az egyéni témaköröket is. Minden belépő esemény tartományhoz egy esemény-művelet, és minden egyes kézbesítési kísérlet egy esemény egy művelet.

## <a name="next-steps"></a>További lépések

* Esemény tartományok beállításával kapcsolatos további információkért témakörök létrehozására, az esemény-előfizetések létrehozása és események, közzétételéhez lásd [esemény tartományok kezelése](./how-to-event-domains.md).