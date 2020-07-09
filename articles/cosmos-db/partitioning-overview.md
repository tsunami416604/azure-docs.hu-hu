---
title: Particionálás az Azure Cosmos DB-ben
description: Tudnivalók a particionálásról Azure Cosmos DB, ajánlott eljárások a partíciós kulcs kiválasztásakor és a logikai partíciók kezeléséhez
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: aa7d67cd6bd1bd422bd257b75ac5bde3bd534d7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85481833"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Particionálás az Azure Cosmos DB-ben

A Azure Cosmos DB particionálás használatával méretezi az egyes tárolókat egy adatbázisban az alkalmazás teljesítménybeli igényeinek kielégítése érdekében. A particionálás során a tároló elemei a *logikai partíciók*nevű különálló részhalmazokra vannak osztva. A logikai partíciók a tároló egyes elemeihez társított *partíciós kulcs* értéke alapján jönnek létre. Egy logikai partíció összes elemének ugyanaz a partíciós kulcs értéke.

Egy tároló például elemeket tárol. Minden egyes tétel egyedi értékkel rendelkezik a `UserID` tulajdonsághoz. Ha a `UserID` tárolóban lévő elemek partíciós kulcsaként szolgál, és 1 000 egyedi érték van `UserID` , akkor a rendszer 1 000 logikai partíciókat hoz létre a tárolóhoz.

Egy olyan partíciós kulcs mellett, amely meghatározza az elem logikai partícióját, a tároló minden eleméhez tartozik egy *elem azonosítója* (egyedi logikai partíción belül). A partíciós kulcs és az *elem azonosítójának* kombinálásával létrejön az elem *indexe*, amely egyedileg azonosítja az adott tételt.

