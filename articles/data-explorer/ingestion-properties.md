---
title: Az Azure Data Explorer adatbetöltési tulajdonságai
description: Ismerje meg az Azure Data Explorer által támogatott különböző adatbetöltési tulajdonságokat.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109575"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Az Azure Data Explorer adatbetöltési tulajdonságai 

Az adatok betöltése az a folyamat, amelynek során az adatok hozzáadódnak egy táblához, és elérhetővé válik a lekérdezés az Azure Data Explorerben. A kulcsszó után tulajdonságokat ad `with` hozzá a betöltési parancshoz.

## <a name="ingestion-properties"></a>Betöltési tulajdonságok

Az alábbi táblázat felsorolja az Azure Data Explorer által támogatott tulajdonságokat, ismerteti azokat, és példákat tartalmaz: 

|Tulajdonság              |Leírás                                              |Példa                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Karakterlánc-érték, amely azt jelzi, hogyan lehet adatokat képezni a forrásfájlból a tábla tényleges oszlopaihoz. Adja `format` meg az értéket a megfelelő leképezési típussal. Lásd: [adatleképezések](/azure/kusto/management/mappings).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(elavult: `avroMapping`, , `csvMapping` `jsonMapping`) |
|`ingestionMappingReference`|Olyan karakterláncérték, amely azt jelzi, hogyan lehet adatokat képezni a forrásfájlból a tábla tényleges oszlopaihoz egy elnevezett leképezési házirend-objektum használatával. Adja `format` meg az értéket a megfelelő leképezési típussal. Lásd: [adatleképezések](/azure/kusto/management/mappings).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(elavult: `avroMappingReference`, , `csvMappingReference` `jsonMappingReference`)|
|`creationTime` |A datetime érték (ISO8601 karakterláncként formázva) a bevitt adatok terjedelemének létrehozásakor használva. Ha nincs megadva,`now()`a program az aktuális értéket ( ) fogja használni. Az alapértelmezett felülbírálása akkor hasznos, ha régebbi adatokat, így az adatmegőrzési szabály megfelelően lesz alkalmazva.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|Logikai érték, amely ha meg van adva, arra utasítja a parancsot, `false`hogy bővítse ki a tábla sémáját (alapértelmezés szerint). Ez a beállítás `.append` `.set-or-append` csak a parancsokra és parancsokra vonatkozik. Az egyetlen engedélyezett sémabővítmények végén további oszlopokat adnak hozzá a táblához.|Ha az eredeti táblaséma a `(a:string, b:int)`visszaállítása, `(a:string, b:int, c:datetime, d:string)`akkor `(a:string, c:datetime)` érvényes sémakiterjesztés lenne, de nem lenne érvényes.|
|`folder` |A [lekérdezésből betöltési](/azure/kusto/management/data-ingestion/ingest-from-query) parancsokhoz a táblához rendelendő mappa. Ha a tábla már létezik, ez a tulajdonság felülírja a tábla mappáját.|`with (folder="Tables/Temporary")`|
|`format` |Az adatformátum (lásd a [támogatott adatformátumokat](ingestion-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|Olyan karakterlánc-érték, amely ha meg van adva, megakadályozza a betöltést, `ingest-by:` ha a tábla már rendelkezik azonos értékű címkével ellátott adatokkal. Ez biztosítja az idempotens adatok betöltését. További információ: [Betöltési: címkék](/azure/kusto/management/extents-overview#ingest-by-extent-tags).|A `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` tulajdonságok azt jelzik, `ingest-by:Part0001` hogy ha a címke adatait már létezik, akkor ne fejezze be az aktuális betöltést. Ha még nem létezik, az új betöltéshez be kell állítani ezt a címkét (abban az esetben, ha egy jövőbeli betöltés ily módon ismét megkísérli ugyanazokat az adatokat beadni.)|
|`ignoreFirstRecord` |Logikai érték, amely `true`, ha értéke , azt jelzi, hogy a betöltésnek figyelmen kívül kell hagynia minden fájl első rekordját. Ez a tulajdonság akkor `CSV`hasznos, ha a fájl első rekordja az oszlopnevek. Alapértelmezés szerint `false` a program feltételezi.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Logikai érték, amely ha meg van adva, azt jelzi, hogy a parancsnak meg kell persistnie a részletes eredményeket (még akkor is, ha sikeres), hogy a [.show művelet részletei](/azure/kusto/management/operations#show-operation-details) parancs beolvashassa őket. Alapértelmezés szerint `false`a.|`with (persistDetails=true)`|
|`policy_ingestiontime`|Logikai érték, amely ha meg van adva, azt írja le, hogy engedélyezi-e a [betöltési időházirendet](/azure/kusto/management/ingestiontimepolicy) egy olyan táblában, amelyet ez a parancs hozott létre. A mező alapértelmezett értéke: `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Logikai érték, amely ha meg van adva, azt írja le, hogy a parancs újra létrehozhatja-e a tábla sémáját. Ez a tulajdonság `.set-or-replace` csak a parancsra vonatkozik. Ez a tulajdonság elsőbbséget `extend_schema` élvez a tulajdonsággal szemben, ha mindkettő be van állítva.|`with (recreate_schema=true)`|
|`tags`|A bevitt adatokhoz [társítható,](/azure/kusto/management/extents-overview#extent-tagging) JSON-karakterláncként formázott címkék listája |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|JSON-karakterlánc, amely azt jelzi, hogy mely érvényesítéseket kell futtatni a betöltés során. A különböző lehetőségek magyarázatát az [Adatok betöltése](/azure/kusto/management/data-ingestion/) című témakörben található.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(ez valójában az alapértelmezett házirend)|
|`zipPattern`|Ezt a tulajdonságot zip-archívumot tartalmazó tárolóból történő adatbetöltésekhez használja. Ez egy karakterlánc-érték, amely a zip-archívumban a betöltéshez használt fájlok kiválasztásához használandó reguláris kifejezést jelzi.  Az archívumban lévő összes többi fájlt a rendszer figyelmen kívül hagyja.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>További lépések

* További információ az [adatok betöltéséről](/azure/data-explorer/ingest-data-overview)
* További információ a [támogatott adatformátumokról](ingestion-supported-formats.md)
