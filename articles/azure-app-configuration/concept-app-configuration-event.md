---
title: Az Azure-alkalmazások konfigurálása kulcs-érték eseményekre reakciójú |} A Microsoft Docs
description: Azure Event Grid használatával fizessen elő az alkalmazás konfigurációs eseményekre.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735646"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagálás eseményekre az Azure-alkalmazások konfigurálása

Az Azure App-konfigurációs események lehetővé teszik az alkalmazások reagálhat a változásokra kulcs-értékeket. Ez történik, nincs szükség bonyolult kód vagy költséges és hatékony lekérdezési szolgáltatásokat. Ehelyett eseményt leküld [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) például előfizetők [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját egyéni http-figyelőt, és csak akkor Mit kell fizetni.

Az Azure App-konfigurációs események az Azure Event Griddel, amely biztosít a megbízható kézbesítést szolgáltatásokat az alkalmazások sokoldalú újrapróbálkozási szabályzatok és a kézbesíthetetlen levelek kézbesítési érkeznek. További tudnivalókért lásd: [Event Grid az üzenetek kézbesítését, és ismételje meg](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Általános konfiguráció eseményvezérelt forgatókönyvek között szerepel az alkalmazáskonfiguráció, üzembe helyezésére, illetve bármely konfigurációs-orientált munkafolyamat elindítása. Módosítások ritkák, de a forgatókönyvhöz szükséges azonnali válaszképességét, eseményalapú architektúrát különösen hatékony is lehet.

Vessen egy pillantást [útvonal az Azure-alkalmazások konfigurálása az eseményeket az egyéni webes végpont - CLI](./howto-app-configuration-event.md) rövid példában. 

![Event Grid-modell](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Elérhető az Azure App-konfigurációs események
Event grid használ [esemény-előfizetések](../event-grid/concepts.md#event-subscriptions) eseményt üzenetek továbbítását-előfizetők számára. Alkalmazások konfigurálása az Azure esemény-előfizetések lehetnek kétféle esemény:  

> |Esemény neve|Leírás|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Akkor, ha a kulcs-érték jön létre vagy cseréje|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Akkor, ha a kulcs-érték törlése|

## <a name="event-schema"></a>Eseményséma
Az Azure App-konfigurációs események kell reagálni az igények változásaira az adatokban minden információt tartalmaznak. Az alkalmazás konfigurációs esemény is azonosítani, mert az esemény típusa tulajdonság "Microsoft.AppConfiguration" karakterlánccal kezdődik. További információ a használati Event Grid-esemény tulajdonságainak leírása itt található [Event Grid-esemény séma](../event-grid/event-schema.md).  

> |Tulajdonság|Típus|Leírás|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Teljes Azure Resource Manager azonosítója, amely az eseményt bocsát ki az alkalmazáskonfigurációt.|
> |subject|string|Az URI-ját az esemény tárgyát képező kulcs-érték.|
> |eventTime|string|A dátum/idő, hogy az eseményt a rendszer létrehozta, ISO 8601 formátumban.|
> |eventType|string|"Microsoft.AppConfiguration.KeyValueModified" or "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Azonosító|string|Ez az esemény egyedi azonosítója.|
> |dataVersion|string|Az adatobjektum sémaverziója.|
> |metadataVersion|string|A legfelső szintű tulajdonságok sémaverziója.|
> |data|objektum|Az Azure-alkalmazások konfigurálása adott esemény adatok gyűjteménye|
> |Data.key|string|A kulcs-érték módosított vagy törölt kulcsa.|
> |data.label|string|A címke, ha bármely, a kulcs-érték, amely módosították vagy törölték.|
> |Data.ETag|string|A `KeyValueModified` etag címkéje az új kulcs-érték. A `KeyValueDeleted` etag címkéje a kulcs-érték, amely törölve lett.|

Íme egy példa egy KeyValueModified esemény:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

További információkért lásd: [Azure alkalmazás-konfigurációs események sémája](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Eljárások az események felhasználásához
Az alkalmazásokat, amelyek az alkalmazás-konfigurációs események kezeléséhez kövesse kell néhány ajánlott eljárást:
> [!div class="checklist"]
> * Több előfizetés is beállíthatók úgy, hogy az azonos eseménykezelő események átirányítása, fontos, nem egy adott forrásból származó események feltételezik, de ellenőrizze annak érdekében, hogy a várt alkalmazás konfigurációjából származik az üzenet a témakörben.
> * Ehhez hasonlóan ellenőrizze, hogy az esemény típusa egy folyamat kész, és nem feltételezi, hogy kap az összes esemény lesz-e a várt típusú.
> * Üzenetek érkezésekor is sorrendben, és némi várakozás után, ha még mindig naprakész állapotban-e az adatok és objektumok megértése az etag-mezők használatával.  A sequencer mezőket is, használja a tudni, hogy egy adott objektumra események sorrendje.
> * A kulcs-érték módosított eléréséhez használja a tulajdonos mezőt.


## <a name="next-steps"></a>További lépések

További információ az Event Grid, és próbálja az Azure App-konfigurációs események:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
- [Alkalmazások konfigurálása az Azure-események átirányítása egyéni webes végpontra](./howto-app-configuration-event.md)