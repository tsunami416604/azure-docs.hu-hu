---
title: Lekérdezés nyelve
titleSuffix: Azure Digital Twins
description: Ismerje meg az Azure Digital Twins Query Store nyelvének alapjait.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 64aa85cf0a749cb335fa68f968536881b591ab45
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613144"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Az Azure Digital Twins lekérdezési nyelvének ismertetése

Ne felejtse el, hogy az Azure Digital Twins középpontja a **digitális ikrekből** és **kapcsolatokból**kialakított [**Twin gráf**](concepts-twins-graph.md). Ez a gráf lekérdezhető a digitális ikrek és a benne található kapcsolatok adatainak lekérdezéséhez. Ezeket a lekérdezéseket az **Azure Digital Twins Query Store Language**nevű egyéni SQL-szerű lekérdezési nyelven írták.

Ha egy ügyfélalkalmazás számára szeretne lekérdezést küldeni a szolgáltatásnak, az Azure Digital Twins **lekérdezési API**-ját fogja használni. Ez lehetővé teszi, hogy a fejlesztők írási lekérdezéseket és szűrőket alkalmazzanak a digitális ikrek készletének megkereséséhez a Twin gráfban, valamint az Azure digitális Twins forgatókönyvével kapcsolatos további információkat.

## <a name="query-language-features"></a>Nyelvi funkciók lekérdezése

Az Azure Digital Twins kiterjedt lekérdezési képességeket biztosít a Twin gráfhoz képest. A lekérdezések SQL-szerű szintaxissal vannak leírva, a [IoT hub lekérdezési nyelv](../iot-hub/iot-hub-devguide-query-language.md)képességeinek kibővítéseként.

Az alábbi műveletek érhetők el az Azure Digital Twins Query Store nyelvén:
* Az ikrek a digitális ikrek tulajdonságai között olvashatók be.
* Az ikrek a digitális Twins-felületek használatával szerezhetők be.
* Az ikrek a kapcsolati tulajdonságok alapján szerezhetők be.
* Az ikrek több kapcsolati típust ( `JOIN` lekérdezéseket) kapnak. Az s megengedett számának korlátozásai érvényesek `JOIN` (egy szint a nyilvános előzetes verzió esetében).
* Használja az egyéni függvényt `IS_OF_MODEL(twinCollection, twinTypeName)` , amely lehetővé teszi a Twin [modell](concepts-models.md)alapján történő szűrést. Támogatja az öröklést.
* A fentiek bármely kombinációját ( `AND` , `OR` , `NOT` operátor) használja.
* Skaláris függvények használata:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` , `ENDS_WITH` .
* Lekérdezés-összehasonlító operátorok használata:,,, `AND` / `OR` / `NOT` `IN` / `NOT IN` `STARTSWITH` / `ENDSWITH` `=` , `!=` , `<` , `>` , `<=` , `>=` .
* A folytatás használata: a lekérdezési objektum egy Oldalméret (legfeljebb 100) használatával hozható létre. A digitális ikrek egy oldalát egyszerre is lekérheti, ha ismétlődő hívásokat kezdeményez a `nextAsTwin` metódusnak.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan írhat lekérdezéseket, és hogyan tekintheti meg az ügyféloldali kód példáit [: a Twin gráf lekérdezése](how-to-query-graph.md).