---
title: V3 API lekérdezési karakterlánc paraméterei
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610785"
---
A V3 API lekérdezési karakterlánc paraméterei a következők:

|Lekérdezési paraméter|LUIS-portál neve|Típus|Verzió|Alapértelmezett|Cél|
|--|--|--|--|--|--|
|`log`|Naplók mentése|logikai|V2 & V3|hamis|A lekérdezés tárolása a naplófájlban. Az alapértelmezett érték false (hamis).|
|`query`|-|sztring|Csak v3|Nincs alapértelmezett – a GET kérelemben szükséges|A **v2-ben**az előre jelzett érték a `q` paraméterben szerepel. <br><br>A **v3**-as verzióban a funkció a `query` paraméterben lesz átadva.|
|`show-all-intents`|Az összes cél pontszámának belefoglalása|logikai|Csak v3|hamis|Az összes leképezés visszaküldése a megfelelő pontszámmal a **jóslat. szándékok** objektumban. A rendszer a fölérendelt objektumban lévő objektumokként adja vissza a leképezéseket `intents` . Ez lehetővé teszi a programozott hozzáférést anélkül, hogy meg kellene találni a szándékot egy tömbben: `prediction.intents.give` . A v2-ben ezek egy tömbben voltak visszaadva. |
|`verbose`|További entitások részleteinek belefoglalása|logikai|V2 & V3|hamis|Ha **a v2**értéke TRUE (igaz) értékre van állítva, az összes előre jelzett leképezést visszaadja. Ha minden előre jelzett leképezésre van szüksége, használja a v3 paraméterét `show-all-intents` .<br><br>**A v3-** as verzióban ez a paraméter csak az entitások előrejelzését biztosító entitás-metaadatokat tartalmazza.  |
|`timezoneOffset`|-|sztring|2. verzió|-|A datetimeV2 entitásokra alkalmazott időzóna.|
|`datetimeReference`|-|sztring|V3|-|A datetimeV2 entitásokra alkalmazott [időzóna](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) . `timezoneOffset`A rendszer a v2-ből cseréli le.|