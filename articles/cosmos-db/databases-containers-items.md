---
title: Adatbázisok, tárolók és elemek használata Azure Cosmos DBban
description: Ez a cikk azt ismerteti, hogyan hozhatók létre és használhatók adatbázisok, tárolók és elemek a Azure Cosmos DBban.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.reviewer: sngun
ms.openlocfilehash: e1718ac9a7b7fcaab096595ea7341fcc90c2ddd6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422334"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Adatbázisok, tárolók és elemek használata Azure Cosmos DBban

Miután létrehozta az Azure-előfizetéshez tartozó [Azure Cosmos db fiókot](account-overview.md) , a fiókban lévő adatokat adatbázisok, tárolók és elemek létrehozásával kezelheti. Ez a cikk ezeket az entitásokat ismerteti. 

Az alábbi képen egy Azure Cosmos DB fiók különböző entitásait ábrázoló hierarchia látható:

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos-fiókok entitásai" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos-adatbázisok

A fiókjában létrehozhat egy vagy több Azure Cosmos-adatbázist is. Az adatbázisok egy névtérhez hasonlítanak. Az adatbázis az Azure Cosmos-tárolók egy készletének felügyeleti egysége. Az alábbi táblázat bemutatja, hogyan van leképezve egy Azure Cosmos-adatbázis a különböző API-specifikus entitásokra:

| Azure Cosmos-entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-adatbázis | Adatbázis | Kulcstartomány | Adatbázis | Adatbázis | NA |

> [!NOTE]
> Az első táblázat létrehozásakor a Table API fiókok esetében automatikusan létrejön egy alapértelmezett adatbázis az Azure Cosmos-fiókban.

### <a name="operations-on-an-azure-cosmos-database"></a>Műveletek egy Azure Cosmos-adatbázisban

Az Azure Cosmos API-kkal az alábbi táblázatban leírtak szerint dolgozhat:

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- |
|Az összes adatbázis enumerálása| Igen | Igen | Igen (az adatbázis egy üres területre van leképezve) | Yes | NA | NA |
|Adatbázis beolvasása| Igen | Igen | Igen (az adatbázis egy üres területre van leképezve) | Yes | NA | NA |
|Új adatbázis létrehozása| Igen | Igen | Igen (az adatbázis egy üres területre van leképezve) | Yes | NA | NA |
|Adatbázis frissítése| Igen | Igen | Igen (az adatbázis egy üres területre van leképezve) | Yes | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-tárolók

Az Azure Cosmos-tároló a skálázhatósági egység, amely a kiépített átviteli sebességre és a tárterületre is alkalmas. A tárolók horizontálisan particionálva vannak, majd több régióban replikálódnak. A tárolóhoz hozzáadott elemek és a rajta kiépített átviteli sebesség a partíciós kulcs alapján automatikusan oszlik meg a logikai partíciók készletében. A particionálással és a partíciós kulcsokkal kapcsolatos további tudnivalókért tekintse meg az [Adatparticionálást](partition-data.md)ismertető témakört. 

Azure Cosmos-tároló létrehozásakor az átviteli sebességet az alábbi módokon konfigurálhatja:

* **Dedikált kiépített átviteli sebesség**: a tárolón kiépített átviteli sebesség kizárólag az adott tároló számára van fenntartva, és a SLA-kat támogatja. További információért lásd: [az átviteli sebesség kiépítése egy Azure Cosmos-tárolón](how-to-provision-container-throughput.md).

* **Megosztott kiépített átviteli sebesség**: ezek a tárolók megosztják a kiosztott átviteli sebességet ugyanabban az adatbázisban lévő többi tárolóval (kivéve a dedikált kiosztott átviteli sebességgel konfigurált tárolókat). Ez azt jelenti, hogy az adatbázison kiosztott átviteli sebesség a "megosztott átviteli sebesség" tárolók között van megosztva. További információ: [az átviteli sebesség kiépítése egy Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md).

> [!NOTE]
> A megosztott és a dedikált átviteli sebességet csak az adatbázis és a tároló létrehozásakor lehet konfigurálni. A dedikált teljesítményű módról a megosztott teljesítményű módra (és fordítva) való váltáshoz a tároló létrehozása után egy új tárolót kell létrehoznia, és migrálnia kell az adatokat az új tárolóba. Az adatáttelepítés a Azure Cosmos DB módosítási funkció használatával végezhető el.

