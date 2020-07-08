---
title: Reagálás az Azure app Configuration kulcs-érték eseményeire
description: Azure Event Grid használata az alkalmazás-konfigurációs eseményekre való előfizetéshez.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77523798"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Az Azure-alkalmazás konfigurációs eseményeire való reagálás

Az Azure-alkalmazás konfigurációs eseményei lehetővé teszik az alkalmazások számára a Key-Values változásokra való reagálást. Ez bonyolult kód vagy költséges és nem hatékony lekérdezési szolgáltatások szükségessége nélkül történik. Ehelyett az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az előfizetők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját egyéni HTTP-figyelő számára is leküldik. Kritikus fontosságú, hogy csak a ténylegesen használt funkciókért kell fizetnie.

Az Azure-alkalmazás konfigurációs eseményeit a rendszer elküldi a Azure Event Grid, amely megbízható kézbesítési szolgáltatásokat biztosít az alkalmazásai számára a részletes újrapróbálkozási szabályzatok és a kézbesítetlen levelek kézbesítése révén. További információ: [Event Grid üzenet kézbesítése, és próbálkozzon újra](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Az alkalmazások konfigurációjának gyakori forgatókönyvei közé tartozik például az alkalmazás konfigurációjának frissítése, a központi telepítések elindítása vagy bármely konfigurációs irányú munkafolyamat. Ha a módosítások nem ritkák, de a forgatókönyvnek azonnali reagálásra van szüksége, az eseményvezérelt architektúra különösen hatékony lehet.

Tekintse át az [Azure-alkalmazás konfigurációs eseményeinek átirányítását egy egyéni webes végpontra – a CLI-t](./howto-app-configuration-event.md) egy gyors példaként. 

![Event Grid modell](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Elérhető Azure-alkalmazás konfigurációs eseményei
Az Event Grid [esemény-előfizetések](../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Az Azure-alkalmazás konfigurációs esemény-előfizetései két típusú eseményt tartalmazhatnak:  

> |Esemény neve|Description|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Kulcs-érték létrehozásakor vagy cseréjekor|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Kulcs-érték törlése után|

## <a name="event-schema"></a>Eseményséma
Az Azure-alkalmazás konfigurációs eseményei tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja az alkalmazás konfigurációs eseményeit, mert a eventType tulajdonság a "Microsoft. AppConfiguration" karakterlánccal kezdődik. Event Grid esemény tulajdonságainak használatáról további információt [Event Grid Event Schema](../event-grid/event-schema.md)dokumentációban olvashat.  

> |Tulajdonság|Típus|Description|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |témakör|sztring|Az eseményt kibocsátó alkalmazás-konfiguráció teljes Azure Resource Manager azonosítója.|
> |tulajdonos|sztring|Az esemény tárgyát képező kulcs-érték URI-ja.|
> |eventTime|sztring|Az esemény létrehozásának dátuma és időpontja ISO 8601 formátumban.|
> |eventType|sztring|"Microsoft. AppConfiguration. KeyValueModified" vagy "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Azonosító|sztring|Az esemény egyedi azonosítója.|
> |dataVersion|sztring|Az adatobjektum sémaverziója.|
> |metadataVersion|sztring|A legfelső szintű tulajdonságok sémájának verziója.|
> |adatok|objektum|Az Azure-alkalmazások konfigurációjának adott eseményeinek gyűjteménye|
> |adat. kulcs|sztring|A módosított vagy törölt kulcs-érték kulcsa.|
> |a. label|sztring|A módosított vagy törölt kulcs-érték címkéje (ha van).|
> |az ETAG.|sztring|Az `KeyValueModified` új kulcs-érték ETAG. A `KeyValueDeleted` törölt kulcs-érték ETAG.|

Íme egy példa egy KeyValueModified eseményre:
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

További információ: Azure- [alkalmazás konfigurációs eseményeinek sémája](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai
Az alkalmazás-konfigurációs eseményeket kezelő alkalmazásoknak az alábbi ajánlott eljárásokat kell követniük:
> [!div class="checklist"]
> * Több előfizetés is konfigurálható úgy, hogy az eseményeket ugyanahhoz az eseménykezelőhöz irányítsák, ezért ne Tételezzük fel, hogy az események egy adott forrásból származnak. Ehelyett ellenőrizze az üzenet témakörét, és győződjön meg arról, hogy az alkalmazás konfigurációs példánya küldi az eseményt.
> * Tekintse meg a eventType, és ne Tételezzük fel, hogy minden kapott esemény a várt típus lesz.
> * A ETAG mezőkkel megtudhatja, hogy az objektumok adatai továbbra is naprakészek-e.  
> * Az események sorrendjének megismeréséhez használja a Sequencer mezőket az adott objektumon.
> * A tulajdonos mező használatával férhet hozzá a módosított kulcs-értékhez.


## <a name="next-steps"></a>További lépések

További információ a Event Gridről és az Azure-alkalmazások konfigurációs eseményeinek megadásáról:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
- [Azure-alkalmazás konfigurációs eseményeinek átirányítása egyéni webes végpontra](./howto-app-configuration-event.md)