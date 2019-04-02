---
title: Azure Cosmos DB-adatbázisok, tárolók és elemek használata
description: Ez a cikk bemutatja, hogyan hozhat létre és használhat az Azure Cosmos DB-adatbázisok, tárolók és elemek
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: f3bec1b279c07e62e246ebfa933b3942e38406de
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762905"
---
# <a name="work-with-databases-containers-and-items"></a>Adatbázisok, tárolók és elemek használata

Miután létrehozott egy [Azure Cosmos-fiók](account-overview.md) az Azure-előfizetéshez adatai is kezelhetők a fiókban lévő adatbázisok, tárolók és elemek létrehozásával. Ez a cikk ismerteti ezeket az entitásokat: adatbázisok, tárolók és elemek. Az alábbi képen látható a különböző entitások hierarchiája egy Azure Cosmos-fiók:

![Azure Cosmos-fiók entitások](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Az Azure Cosmos Database.

A fiók alatt létrehozhat egy vagy több Azure-Cosmos-adatbázis. Egy adatbázis nevű névtér. Több Azure-Cosmos-tárolók egysége. Az alábbi táblázat bemutatja, hogyan le van képezve egy Azure Cosmos database különböző API-specifikus entitások:

| **Azure Cosmos-entitás** | **SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API** | **Tábla API** |
| --- | --- | --- | --- | --- | --- |
|Az Azure Cosmos-adatbázis | Adatbázis | Kulcstér | Adatbázis | Adatbázis | NA |

> [!NOTE]
> A Table API-fiókok esetében az első tábla létrehozásakor egy alapértelmezett adatbázis automatikusan jön létre az Azure Cosmos-fiókon belül.

### <a name="operations-on-an-azure-cosmos-database"></a>Egy Azure Cosmos database műveletek

Kezelheti egy Azure Cosmos database az Azure-Cosmos-API-kkal a következőképpen:

| **Művelet** | **Azure CLI**|**SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API** | **Tábla API** |
| --- | --- | --- | --- | --- | --- | --- |
|Minden adatbázis számbavétele| Igen | Igen | Igen (adatbázis le van képezve egy kulcstér) | Igen | NA | NA |
|Adatbázis olvasása| Igen | Igen | Igen (adatbázis le van képezve egy kulcstér) | Igen | NA | NA |
|Új adatbázis létrehozása| Igen | Igen | Igen (adatbázis le van képezve egy kulcstér) | Igen | NA | NA |
|Adatbázis frissítése| Igen | Igen | Igen (adatbázis le van képezve egy kulcstér) | Igen | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-tárolók

Az Azure Cosmos-tárolókat méretezhetőséget biztosít a kiosztott átviteli sebesség és a tárolási egység. Egy tároló vízszintesen particionált, és több régióra replikált majd. Adja hozzá a tároló és az átviteli sebesség, a rajta üzembe helyezi az elemek automatikusan a partíciókulcs alapján logikai partíciók egy halmazában vannak elosztva. Particionálás és a partíciókulcsok kapcsolatos további információkért lásd: [ez](partition-data.md) cikk. 

Egy Azure Cosmos-tároló létrehozásakor átviteli konfigurálja a következő módok egyikében:

* **Dedikált kiosztott átviteli sebesség** mód: Egy tároló kiosztott átviteli kizárólag az adott tárolók számára fenntartott és a szavatolják. További tudnivalókért lásd: [hogyan helyezhet üzembe egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).

* **Megosztott kiosztott átviteli sebesség** mód: Ezek a tárolók a kiosztott átviteli sebesség megoszthatja a más tárolók ugyanabban az adatbázisban (kivéve azokat, amelyek dedikált kiosztott átviteli sebesség a konfigurált tárolók). Más szóval a kiosztott átviteli sebességét az adatbázis összes "megosztott átviteli" tároló között megosztott. További tudnivalókért lásd: [kiosztott átviteli sebesség konfigurálása az Azure Cosmos database](how-to-provision-database-throughput.md).

Az Azure Cosmos-tárolókat rugalmasan méretezhető, hogy hoz létre a tárolót vagy a "megosztás" vagy "dedikált" kiosztott átviteli mód.

Az Azure Cosmos-tárolókat az elemek sémafüggetlen tárolója. Tárolóban lévő elemek is tetszőleges sémákkal rendelkeznek. Például egy cikk egy személyt jelölő, egy autó jelölő elem nem helyezhető a *ugyanabban a tárolóban*. Alapértelmezés szerint adja hozzá a tároló az összes elem automatikus indexelése semmilyen kifejezett index vagy séma kezelése nélkül. Az indexelés viselkedésének konfigurálásával testre szabhatja a [indexelési szabályzat](index-overview.md) egy tárolón. 

Beállíthat [élettartam (TTL)](time-to-live.md) a kijelölt elemek egy Azure Cosmos-tárolón belül vagy a teljes tárolót szabályosan kiürítése azok az elemek a rendszerből. Az Azure Cosmos DB automatikusan törli a cikkek járnak. Emellett biztosítja azt, hogy a tárolón végrehajtott lekérdezés nem ad vissza a lejárt elemek egy rögzített kötött belül. További tudnivalókért lásd: [élettartam konfigurálása a tároló](how-to-time-to-live.md).

Használatával [módosítási hírcsatorna](change-feed.md), előfizethet a műveleti napló minden a tároló logikai partíció felügyelt. A módosítási hírcsatorna biztosít az összes tartományvezérlőn végrehajtott frissítések összevonása a tároló és a napló a előtti és utáni képek elem. Lásd: [hogyan hozhat létre alkalmazásokat reaktív módosítási hírcsatorna](serverless-computing-database.md). Beállíthatja a megőrzési időtartam a hírcsatorna módosítsa a házirendet a tárolón a változáscsatorna használatával. 

Regisztrálhatja [tárolt eljárások, eseményindítók, felhasználó által definiált függvények (UDF)](stored-procedures-triggers-udfs.md) és [eljárások egyesítése](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy-with-a-stored-procedure) az Azure Cosmos-tárolóval. 

Megadhat egy [egyedi kulcsmegkötés](unique-keys.md) az Azure Cosmos-tárolón. Hozzon létre egyedi, a logikai partíciókulcs egy vagy több értéket, a egyediségének biztosítása. Miután a tároló egyedi létre lett hozva, nem kell létrehozni az új vagy frissített elemek által az egyedi kulcsmegkötés megadott értékek ismétlődő értékekkel. További tudnivalókért lásd: [egyedi kulcsra vonatkozó megkötések](unique-keys.md).

Az Azure Cosmos-tárolókat a következő kifejezetten az API-specifikus entitásokkal:

| **Azure Cosmos-entitás** | **SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API** | **Tábla API** |
| --- | --- | --- | --- | --- | --- |
|Az Azure Cosmos-tároló | Gyűjtemény | Tábla | Gyűjtemény | Graph | Tábla |

### <a name="properties-of-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló tulajdonságai

Egy Azure Cosmos-tárolóhoz egy rendszer által definiált tulajdonsággal rendelkezik. A kiválasztott API-t, attól függően némelyike nem lehet közvetlenül téve. A következő táblázat ismerteti a rendszer által definiált tulajdonságok listája:

| **Rendszer által meghatározott tulajdonság** | **Létrehozott vagy felhasználó által konfigurálható** | **Cél** | **SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API** | **Tábla API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | A rendszer által létrehozott | Tároló egyedi azonosítója | Igen | Nem | Nem | Nem | Nem |
|_etag | A rendszer által létrehozott | Az optimista egyidejűség-vezérlési entitáscímkéje | Igen | Nem | Nem | Nem | Nem |
|_ts | A rendszer által létrehozott | A tároló utolsó frissítés időbélyege | Igen | Nem | Nem | Nem | Nem |
|_self | A rendszer által létrehozott | A tároló címezhető URI | Igen | Nem | Nem | Nem | Nem |
|id | Felhasználó által konfigurálható | Felhasználó által megadott egyedi a tároló nevét. | Igen | Igen | Igen | Igen | Igen |
|indexingPolicy | Felhasználó által konfigurálható | Lehetővé teszi az index elérési útja, index típusa és index mód módosítása. | Igen | Nem | Nem | Nem | Igen |
|TimeToLive | Felhasználó által konfigurálható | Lehetővé teszi elemek törlése automatikusan végre egy bizonyos idő elteltével. További részletekért tekintse meg a [Time To Live](time-to-live.md) cikk. | Igen | Nem | Nem | Nem | Igen |
|changeFeedPolicy | Felhasználó által konfigurálható | Olvassa el a tárolóban lévő elemek segítségével. További részletekért tekintse meg a [módosítási hírcsatorna](change-feed.md) cikk. | Igen | Nem | Nem | Nem | Igen |
|uniqueKeyPolicy | Felhasználó által konfigurálható | Használja a logikai partíción belül egy vagy több értéket egyediségének biztosítása érdekében. További információkért lásd: a [egyedi kulcs megkötéseket](unique-keys.md) cikk. | Igen | Nem | Nem | Nem | Igen |

### <a name="operations-on-an-azure-cosmos-container"></a>Az Azure Cosmos-tárolókat műveletek

Az Azure Cosmos-tárolókat a következő műveletek bármelyikével az Azure Cosmos API-kat támogatja.

| **Művelet** | **Azure CLI** | **SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API** | **Tábla API** |
| --- | --- | --- | --- | --- | --- | --- |
| A tárolók egy adatbázis számbavétele | Igen | Igen | Igen | Igen | NA | NA |
| Egy tároló olvasása | Igen | Igen | Igen | Igen | NA | NA |
| Új tároló létrehozása | Igen | Igen | Igen | Igen | NA | NA |
| Tároló frissítése | Igen | Igen | Igen | Igen | NA | NA |
| Tároló törlése | Igen | Igen | Igen | Igen | NA | NA |

## <a name="azure-cosmos-items"></a>Az Azure Cosmos-elemek

Az API-t a választás, attól függően egy Azure Cosmos-elemet vagy egy dokumentumot egy gyűjtemény egy sort a táblában vagy egy csomópont/edge grafikon hozhat létre. Az alábbi táblázat bemutatja egy Azure Cosmos-elemet az API-specifikus entitások közötti leképezést:

| **Cosmos-entitás** | **SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API** | **Tábla API** |
| --- | --- | --- | --- | --- | --- |
|Az Azure Cosmos-elem | Dokumentum | Sor | Dokumentum | Csomópont- vagy Peremtábla | Elem |

### <a name="properties-of-an-item"></a>Egy elem tulajdonságai

Minden Azure-Cosmos-elem a következő definiált rendszertulajdonsággal rendelkezik. A kiválasztott API-t, attól függően némelyike nem lehet közvetlenül téve.

|**Rendszer által meghatározott tulajdonság** | **Létrehozott vagy felhasználó által konfigurálható**| **Cél** | **SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API** | **Tábla API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | A rendszer által létrehozott | Elem egyedi azonosítója | Igen | Nem | Nem | Nem | Nem |
|_etag | A rendszer által létrehozott | Az optimista egyidejűség-vezérlési entitáscímkéje | Igen | Nem | Nem | Nem | Nem |
|_ts | A rendszer által létrehozott | A cikk az utolsó frissítés történő küldés időbélyegzője | Igen | Nem | Nem | Nem | Nem |
|_self | A rendszer által létrehozott | Az elemek címezhető URI azonosítója | Igen | Nem | Nem | Nem | Nem |
|id | Vagy | Felhasználó által definiált belül egyedi nevet a logikai partíció. Ha a felhasználó nem ad meg az azonosító, a rendszer automatikusan létrehoz egyet. | Igen | Igen | Igen | Igen | Igen |
|Tetszőleges, felhasználó által definiált tulajdonságai | Felhasználó által megadott | Felhasználó által definiált tulajdonságok jelölt API natív ábrázolás (JSON, BSON, CQL, stb.) | Igen | Igen | Igen | Igen | Igen |

### <a name="operations-on-items"></a>Műveleti elemek

Az Azure Cosmos-cikk támogatja a következő műveleteket az Azure Cosmos API-k használatával elvégezhető.

| **Művelet** | **Azure CLI** | **SQL API-HOZ** | **Cassandra API** | **Az Azure Cosmos DB MongoDB API-jaival** | **Gremlin API** | **Tábla API** |
| --- | --- | --- | --- | --- | --- | --- |
| Helyezze be, cserélje le, törlése, Upsert, olvassa el | Nem | Igen | Igen | Igen | Igen | Igen |

## <a name="next-steps"></a>További lépések

Folytassa a következő fogalmak további:

* [Egy Azure Cosmos database kiosztott átviteli sebesség konfigurálása](how-to-provision-database-throughput.md)
* [Egy Azure Cosmos-tároló kiosztott átviteli sebesség konfigurálása](how-to-provision-container-throughput.md)
* [Logikai partíció](partition-data.md)
* [Élettartam konfigurálása az Azure Cosmos-tároló](how-to-time-to-live.md)
* [Hogyan hozhat létre alkalmazásokat reaktív módosítási hírcsatorna](change-feed.md)
* [Az Azure Cosmos-tároló egyedi kulcsmegkötés konfigurálása](unique-keys.md)
