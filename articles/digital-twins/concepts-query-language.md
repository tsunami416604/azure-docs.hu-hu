---
title: Lekérdezés nyelve
titleSuffix: Azure Digital Twins
description: Ismerje meg az Azure Digital Twins Query Store nyelvének alapjait.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: f7e9a76309b4d9dcd010b85d1b55f340374be5c4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337925"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Az Azure Digital Twins lekérdezési nyelvének ismertetése

Ne felejtse el, hogy az Azure Digital Twins középpontja a **digitális ikrekből** és **kapcsolatokból**kialakított [**Twin gráf**](concepts-twins-graph.md). Ez a gráf lekérdezhető a digitális ikrek és a benne található kapcsolatok adatainak lekérdezéséhez. Ezeket a lekérdezéseket az **Azure Digital Twins Query Store Language**nevű egyéni SQL-szerű lekérdezési nyelven írták.

Ha egy ügyfélalkalmazás számára szeretne lekérdezést küldeni a szolgáltatásnak, az Azure Digital Twins [**lekérdezési API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)-ját fogja használni. Ez lehetővé teszi, hogy a fejlesztők írási lekérdezéseket és szűrőket alkalmazzanak a digitális ikrek készletének megkereséséhez a Twin gráfban, valamint az Azure digitális Twins forgatókönyvével kapcsolatos további információkat.

## <a name="query-language-features"></a>Nyelvi funkciók lekérdezése

Az Azure Digital Twins kiterjedt lekérdezési képességeket biztosít a Twin gráfhoz képest. A lekérdezések SQL-szerű szintaxissal vannak leírva, hasonlóan a lekérdezési nyelvhez, mint a számos hasonló funkcióval rendelkező [IoT hub lekérdezési nyelv](../iot-hub/iot-hub-devguide-query-language.md) .

> [!NOTE]
> Az összes Azure digitális Twins lekérdezési művelet megkülönbözteti a kis-és nagybetűket.

Az alábbi műveletek érhetők el az Azure Digital Twins Query Store nyelvén:
* Az ikreket a digitális Twins tulajdonságai (beleértve a [címkéket](how-to-use-tags.md)is) alapján szerezheti be.
* Az ikrek a digitális Twins-felületek használatával szerezhetők be.
* Az ikrek a kapcsolati tulajdonságok alapján szerezhetők be.
* Az ikrek több kapcsolati típust ( `JOIN` lekérdezéseket) kapnak. Az s megengedett számának korlátozásai érvényesek `JOIN` (egy szint a nyilvános előzetes verzió esetében).
* Használja az egyéni függvényt `IS_OF_MODEL(twinCollection, twinTypeName)` , amely lehetővé teszi a Twin [modell](concepts-models.md)alapján történő szűrést. Támogatja az öröklést.
* Skaláris függvények használata:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` , `ENDS_WITH` .
* Lekérdezés-összehasonlító operátorok használata:,,, `IN` / `NIN` `=` `!=` `<` , `>` , `<=` , `>=` .
* A fentiek bármely kombinációját ( `AND` , `OR` , `NOT` operátor) használja.
* A folytatás használata: a lekérdezési objektum egy Oldalméret (legfeljebb 100) használatával hozható létre. A digitális ikrek egy oldalát egyszerre is lekérheti, ha a folytatási tokent az API-ra irányuló további hívásokban is megadja.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan írhat lekérdezéseket, és hogyan tekintheti meg az ügyféloldali kód példáit [*: a Twin gráf lekérdezése*](how-to-query-graph.md).
