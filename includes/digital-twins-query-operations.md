---
author: baanders
description: fájl belefoglalása az Azure Digital Twins lekérdezési műveleteihez
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 70ff1847548c1328a709cf17c02bba3dd25ba213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486653"
---
## <a name="query-language-features"></a>Nyelvi funkciók lekérdezése

Az Azure Digital Twins kiterjedt lekérdezési képességeket biztosít a Twin gráfhoz képest. A lekérdezések SQL-szerű szintaxissal vannak leírva, hasonlóan a lekérdezési nyelvhez, mint a számos hasonló funkcióval rendelkező [IoT hub lekérdezési nyelv](../articles/iot-hub/iot-hub-devguide-query-language.md) .

> [!NOTE]
> Az összes Azure digitális Twins lekérdezési művelet megkülönbözteti a kis-és nagybetűket.

Itt láthatja az Azure Digital Twins Query Store nyelvén elérhető műveleteket.

Digitális ikrek beolvasása...
* modell ( `IS_OF_MODEL` operátor használatával)
* tulajdonságok (a [címke tulajdonságaival](../articles/digital-twins/how-to-use-tags.md)együtt)
* adapterek
* kapcsolatok
  - a kapcsolatok tulajdonságai

A lekérdezéseket tovább növelheti a következő műveletekkel:
* Az ikrek több kapcsolati típust ( `JOIN` lekérdezéseket) kapnak. 
  - Az s megengedett számának korlátozásai érvényesek `JOIN` (egy szint a nyilvános előzetes verzió esetében).
* Csak a legfelső szintű lekérdezés eredményeinek kiválasztása ( `Select TOP` operátor)
* Skaláris függvények használata:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Lekérdezés-összehasonlító operátorok használata:,,, `IN` / `NIN` `=` `!=` `<` , `>` , `<=` , `>=` .
* Tetszőleges kombinációt ( `AND` , `OR` `NOT` operátort) `IS_OF_MODEL` , skaláris függvényeket és összehasonlító operátorokat használhat.
* A folytatás használata: a lekérdezési objektum egy Oldalméret (legfeljebb 100) használatával hozható létre. A digitális ikrek egy oldalát egyszerre is lekérheti, ha a folytatási tokent az API-ra irányuló további hívásokban is megadja.