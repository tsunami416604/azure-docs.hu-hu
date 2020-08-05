---
author: baanders
description: fájl belefoglalása az Azure Digital Twins lekérdezési műveleteihez
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 82639a19ef728c22a74381d24754992e0f647976
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562470"
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
  - Az s megengedett számának korlátozásai érvényesek `JOIN` (egy szint a nyilvános előzetes verzió esetében).
* Csak a legfelső szintű lekérdezés eredményeinek kiválasztása ( `Select TOP` operátor)
* Skaláris függvények használata:,,,,,,, `IS_BOOL` `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Lekérdezés-összehasonlító operátorok használata:,,, `IN` / `NIN` `=` `!=` `<` , `>` , `<=` , `>=` .
* Tetszőleges kombinációt ( `AND` , `OR` `NOT` operátort) `IS_OF_MODEL` , skaláris függvényeket és összehasonlító operátorokat használhat.
* A folytatás használata: a lekérdezési objektum egy Oldalméret (legfeljebb 100) használatával hozható létre. A digitális ikrek egy oldalát egyszerre is lekérheti, ha a folytatási tokent az API-ra irányuló további hívásokban is megadja.