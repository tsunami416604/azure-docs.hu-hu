---
title: Reagálás az Azure App konfigurációs kulcsérték-eseményeire
description: Az Azure Event Grid használatával előfizethet az alkalmazáskonfigurációs eseményekre.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523798"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagálás az Azure App konfigurációs eseményeire

Az Azure App Configuration események lehetővé teszik, hogy az alkalmazások reagáljanak a kulcsértékek változásaira. Ez bonyolult kód vagy költséges és nem hatékony szavazási szolgáltatások nélkül történik. Ehelyett az események et az [Azure Event Grid-ön](https://azure.microsoft.com/services/event-grid/) keresztül az előfizetők, például [az Azure Functions,](https://azure.microsoft.com/services/functions/) [az Azure Logic Apps,](https://azure.microsoft.com/services/logic-apps/)vagy akár a saját egyéni http-figyelő. Kritikusan, csak akkor fizet, amit használ.

Az Azure App Configuration események et az Azure Event Grid, amely megbízható kézbesítési szolgáltatásokat nyújt az alkalmazások gazdag újrapróbálkozási szabályzatok és a kézbesítési kézbesítés. További információ: [Event Grid üzenet kézbesítése és újra próbálkozás](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

A gyakori alkalmazáskonfigurációs eseményforgatókönyvek közé tartozik az alkalmazáskonfiguráció frissítése, a központi telepítések vagy bármely konfigurációorientált munkafolyamat. Ha a módosítások ritkán fordulnak elő, de a forgatókönyv azonnali válaszidőt igényel, az eseményalapú architektúra különösen hatékony lehet.

Vessen egy pillantást [a Route Azure App Konfigurációs események egy egyéni webes végpont - CLI](./howto-app-configuration-event.md) egy gyors példa. 

![Eseményrács-modell](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Elérhető Azure App Konfigurációs események
Az eseményrács [esemény-előfizetéseket](../event-grid/concepts.md#event-subscriptions) használ az eseményüzenetek előfizetőkhöz való irányításához. Az Azure App Configuration esemény-előfizetések kétféle eseményt tartalmazhatnak:  

> |Esemény neve|Leírás|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Kulcsérték létrehozásakor vagy cseréjekor kilőve|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Kulcsérték törlésekor kilőve|

## <a name="event-schema"></a>Eseményséma
Az Azure App konfigurációs eseményei tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Az alkalmazáskonfigurációs eseményeket azonosíthatja, mert az eventType tulajdonság "Microsoft.AppConfiguration" néven kezdődik. Az Event Grid eseménytulajdonságainak használatáról további információt az [Event Grid eseménysémája](../event-grid/event-schema.md)dokumentál.  

> |Tulajdonság|Típus|Leírás|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |témakör|sztring|Az eseményt kibocsátó alkalmazáskonfiguráció teljes Azure-erőforrás-kezelőazonosítója.|
> |Tárgy|sztring|Az esemény tárgyát képező kulcsérték URI-ja.|
> |eventTime|sztring|Az esemény létrehozásának dátuma/időpontja, ISO 8601 formátumban.|
> |eventType|sztring|"Microsoft.AppConfiguration.KeyValueModified" vagy "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Azonosító|sztring|Az esemény egyedi azonosítója.|
> |dataVersion|sztring|Az adatobjektum sémaverziója.|
> |metadataVersion|sztring|A legfelső szintű tulajdonságok sémaverziója.|
> |data|objektum|Az Azure App konfigurációjának konkrét eseményadatainak gyűjtése|
> |data.key|sztring|A módosított vagy törölt kulcsérték kulcsa.|
> |data.label|sztring|A módosított vagy törölt kulcsérték címkéje(ha van).|
> |data.etag|sztring|Az `KeyValueModified` új kulcs-érték e-címkéje esetén. A `KeyValueDeleted` törölt kulcsérték e-címkéje esetén.|

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

További információ: [Azure App Configuration events schema](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Az események fogyasztásának gyakorlata
Az alkalmazáskonfigurációs eseményeket kezelő alkalmazásoknak az alábbi ajánlott eljárásokat kell követniük:
> [!div class="checklist"]
> * Több előfizetés konfigurálható úgy, hogy az eseményeket ugyanarra az eseménykezelőre irányítsa, ezért ne feltételezze, hogy az események egy adott forrásból származnak. Ehelyett ellenőrizze az üzenet témakörét, hogy az alkalmazáskonfigurációs példány küldje el az eseményt.
> * Ellenőrizze az eventType,és nem feltételezik, hogy minden esemény kap lesz a várt típusú.
> * Az etag mezők segítségével megtudhatja, hogy az objektumokkal kapcsolatos adatai továbbra is naprakészek-e.  
> * A szekvenszer mezők segítségével megismerheti az adott objektum eseményeinek sorrendjét.
> * A tárgy mező segítségével érheti el a módosított kulcsértéket.


## <a name="next-steps"></a>További lépések

Tudjon meg többet az Event Gridről, és próbálja ki az Azure App Konfigurációs eseményeit:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)
- [Az Azure App Konfigurációs eseményeinek irányítása egyéni webvégpontra](./howto-app-configuration-event.md)