[A partíciós kulcs kiválasztása](partitioning-overview.md#choose-partitionkey) fontos döntés, amely hatással lesz az alkalmazás teljesítményére.

## <a name="managing-logical-partitions"></a>Logikai partíciók kezelése

Azure Cosmos DB átlátható módon, és automatikusan kezeli a logikai partíciók elhelyezését a fizikai partíciókon, hogy hatékonyan kielégítse a tároló méretezhetőségét és teljesítményét. Az alkalmazások adatátviteli és tárolási követelményeinek növekedésével Azure Cosmos DB a logikai partíciók mozgatásával automatikusan elosztja a terhelést a több fizikai partíció között. További információ a [fizikai partíciók](partition-data.md#physical-partitions)szolgáltatásról.

A Azure Cosmos DB kivonatoló particionálást használ a logikai partíciók fizikai partíciók közötti elterjesztéséhez. Azure Cosmos DB kivonatok egy adott tétel partíciós kulcsának értékét. A kivonatos eredmény határozza meg a fizikai partíciót. Ezután a Azure Cosmos DB a partíciós kulcsok kivonatait egyenletesen osztja el a fizikai partíciók között.

A tranzakciókat (tárolt eljárásokban vagy eseményindítókban) csak egyetlen logikai partíció elemeire lehet engedélyezni.

További tudnivalókat a [Azure Cosmos db a partíciók kezelése](partition-data.md)című témakörben olvashat. (Nem szükséges megérteni az alkalmazások létrehozásához és futtatásához szükséges belső adatokat, de itt egy kíváncsi olvasóhoz is hozzá lehet adni őket.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Partíciókulcs kiválasztása

A partíciós kulcsnak két összetevője van: a **partíciós kulcs elérési útja** és a **partíciós kulcs értéke**. Vegyünk például egy {"userId": "Andrew", "worksFor": "Microsoft"} elemet, ha a "felhasználóazonosító" a partíciós kulcsként van kiválasztva, a következő két partíciós kulcs összetevő:

* A partíciós kulcs elérési útja (például: "/userId"). A partíciós kulcs elérési útja alfanumerikus és aláhúzás (_) karaktereket fogad el. A beágyazott objektumokat a szabványos elérési út (/) használatával is használhatja.

* A partíciós kulcs értéke (például: "Andrew"). A partíciós kulcs értéke lehet karakterlánc vagy numerikus típus.

Az átviteli sebességre, a tárterületre és a partíciós kulcs hosszára vonatkozó korlátokkal kapcsolatos további tudnivalókért tekintse meg a [Azure Cosmos db szolgáltatási kvótákat](concepts-limits.md) ismertető cikket.

A partíciós kulcs kiválasztásával egy egyszerű, de fontos kialakítási lehetőség van a Azure Cosmos DB. A partíciós kulcs kiválasztását követően nem lehet helyben módosítani. Ha módosítania kell a partíciós kulcsot, helyezze át az adatait egy új tárolóba az új kívánt partíciós kulccsal.

Az **összes** tároló esetében a partíciós kulcsnak a következőket kell tennie:

* Olyan tulajdonságnak kell lennie, amely nem módosítható értékkel rendelkezik. Ha a tulajdonság a partíciós kulcs, a tulajdonság értéke nem frissíthető.
* Magas fokú kardinális. Más szóval a tulajdonságnak a lehetséges értékek széles körének kell lennie.
* A kérési egység (RU) felhasználásának és az adattárolásnak egyenletes elosztása az összes logikai partíción keresztül. Ez biztosítja a fizikai partíciók esetében is az RU-felhasználást és a tárolók eloszlását.

Ha Azure Cosmos DB [több tételes savas tranzakcióra](database-transactions-optimistic-concurrency.md#multi-item-transactions) van szüksége, akkor [tárolt eljárásokat vagy eseményindítókat](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)kell használnia. Minden JavaScript-alapú tárolt eljárás és eseményindító hatóköre egyetlen logikai partícióra van korlátozva.

## <a name="partition-keys-for-read-heavy-containers"></a>Partíciós kulcsok olvasási-nehéz tárolók számára

A legtöbb tároló esetében a fenti feltételek csak a partíciós kulcsok kiválogatásakor szükségesek. Nagyméretű, nehezen olvasható tárolók esetében azonban érdemes lehet olyan partíciós kulcsot választania, amely gyakran jelenik meg szűrőként a lekérdezésekben. A lekérdezések [hatékonyan irányíthatók úgy, hogy csak a megfelelő fizikai partíciók számára legyenek átirányítva](how-to-query-container.md#in-partition-query) , a Filter predikátumban található partíciós kulccsal együtt.

Ha a számítási feladatok nagy része lekérdezésekből áll, és a legtöbb lekérdezés egy egyenlőségi szűrővel rendelkezik ugyanazon a tulajdonságon, akkor ez a tulajdonság lehet jó partíciós kulcs választása. Ha például gyakran futtat egy olyan lekérdezést, amely a szűrők alapján fut `UserID` , akkor `UserID` a partíciós kulcs kiválasztásával csökkentheti a több [partíciós lekérdezések](how-to-query-container.md#avoiding-cross-partition-queries)számát.

Ha azonban a tároló kicsi, valószínűleg nem rendelkezik elegendő fizikai partícióval, hogy ne kelljen aggódnia a több partíciós lekérdezések teljesítményére gyakorolt hatásával kapcsolatban. Azure Cosmos DB a legtöbb kisméretű tárolóhoz csak egy vagy két fizikai partíció szükséges.

Ha a tároló több fizikai partícióra is nőhet, akkor győződjön meg arról, hogy olyan partíciós kulcsot választ, amely a több partíciós lekérdezések minimalizálását végzi. A tároló több fizikai partíciót is igényel, ha az alábbiak egyike igaz:

* A tároló több mint 30 000 RU kiépítve lesz
* A tároló 100 GB-nál több adat tárolására is alkalmas

## <a name="using-item-id-as-the-partition-key"></a>Az Item ID használata partíciós kulcsként

Ha a tároló olyan tulajdonsággal rendelkezik, amely a lehetséges értékek széles választékával rendelkezik, valószínűleg nagyszerű partíciós kulcsra van kiválasztva. Ilyen tulajdonság egy lehetséges példa az *Item azonosító*. A kis olvasási nehéz tárolók vagy a nagy méretű írható tárolók esetében az *elem azonosítója* természetesen remek választás a partíciós kulcshoz.

A rendszertulajdonság- *azonosító* a Cosmos-tároló minden eleme esetében garantált. Lehet, hogy más tulajdonságokkal rendelkezik, amelyek az adott eleme logikai AZONOSÍTÓját jelölik. Sok esetben a partíciós kulcsra vonatkozó döntések is megegyeznek az *elem azonosítójának*indokairól.

Az *azonosító* egy nagyszerű partíciós kulcs, amely a következő okok miatt választható:

* A lehetséges értékek széles választéka (egy egyedi *azonosító* /tétel).
* Mivel egy elemhez egyedi *elem-azonosító* van, az *elem azonosítója* nagyszerű feladatot jelent az ru-használat és az adattárolás egyenletes elosztásával.
* Egyszerűen elvégezheti a hatékony pontok olvasását, mivel a rendszer mindig ismeri az elemek partíciós kulcsát, ha ismeri annak *azonosítóját*.

Az *elemek azonosítójának* kiválasztásakor figyelembe kell venni az alábbiakat:

* Ha az *elem azonosítója* a partíciós kulcs, akkor a teljes tárolóban egyedi azonosító lesz. Nem lesz lehetősége ismétlődő *elem-azonosítóval*rendelkező elemekre.
* Ha olyan nagy mennyiségű [fizikai partícióval](partition-data.md#physical-partitions)rendelkező olvasási nehéz tárolóval rendelkezik, a lekérdezések hatékonyabbak lesznek, ha az *elem azonosítójának*megfelelő egyenlőségi szűrővel rendelkeznek.
* Nem futtathat tárolt eljárásokat vagy eseményindítókat több logikai partíción keresztül.

## <a name="next-steps"></a>További lépések

* Tudnivalók a [particionálásról és a horizontális skálázásról Azure Cosmos db](partition-data.md).
* További információ [a kiépített átviteli sebességről Azure Cosmos db](request-units.md).
* Ismerkedjen meg a [Azure Cosmos db globális eloszlásával](distribute-data-globally.md).
