---
author: baanders
description: fájl belefoglalása az Azure Digital Twins lekérdezési műveleteihez
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 333a7ec4ae0e5c8cbc94a603e2ccf81ee92e7d48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078459"
---
## <a name="query-language-features"></a>Nyelvi funkciók lekérdezése

Az Azure Digital Twins kiterjedt lekérdezési képességeket biztosít a Twin gráfhoz képest. A lekérdezések SQL-szerű szintaxissal vannak leírva, hasonlóan a lekérdezési nyelvhez, mint a számos hasonló funkcióval rendelkező [IoT hub lekérdezési nyelv](../articles/iot-hub/iot-hub-devguide-query-language.md) .

> [!NOTE]
> Az összes Azure digitális Twins lekérdezési művelet megkülönbözteti a kis-és nagybetűket.

Itt láthatja az Azure Digital Twins lekérdezési nyelvén elérhető műveleteket.

Digitális ikrek beolvasása...
* modell ( `IS_OF_MODEL` operátor használatával)
* tulajdonságok (a [címke tulajdonságaival](../articles/digital-twins/how-to-use-tags.md)együtt)
* adapterek
* kapcsolatok
  - a kapcsolatok tulajdonságai

A lekérdezéseket tovább növelheti a következő műveletekkel:
* Az ikrek több kapcsolati típust ( `JOIN` lekérdezéseket) kapnak. 
  - Az előzetes verzióban legfeljebb öt szint `JOIN` adható meg.
* Csak a legfelső szintű lekérdezés eredményeinek kiválasztása ( `Select TOP` operátor)
* Egy eredményhalmaz elemeinek darabszáma a következő használatával: `Select COUNT`
* Kivetítések használata a lekérdezés által visszaadott oszlopok kiválasztásához
* Skaláris függvények használata:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Lekérdezés-összehasonlító operátorok használata:,,, `IN` / `NIN` `=` `!=` `<` , `>` , `<=` , `>=` .
* Tetszőleges kombinációt ( `AND` , `OR` `NOT` operátort) `IS_OF_MODEL` , skaláris függvényeket és összehasonlító operátorokat használhat.
* A folytatás használata: a lekérdezési objektum egy Oldalméret (legfeljebb 100) használatával hozható létre. A digitális ikrek egy oldalát egyszerre is lekérheti, ha a folytatási tokent az API-ra irányuló további hívásokban is megadja.