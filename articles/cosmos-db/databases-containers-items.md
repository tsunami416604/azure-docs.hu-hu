---
title: Adatbázisok, tárolók és elemek munkája az Azure Cosmos DB-ben
description: Ez a cikk ismerteti, hogyan hozhat létre és használhat adatbázisokat, tárolókat és elemeket az Azure Cosmos DB-ben.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246785"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Adatbázisok, tárolók és elemek munkája az Azure Cosmos DB-ben

Miután létrehozott egy [Azure Cosmos DB-fiókot](account-overview.md) az Azure-előfizetésében, adatbázisok, tárolók és elemek létrehozásával kezelheti a fiókjában lévő adatokat. Ez a cikk ezeket az entitásokat ismerteti. 

Az alábbi képen az Azure Cosmos DB-fiók különböző entitásainak hierarchiája látható:

![Azure Cosmos-fiókentitások](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos-adatbázisok

Egy vagy több Azure Cosmos-adatbázist hozhat létre a fiókjában. Az adatbázis hasonló a névtérhez. Az adatbázis az Azure Cosmos-tárolók felügyeleti egysége. Az alábbi táblázat bemutatja, hogyan van leképezve egy Azure Cosmos-adatbázis különböző API-specifikus entitásokhoz:

| Az Azure Cosmos entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-adatbázis | Adatbázis | Kulcstér | Adatbázis | Adatbázis | NA |

> [!NOTE]
> Table API-fiókok, amikor létrehozza az első tábla, az alapértelmezett adatbázis automatikusan létrejön az Azure Cosmos-fiókban.

### <a name="operations-on-an-azure-cosmos-database"></a>Műveletek egy Azure Cosmos-adatbázisban

Az Azure Cosmos-adatbázist az alábbi táblázatban leírtak szerint használhatja az Azure Cosmos API-kkal:

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- |
|Az összes adatbázis felsorolása| Igen | Igen | Igen (az adatbázis kulcstérbe van rendelve) | Igen | NA | NA |
|Adatbázis olvasása| Igen | Igen | Igen (az adatbázis kulcstérbe van rendelve) | Igen | NA | NA |
|Új adatbázis létrehozása| Igen | Igen | Igen (az adatbázis kulcstérbe van rendelve) | Igen | NA | NA |
|Adatbázis frissítése| Igen | Igen | Igen (az adatbázis kulcstérbe van rendelve) | Igen | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-tárolók

Az Azure Cosmos-tároló a méretezhetőségi egység mind a kiosztott átviteli és tárolási egység. Egy tároló vízszintesen particionált, majd több régióban replikálódik. A tárolóhoz hozzáadott elemek és a hozzáépített átviteli rendszer automatikusan elosztva a partíciókulcs on alapuló logikai partíciók között. A particionálásról és a partíciókulcsokról a [Partíciós adatok (Partíciós adatok) (Partíciós adatok) (Partíciós adatok) (Partíciós adatok) (Partíciós adatok) (Partíciós adatok](partition-data.md) 

Amikor létrehoz egy Azure Cosmos-tárolót, az alábbi módok egyikében konfigurálja az átviteli szintet:

* **Dedikált kiépített átviteli mód:** A tárolón kiosztott átviteli túlára kizárólag az adott tároló számára van fenntartva, és az SL-ek támogatja. További információ: [Átviteli átviteli közbeni szolgáltatás kiépítése egy Azure Cosmos-tárolóban.](how-to-provision-container-throughput.md)

* **Megosztott kiosztott átviteli mód:** Ezek a tárolók megosztják a kiosztott átviteli folyamatot az ugyanabban az adatbázisban lévő többi tárolóval (kivéve a dedikált kiosztott átviteli fokkal konfigurált tárolókat). Más szóval az adatbázis kiosztott átviteli keresztüli átavan osztva az összes "megosztott átviteli" tárolók között. További információ: [Átviteli átviteli közbeni szolgáltatás kiépítése egy Azure Cosmos-adatbázisban.](how-to-provision-database-throughput.md)

> [!NOTE]
> A megosztott és dedikált átviteli hang csak az adatbázis és a tároló létrehozásakor konfigurálható. A dedikált teljesítményű módról a megosztott teljesítményű módra (és fordítva) való váltáshoz a tároló létrehozása után egy új tárolót kell létrehoznia, és migrálnia kell az adatokat az új tárolóba. Az adatok áttelepítése az Azure Cosmos DB változáscsatorna szolgáltatás használatával.

Az Azure Cosmos-tároló rugalmasan skálázható, függetlenül attól, hogy dedikált vagy megosztott kiosztott átviteli módok használatával hoz létre tárolókat.

Az Azure Cosmos-tároló egy séma-független tároló elemek. A tárolóban lévő elemek tetszőleges sémákkal rendelkezhetnek. Egy személyt képviselő elem és egy gépkocsit ábrázoló elem például ugyanabban a *tárolóban*helyezhető el. Alapértelmezés szerint a tárolóhoz hozzáadott összes elem automatikusan indexelésre kerül explicit index- vagy sémakezelés nélkül. Az indexelési viselkedés testreszabásához konfigurálja az [indexelési házirendet](index-overview.md) egy tárolón. 

Beállíthatja [a Time to Live (TTL)](time-to-live.md) a kijelölt elemek egy Azure Cosmos-tárolóban, vagy a teljes tároló tao-tároló szabályosan törli ezeket az elemeket a rendszerből. Az Azure Cosmos DB automatikusan törli az elemeket, amikor lejár. Azt is garantálja, hogy a tárolón végrehajtott lekérdezés nem adja vissza a lejárt elemeket egy rögzített kötött. További információ: [TTL konfigurálása a tárolón.](how-to-time-to-live.md)

A [módosítási hírcsatorna](change-feed.md) segítségével előfizethet a tároló minden logikai partíciójára felügyelt műveleti naplóra. A módosítási hírcsatorna biztosítja a tárolón végrehajtott összes frissítés naplóját, valamint az elemek előtte és utána készült képeit. További információ: [Reaktív alkalmazások létrehozása módosítási hírcsatornával.](serverless-computing-database.md) A változáscsatorna-csatorna megőrzési időtartamát a tárolón lévő változáscsatorna-házirend használatával is konfigurálhatja. 

Regisztrálhatja [a tárolt eljárásokat, eseményindítókat, a felhasználó által definiált függvényeket (UDF-](stored-procedures-triggers-udfs.md)és az Azure Cosmos-tároló [eljárások at).](how-to-manage-conflicts.md) 

Az Azure Cosmos-tárolóegyedi [kulcsmegkötést](unique-keys.md) adhat meg. Egy egyedi kulcsházirend létrehozásával logikai partíciókulcsonként egy vagy több érték egyediségét biztosítja. Ha egyedi kulcsházirend használatával hoz létre tárolót, nem hozhatók létre olyan új vagy frissített elemek, amelyek értékei megkettőzik az egyedi kulcsmegkötés által megadott értékeket. További információ: [Egyedi kulcsmegkötések](unique-keys.md).

Az Azure Cosmos-tároló API-specifikus entitásokra specializálódott, az alábbi táblázatban látható módon:

| Az Azure Cosmos entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-tároló | Tároló | Tábla | Gyűjtemény | Graph | Tábla |

### <a name="properties-of-an-azure-cosmos-container"></a>Egy Azure Cosmos-tároló tulajdonságai

Az Azure Cosmos-tároló rendszeráltal definiált tulajdonságokkal rendelkezik. Attól függően, hogy melyik API-t használja, előfordulhat, hogy egyes tulajdonságok nem közvetlenül elérhetővé. Az alábbi táblázat a rendszer által definiált tulajdonságok listáját ismerteti:

| Rendszer által definiált tulajdonság | A rendszer által generált vagy felhasználó által konfigurálható | Cél | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | A rendszer által létrehozott | A tároló egyedi azonosítója | Igen | Nem | Nem | Nem | Nem |
|\_Etag | A rendszer által létrehozott | Optimista egyidejűség-ellenőrzéshez használt entitáscímke | Igen | Nem | Nem | Nem | Nem |
|\_Ts | A rendszer által létrehozott | A tároló utolsó frissített időbélyege | Igen | Nem | Nem | Nem | Nem |
|\_Önálló | A rendszer által létrehozott | A tároló címezhető URI-ja | Igen | Nem | Nem | Nem | Nem |
|id | Felhasználó által konfigurálható | A tároló felhasználó által definiált egyedi neve | Igen | Igen | Igen | Igen | Igen |
|indexelési házirend | Felhasználó által konfigurálható | Lehetővé teszi az index elérési útjának, indextípusának és tárgymutató-módjának módosítását | Igen | Nem | Nem | Nem | Igen |
|TimeToLive | Felhasználó által konfigurálható | Lehetővé teszi az elemek automatikus törlését a tárolóból egy meghatározott időszak után. További részletek: [Time to Live](time-to-live.md). | Igen | Nem | Nem | Nem | Igen |
|changeFeedPolicy (módosítás: feedpolicy) | Felhasználó által konfigurálható | A tárolóban lévő elemeken végrehajtott módosítások olvasására szolgál. További információt a [Hírcsatorna módosítása (Hírcsatorna módosítása) (Hírcsatorna módosítása) (Hírcsatorna módosítása) (Hír](change-feed.md) | Igen | Nem | Nem | Nem | Igen |
|uniqueKeyPolicy | Felhasználó által konfigurálható | Egy logikai partíció egy vagy több értékegyediségének biztosítására szolgál. További információ: [Egyedi kulcsmegkötések](unique-keys.md). | Igen | Nem | Nem | Nem | Igen |

### <a name="operations-on-an-azure-cosmos-container"></a>Műveletek egy Azure Cosmos-tárolón

Az Azure Cosmos-tároló a következő műveleteket támogatja az Azure Cosmos API-k bármelyikének használatakor:

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- |
| Tárolók számbavétele az adatbázisban | Igen | Igen | Igen | Igen | NA | NA |
| Tároló olvasása | Igen | Igen | Igen | Igen | NA | NA |
| Új tároló létrehozása | Igen | Igen | Igen | Igen | NA | NA |
| Tároló frissítése | Igen | Igen | Igen | Igen | NA | NA |
| Tároló törlése | Igen | Igen | Igen | Igen | NA | NA |

## <a name="azure-cosmos-items"></a>Az Azure Cosmos elemei

Attól függően, hogy melyik API-t használja, az Azure Cosmos-elem képviselheti egy gyűjteményben lévő dokumentumot, egy tábla sorát vagy egy csomópontot vagy egy grafikon szélét. Az alábbi táblázat az API-specifikus entitások leképezését mutatja be egy Azure Cosmos-elemhez:

| Cosmos entitás | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-elem | Dokumentum | Sor | Dokumentum | Csomópont vagy él | Elem |

### <a name="properties-of-an-item"></a>Elem tulajdonságai

Minden Azure Cosmos-elem rendelkezik a következő rendszer által meghatározott tulajdonságokkal. Attól függően, hogy melyik API-t használja, előfordulhat, hogy néhány uk nem közvetlenül elérhető.

| Rendszer által definiált tulajdonság | A rendszer által generált vagy felhasználó által konfigurálható| Cél | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | A rendszer által létrehozott | A cikk egyedi azonosítója | Igen | Nem | Nem | Nem | Nem |
|\_Etag | A rendszer által létrehozott | Optimista egyidejűség-ellenőrzéshez használt entitáscímke | Igen | Nem | Nem | Nem | Nem |
|\_Ts | A rendszer által létrehozott | Az elem utolsó frissítésének időbélyege | Igen | Nem | Nem | Nem | Nem |
|\_Önálló | A rendszer által létrehozott | Az elem címezhető URI-ja | Igen | Nem | Nem | Nem | Nem |
|id | Vagy | Felhasználó által definiált egyedi név logikai partícióban. | Igen | Igen | Igen | Igen | Igen |
|Tetszőleges, felhasználó által definiált tulajdonságok | Felhasználó által megadott | Az API-natív ábrázolásban (beleértve a JSON, BSON és CQL) szereplő, felhasználó által definiált tulajdonságokat | Igen | Igen | Igen | Igen | Igen |

> [!NOTE]
> A `id` tulajdonság egyedisége csak az egyes logikai partíciókon belül van kényszerítve. Több dokumentum is `id` rendelkezhet ugyanazzal a tulajdonsággal különböző partíciókulcs-értékekkel.

### <a name="operations-on-items"></a>Cikkek műveletei

Az Azure Cosmos-elemek a következő műveleteket támogatják. Az Azure Cosmos API-k bármelyikét használhatja a műveletek végrehajtásához.

| Művelet | Azure CLI | SQL API | Cassandra API | MongoDB-hez készült Azure Cosmos DB API | Gremlin API | Tábla API |
| --- | --- | --- | --- | --- | --- | --- |
| Beszúrás, Csere, Törlés, Upsert, Olvasás | Nem | Igen | Igen | Igen | Igen | Igen |

## <a name="next-steps"></a>További lépések

Ismerje meg ezeket a feladatokat és fogalmakat:

* [Átviteli átbocsátás kiépítése egy Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md)
* [Átviteli átbocsátás kiépítése egy Azure Cosmos-tárolón](how-to-provision-container-throughput.md)
* [Logikai partíciók kal való munka](partition-data.md)
* [TTL konfigurálása egy Azure Cosmos-tárolón](how-to-time-to-live.md)
* [Reaktív alkalmazások létrehozása módosítási hírcsatorna használatával](change-feed.md)
* [Egyedi kulcsmegkötés konfigurálása az Azure Cosmos-tárolón](unique-keys.md)
