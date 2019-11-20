---
title: Újraműködés az Azure-alkalmazás konfigurációs kulcs-érték eseményeivel | Microsoft Docs
description: Azure Event Grid használata az alkalmazás-konfigurációs eseményekre való előfizetéshez.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 5da64155f2823712eee7a60427b1c1e80abec068
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185300"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Az Azure-alkalmazás konfigurációs eseményeire való reagálás

Az Azure-alkalmazás konfigurációs eseményei lehetővé teszik az alkalmazások számára a Key-Values változásokra való reagálást. Ez bonyolult kód vagy költséges és nem hatékony lekérdezési szolgáltatások szükségessége nélkül történik. Ehelyett az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az előfizetők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját egyéni HTTP-figyelő számára is leküldik, és csak azért kell fizetnie, amit ténylegesen használ.

Az Azure-alkalmazás konfigurációs eseményeit a rendszer elküldi a Azure Event Grid, amely megbízható kézbesítési szolgáltatásokat biztosít az alkalmazásai számára a részletes újrapróbálkozási szabályzatok és a kézbesítetlen levelek kézbesítése révén. További információ: [Event Grid üzenet kézbesítése, és próbálkozzon újra](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Az alkalmazások konfigurációjának gyakori forgatókönyvei közé tartozik például az alkalmazás konfigurációjának frissítése, a központi telepítések elindítása vagy bármely konfigurációs irányú munkafolyamat. Ha a módosítások nem ritkák, de a forgatókönyvnek azonnali reagálásra van szüksége, az eseményvezérelt architektúra különösen hatékony lehet.

Tekintse át az [Azure-alkalmazás konfigurációs eseményeinek átirányítását egy egyéni webes végpontra – a CLI-t](./howto-app-configuration-event.md) egy gyors példaként. 

![Event Grid modell](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Elérhető Azure-alkalmazás konfigurációs eseményei
Az Event Grid [esemény-előfizetések](../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Az Azure-alkalmazás konfigurációs esemény-előfizetései két típusú eseményt tartalmazhatnak:  

> |Esemény neve|Leírás|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Kulcs-érték létrehozásakor vagy cseréjekor|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Kulcs-érték törlése után|

## <a name="event-schema"></a>Eseményséma
Az Azure-alkalmazás konfigurációs eseményei tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja az alkalmazás konfigurációs eseményeit, mert a eventType tulajdonság a "Microsoft. AppConfiguration" karakterlánccal kezdődik. Event Grid esemény tulajdonságainak használatáról további információt [Event Grid Event Schema](../event-grid/event-schema.md)dokumentációban olvashat.  

> |Tulajdonság|Típus|Leírás|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |témakör|sztring|Az eseményt kibocsátó alkalmazás-konfiguráció teljes Azure Resource Manager azonosítója.|
> |subject|sztring|Az esemény tárgyát képező kulcs-érték URI-ja.|
> |eventTime|sztring|Az esemény létrehozásának dátuma és időpontja ISO 8601 formátumban.|
> |eventType|sztring|"Microsoft. AppConfiguration. KeyValueModified" vagy "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Azonosító|sztring|Az esemény egyedi azonosítója.|
> |dataVersion|sztring|Az adatobjektum séma-verziója.|
> |metadataVersion|sztring|A legfelső szintű tulajdonságok sémájának verziója.|
> |data|objektum|Az Azure-alkalmazások konfigurációjának adott eseményeinek gyűjteménye|
> |adat. kulcs|sztring|A módosított vagy törölt kulcs-érték kulcsa.|
> |a. label|sztring|A módosított vagy törölt kulcs-érték címkéje (ha van).|
> |az ETAG.|sztring|`KeyValueModified` az új kulcs-érték ETAG. `KeyValueDeleted` a törölt kulcs-érték ETAG.|

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
Az alkalmazás-konfigurációs eseményeket kezelő alkalmazásoknak néhány ajánlott eljárást kell követniük:
> [!div class="checklist"]
> * Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való átirányításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével biztosíthatja, hogy a várt alkalmazás-konfigurációból származik.
> * Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
> * Mivel az üzenetek nem érkeznek meg a sorrendbe, és némi késés után a ETAG mezőkből megtudhatja, hogy az objektumokkal kapcsolatos információk továbbra is naprakészek-e.  Emellett a Sequencer mezőket is használhatja az események sorrendjének megismeréséhez egy adott objektumra vonatkozóan.
> * A tulajdonos mező használatával férhet hozzá a módosított kulcs-értékhez.


## <a name="next-steps"></a>Következő lépések

További információ a Event Gridről és az Azure-alkalmazások konfigurációs eseményeinek megadásáról:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
- [Azure-alkalmazás konfigurációs eseményeinek átirányítása egyéni webes végpontra](./howto-app-configuration-event.md)