Az Azure Cosmos-tároló rugalmasan méretezhető, függetlenül attól, hogy dedikált vagy megosztott kiosztású átviteli módok használatával hoz létre tárolókat.

Az Azure Cosmos-tároló az elemek sémája és a rendszertől független tárolója. A tároló elemei tetszőleges sémákkal rendelkezhetnek. Például egy olyan elem, amely egy személyt jelöl, és egy személygépkocsit jelképező elem is elhelyezhető *ugyanabban a tárolóban*. Alapértelmezés szerint a rendszer a tárolóhoz hozzáadott összes elemet automatikusan indexeli a explicit index vagy a séma kezelése nélkül. Az indexelési viselkedést úgy szabhatja testre, hogy az [indexelési házirendet](index-overview.md) egy tárolón konfigurálja. 

Beállíthatja, hogy az Azure Cosmos-tárolóban a kijelölt elemek élettartama [(TTL)](time-to-live.md) vagy a teljes tároló számára megfelelően törölje az elemeket a rendszerből. Azure Cosmos DB automatikusan törli az elemeket, amikor lejárnak. Emellett garantálja, hogy a tárolón végrehajtott lekérdezés nem ad vissza egy rögzített kötésen belüli lejárt elemeket. További információ: a [TTL konfigurálása a tárolón](how-to-time-to-live.md).

A [módosítási hírcsatorna](change-feed.md) használatával előfizethet a tároló minden logikai partíciója által felügyelt műveleti naplóra. A módosítási hírcsatorna a tárolón végrehajtott összes frissítés naplóját tartalmazza, valamint az elemek előtti és utáni képeket is. További információ: [reaktív alkalmazások kiépítése a Change feed használatával](serverless-computing-database.md). A módosítási hírcsatorna megőrzési időtartamát a tárolóban lévő hírcsatorna módosítása házirend használatával is konfigurálhatja.

A [tárolt eljárások, eseményindítók, felhasználó által definiált függvények (UDF)](stored-procedures-triggers-udfs.md)és az Azure Cosmos-tároló [egyesítési eljárásai](how-to-manage-conflicts.md) is regisztrálhatók.

Megadhat [egyedi kulcs megkötést](unique-keys.md) az Azure Cosmos-tárolóban. Egyedi kulcsokra vonatkozó szabályzat létrehozásával gondoskodhat a logikai partíciós kulcs egy vagy több értékének egyediségéről. Ha egyedi kulcs-házirend használatával hoz létre tárolót, akkor nem hozható létre olyan új vagy frissített elem, amely az egyedi kulcs megkötése által megadott értékeket duplikáló értékekkel rendelkezik. További információt az [egyedi kulcsokra vonatkozó megkötések](unique-keys.md)című témakörben talál.

Az Azure Cosmos-tárolók API-specifikus entitásokra vannak kialakítva, az alábbi táblázatban látható módon:

| Azure Cosmos-entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-tároló | Tároló | Tábla | Gyűjtemény | Graph | Tábla |

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

### <a name="properties-of-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló tulajdonságai

Az Azure Cosmos-tárolók rendszer által definiált tulajdonságokkal rendelkeznek. Attól függően, hogy melyik API-t használja, előfordulhat, hogy bizonyos tulajdonságok nem lesznek közvetlenül kitéve. A következő táblázat ismerteti a rendszerszintű tulajdonságok listáját:

| Rendszerszintű tulajdonság | Rendszer által generált vagy felhasználó által konfigurálható | Cél | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Rendszer által generált | Tároló egyedi azonosítója | Yes | Nem | Nem | Nem | Nem |
|\_ETAG | Rendszer által generált | Optimista Egyidejűség-vezérléshez használt entitás címkéje | Yes | Nem | Nem | Nem | Nem |
|\_TS | Rendszer által generált | A tároló utolsó frissített időbélyegzője | Yes | Nem | Nem | Nem | Nem |
|\_önálló | Rendszer által generált | A tároló címezhető URI-ja | Yes | Nem | Nem | Nem | Nem |
|id | Felhasználó által konfigurálható | A tároló felhasználó által definiált egyedi neve | Igen | Igen | Igen | Igen | Igen |
|indexingPolicy | Felhasználó által konfigurálható | Lehetővé teszi az index elérési útjának, az index típusának és az index mód módosítását | Yes | Nem | Nem | Nem | Yes |
|TimeToLive | Felhasználó által konfigurálható | Lehetővé teszi az elemek automatikus törlését a tárolóból egy beállított időszak után. Részletekért lásd: [time to Live](time-to-live.md). | Yes | Nem | Nem | Nem | Yes |
|changeFeedPolicy | Felhasználó által konfigurálható | Egy tároló elemein végrehajtott módosítások olvasására szolgál. Részletekért lásd: a [hírcsatorna módosítása](change-feed.md). | Yes | Nem | Nem | Nem | Yes |
|uniqueKeyPolicy | Felhasználó által konfigurálható | Egy logikai partícióban lévő egy vagy több érték egyediségének biztosítására szolgál. További információ: [egyedi kulcsokra vonatkozó megkötések](unique-keys.md). | Yes | Nem | Nem | Nem | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Műveletek egy Azure Cosmos-tárolón

