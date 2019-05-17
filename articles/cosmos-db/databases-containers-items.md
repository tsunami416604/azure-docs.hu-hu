---
title: Adatbázisok, tárolók és elemek az Azure Cosmos DB használata
description: Ez a cikk bemutatja, hogyan hozhat létre és használhat az adatbázisok, tárolók és elemek az Azure Cosmos DB-ben.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: ea3ba91859bbfb1a7c589cdb36e9fb87b52a89b8
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560289"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Adatbázisok, tárolók és elemek az Azure Cosmos DB használata

Miután létrehozott egy [Azure Cosmos DB-fiók](account-overview.md) az Azure-előfizetéshez adatai is kezelhetők a fiókban lévő adatbázisok, tárolók és elemek létrehozásával. Ez a cikk ismerteti ezeket az entitásokat. 

Az alábbi képen látható a különböző entitások hierarchiája az Azure Cosmos DB-fiók:

![Azure Cosmos-fiók entitások](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Az Azure Cosmos Database.

A fiók egy vagy több Azure-Cosmos-adatbázist hozhat létre. Egy adatbázis nevű névtér. Egy adatbázis több Azure-Cosmos-tárolók egysége. Az alábbi táblázat bemutatja, hogyan le van képezve egy Azure Cosmos database különböző API-specifikus entitások:

| Azure Cosmos entity | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- |
|Az Azure Cosmos-adatbázis | Adatbázis | Kulcstér | Adatbázis | Adatbázis | n/a |

> [!NOTE]
> A Table API-fiókok esetében az első tábla létrehozásakor egy alapértelmezett adatbázis automatikusan jön létre az Azure Cosmos-fiókban.

### <a name="operations-on-an-azure-cosmos-database"></a>Egy Azure Cosmos database műveletek

Használhatja az Azure Cosmos API-kkal egy Azure Cosmos database az alábbi táblázatban leírtak szerint:

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- |
|Minden adatbázis számbavétele| Igen | Igen | Igen (adatbázis le van képezve egy kulcstér) | Igen | NA | NA |
|Adatbázis olvasása| Igen | Igen | Igen (adatbázis le van képezve egy kulcstér) | Igen | NA | NA |
|Új adatbázis létrehozása| Igen | Igen | Igen (adatbázis le van képezve egy kulcstér) | Igen | NA | NA |
|Adatbázis frissítése| Igen | Igen | Igen (adatbázis le van képezve egy kulcstér) | Igen | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-tárolók

Az Azure Cosmos-tárolókat a méretezhetőséget, mind a kiosztott átviteli sebesség és a tárolási egység. Egy tároló vízszintesen particionált, és több régióra replikált majd. Adja hozzá a tároló és az átviteli sebesség, a rajta üzembe helyezi az elemek automatikusan a partíciókulcs alapján logikai partíciók egy halmazában vannak elosztva. Particionálás és a partíciókulcsok kapcsolatos további információkért lásd: [adatokat](partition-data.md). 

Amikor létrehoz egy Azure Cosmos-tárolóhoz, átviteli sebesség konfigurálja a következő módok egyikében:

* **Dedikált kiosztott átviteli mód**: Egy tároló kiosztott átviteli kizárólag az adott tárolók számára fenntartott és a szavatolják. További tudnivalókért lásd: [hogyan helyezhet üzembe egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).

* **Megosztott kiosztott átviteli mód**: Ezek a tárolók a kiosztott átviteli sebesség megoszthatja a más tárolók ugyanabban az adatbázisban (kivéve a tárolók, amelyek dedikált kiosztott átviteli sebesség lettek konfigurálva). Más szóval a kiosztott átviteli sebességét az adatbázis összes "megosztott átviteli" tároló között megosztott. További tudnivalókért lásd: [kioszthatja az átviteli sebességet egy Azure Cosmos database hogyan](how-to-provision-database-throughput.md).

Az Azure Cosmos-tárolókat rugalmasan, méretezhető e tárolók dedikált vagy megosztott kiosztott átviteli mód használatával hoz létre.

Az Azure Cosmos-tárolókat az elemek sémafüggetlen tárolója. Tárolóban lévő elemek is tetszőleges sémákkal rendelkeznek. Például egy személyt jelölő elem és a egy autó jelölő elem nem helyezhető a *ugyanabban a tárolóban*. Alapértelmezés szerint adja hozzá a tároló az összes elem automatikusan indexelt explicit index vagy séma kezelése nélkül. Az indexelés viselkedésének konfigurálásával testre szabhatja a [indexelési szabályzat](index-overview.md) egy tárolón. 

Beállíthat [élettartamot (TTL)](time-to-live.md) a kiválasztott cikkek egy Azure Cosmos-tárolóba vagy a teljes tárolót szabályosan kiürítése azok az elemek a rendszerből. Az Azure Cosmos DB automatikusan törli a cikkeket a lejárat után. Emellett biztosítja azt, hogy a tárolón végrehajtott lekérdezés nem ad vissza a lejárt elemek egy rögzített kötött belül. További tudnivalókért lásd: [élettartam konfigurálása a tároló](how-to-time-to-live.md).

Használhat [módosításcsatornáját](change-feed.md) előfizetni a műveleti napló az egyes logikai partíciók a tároló felügyelt. Módosítási hírcsatorna biztosít a napló az összes tartományvezérlőn végrehajtott frissítések összevonása a tárolót, valamint a előtti és utáni képek a cikkek. További információkért lásd: [használatával hozhat létre olyan reaktív alkalmazásokat módosításcsatornáját](serverless-computing-database.md). Beállíthatja, hogy a módosítás adatcsatorna használatával a változáscsatorna házirendet a tárolón a megőrzési időtartama. 

Regisztrálhatja [tárolt eljárások, eseményindítók, felhasználó által definiált függvények (UDF)](stored-procedures-triggers-udfs.md), és [eljárások egyesítése](how-to-manage-conflicts.md) az Azure Cosmos-tárolóhoz. 

Megadhat egy [egyedi kulcsmegkötés](unique-keys.md) az Azure Cosmos-tárolón. Hozzon létre egyedi, a logikai partíciókulcs egy vagy több értéket, a egyediségének biztosítása. Ha egy tárolót hoz létre az egyedi, új vagy frissített elemek által az egyedi kulcsmegkötés megadott értékek ismétlődő értékekkel hozható létre. További tudnivalókért lásd: [egyedi kulcsra vonatkozó megkötések](unique-keys.md).

Az Azure Cosmos-tárolókat az API-specifikus entitásokkal kifejezetten az alábbi táblázatban látható módon:

| Azure Cosmos entity | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- |
|Az Azure Cosmos-tároló | Gyűjtemény | Tábla | Gyűjtemény | Graph | Tábla |

### <a name="properties-of-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló tulajdonságai

Egy Azure Cosmos-tárolóhoz egy rendszer által definiált tulajdonsággal rendelkezik. Attól függően, melyik API-t használja egyes tulajdonságok esetleg nem kell el közvetlenül. A következő táblázat ismerteti a rendszer által definiált tulajdonságok listája:

| Rendszer által meghatározott tulajdonság | A rendszer vagy felhasználó által konfigurálható | Cél | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_Azonosító | System-generated | Tároló egyedi azonosítója | Igen | Nem | Nem | Nem | Nem |
|\_Az ETag | System-generated | Az optimista egyidejűség-vezérlési entitáscímkéje | Igen | Nem | Nem | Nem | Nem |
|\_TS | System-generated | A tároló utolsó frissítés időbélyege | Igen | Nem | Nem | Nem | Nem |
|\_self | System-generated | A tároló címezhető URI | Igen | Nem | Nem | Nem | Nem |
|azonosító | User-configurable | Felhasználó által megadott egyedi a tároló nevét. | Igen | Igen | Igen | Igen | Igen |
|indexingPolicy | User-configurable | Lehetővé teszi az index elérési útja, index típusa és index mód módosítása | Igen | Nem | Nem | Nem | Igen |
|TimeToLive | User-configurable | Lehetővé teszi elemek után automatikusan törölje a tároló egy időkorlát. További információkért lásd: [élettartama](time-to-live.md). | Igen | Nem | Nem | Nem | Igen |
|changeFeedPolicy | User-configurable | Olvassa el a tárolóban lévő elemek segítségével. További információkért lásd: [Módosításcsatornáját](change-feed.md). | Igen | Nem | Nem | Nem | Igen |
|uniqueKeyPolicy | User-configurable | Használja az egy vagy több értéket egy logikai partíció egyediségének biztosítása érdekében. További információkért lásd: [egyedi kulcsra vonatkozó megkötések](unique-keys.md). | Igen | Nem | Nem | Nem | Igen |

### <a name="operations-on-an-azure-cosmos-container"></a>Az Azure Cosmos-tárolókat műveletek

Az Azure Cosmos-tárolókat a következő műveleteket támogatja, az Azure Cosmos API-k használatakor:

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- |
| A tárolók egy adatbázis számbavétele | Igen | Igen | Igen | Igen | NA | NA |
| Egy tároló olvasása | Igen | Igen | Igen | Igen | NA | n/a |
| Új tároló létrehozása | Igen | Igen | Igen | Igen | NA | n/a |
| Egy tároló frissítése | Igen | Igen | Igen | Igen | NA | n/a |
| Tároló törlése | Igen | Igen | Igen | Igen | NA | NA |

## <a name="azure-cosmos-items"></a>Az Azure Cosmos-elemek

Attól függően, melyik API-t használ egy Azure Cosmos-elemet egy gyűjtemény, tábla, vagy egy csomópontot egy sort egy dokumentumot vagy edge grafikon hozhat létre. Az alábbi táblázat bemutatja egy Azure Cosmos-elemet az API-specifikus entitások leképezése:

| Cosmos-entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- |
|Az Azure Cosmos-elem | Dokumentum | Sor | Dokumentum | Csomópont- vagy peremtábla | Elem |

### <a name="properties-of-an-item"></a>Egy elem tulajdonságai

Minden Azure-Cosmos elemnek a következő, rendszer által definiált tulajdonságok. Attól függően, melyik API-t használ néhányat, előfordulhat, hogy nem kell közvetlenül elérhető.

| Rendszer által meghatározott tulajdonság | A rendszer vagy felhasználó által konfigurálható| Cél | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_Azonosító | System-generated | Az elem egyedi azonosítója | Igen | Nem | Nem | Nem | Nem |
|\_Az ETag | System-generated | Az optimista egyidejűség-vezérlési entitáscímkéje | Igen | Nem | Nem | Nem | Nem |
|\_TS | System-generated | A cikk az utolsó frissítés időbélyege | Igen | Nem | Nem | Nem | Nem |
|\_self | System-generated | Az elemek címezhető URI azonosítója | Igen | Nem | Nem | Nem | Nem |
|id | Vagy | Felhasználó által megadott egyedi nevek a logikai partíció. Ha a felhasználó nem ad meg az azonosító, a rendszer automatikusan létrehoz egy. | Igen | Igen | Igen | Igen | Igen |
|Tetszőleges, felhasználó által definiált tulajdonságai | Felhasználó által meghatározott | API natív ábrázolás (beleértve a JSON, BSON és CQL) jelölt felhasználó által definiált tulajdonságai | Igen | Igen | Igen | Igen | Igen |

### <a name="operations-on-items"></a>Műveleti elemek

Az Azure Cosmos-elemek a következő műveleteket támogatja. Az Azure Cosmos API-k bármelyikét használhatja a műveletek végrehajtásához.

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- |
| Helyezze be, cserélje le, törlése, Upsert, olvassa el | Nem | Igen | Igen | Igen | Igen | Igen |

## <a name="next-steps"></a>További lépések

További információ ezekről a feladatokról és fogalmak:

* [Egy Azure Cosmos-adatbázis kiépítése átviteli sebességet](how-to-provision-database-throughput.md)
* [Egy Azure Cosmos-tároló átviteli sebességet kiépítése](how-to-provision-container-throughput.md)
* [Logikai partíciókkal rendelkező működik](partition-data.md)
* [Egy Azure Cosmos-tárolót az élettartam konfigurálása](how-to-time-to-live.md)
* [Reaktív jellegű alkalmazások készítését használatával – csatorna módosítása](change-feed.md)
* [Az Azure Cosmos-tároló egy egyedi kulcsmegkötés konfigurálása](unique-keys.md)