Az Azure Cosmos-tárolók az alábbi műveleteket támogatják az Azure Cosmos API-k bármelyikének használata esetén:

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Tárolók enumerálása egy adatbázisban | Igen | Igen | Igen | Igen | NA | NA |
| Tároló beolvasása | Igen | Igen | Igen | Igen | NA | NA |
| Új tároló létrehozása | Igen | Igen | Igen | Igen | NA | NA |
| Tároló frissítése | Igen | Igen | Igen | Igen | NA | NA |
| Tároló törlése | Igen | Igen | Igen | Igen | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos-elemek

Attól függően, hogy melyik API-t használja, egy Azure Cosmos-elem a gyűjteményben szereplő dokumentumot, egy tábla egy sorát, vagy egy gráf egyik csomópontját vagy szegélyét jelöli. Az alábbi táblázat az API-specifikus entitások Azure Cosmos-tételhez való hozzárendelését mutatja be:

| Cosmos-entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-tétel | Item | Sor | Dokumentum | Csomópont vagy peremhálózati | Item |

### <a name="properties-of-an-item"></a>Egy tétel tulajdonságai

Minden Azure Cosmos-eleme a következő, rendszerszintű tulajdonságokkal rendelkezik. Attól függően, hogy melyik API-t használja, előfordulhat, hogy néhányat nem lehet közvetlenül feltenni.

| Rendszerszintű tulajdonság | Rendszer által generált vagy felhasználó által konfigurálható| Cél | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Rendszer által generált | Az objektum egyedi azonosítója | Yes | Nem | Nem | Nem | Nem |
|\_ETAG | Rendszer által generált | Optimista Egyidejűség-vezérléshez használt entitás címkéje | Yes | Nem | Nem | Nem | Nem |
|\_TS | Rendszer által generált | Az elemek utolsó frissítésének időbélyege | Yes | Nem | Nem | Nem | Nem |
|\_önálló | Rendszer által generált | Az elemek címezhető URI-ja | Yes | Nem | Nem | Nem | Nem |
|id | Vagy | A felhasználó által definiált egyedi név logikai partícióban. | Yes | Igen | Igen | Igen | Igen |
|Tetszőleges felhasználó által definiált tulajdonságok | Felhasználó által megadott | A felhasználó által definiált tulajdonságok az API-natív ábrázolásban (beleértve a JSON-t, a BSON és a CQL) | Yes | Igen | Igen | Igen | Igen |

> [!NOTE]
> A tulajdonság egyedisége `id` csak minden logikai partíción belül érvényesül. Több dokumentum is rendelkezhet ugyanazzal a `id` tulajdonsággal különböző partíciós kulcs értékekkel.

### <a name="operations-on-items"></a>Elemek műveletei

Az Azure Cosmos-elemek a következő műveleteket támogatják. Az Azure Cosmos API-k bármelyikét használhatja a műveletek végrehajtásához.

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Beszúrás, csere, törlés, Upsert, olvasás | Nem | Yes | Igen | Igen | Igen | Igen |

## <a name="next-steps"></a>Következő lépések

További információ ezekről a feladatokról és fogalmakról:

* [Átviteli sebesség kiépítése Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md)
* [Átviteli sebesség kiépítése Azure Cosmos-tárolón](how-to-provision-container-throughput.md)
* [Logikai partíciók használata](partition-data.md)
* [TTL konfigurálása Azure Cosmos-tárolón](how-to-time-to-live.md)
* [Reaktív alkalmazások kiépítése a változási hírcsatorna használatával](change-feed.md)
* [Egyedi kulcsra vonatkozó korlátozás konfigurálása az Azure Cosmos-tárolón](unique-keys.md)
