---
title: Tárolók lekérdezése az Azure Cosmos DB-ben
description: Megtudhatja, hogyan kérdezheti le a tárolókat az Azure Cosmos DB-ben partíción és partíciók közötti lekérdezések használatával
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131399"
---
# <a name="query-an-azure-cosmos-container"></a>Azure Cosmos-tároló lekérdezése

Ez a cikk bemutatja, hogyan lehet lekérdezni egy tárolót (gyűjtemény, grafikon vagy tábla) az Azure Cosmos DB-ben. Különösen azt ismerteti, hogyan működik a partíción és a partíción átnyúló lekérdezések az Azure Cosmos DB-ben.

## <a name="in-partition-query"></a>Partíción belüli lekérdezés

Amikor adatokat kérdez le a tárolókból, ha a lekérdezés egy partíciókulcs-szűrő megvan adva, az Azure Cosmos DB automatikusan optimalizálja a lekérdezést. A lekérdezést a szűrőben megadott partíciókulcs-értékeknek megfelelő [fizikai partíciókra irányítja.](partition-data.md#physical-partitions)

Vegyük például az alábbi lekérdezést `DeviceId`egyenlőségszűrővel a területen. Ha ezt a lekérdezést egy `DeviceId`partíciós tárolón futtatjuk, ez a lekérdezés egyetlen fizikai partícióra szűr.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

A korábbi példához ugyanúgy, mint a korábbi példában, ez a lekérdezés is szűri egyetlen partícióra. A további szűrő `Location` hozzáadása nem változtat ezen:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Az alábbiakban egy lekérdezést, amely rendelkezik egy tartomány szűrő a partíciókulcs, és nem lesz hatóköre egyetlen fizikai partícióra. Ahhoz, hogy partíción lévő lekérdezés legyen, a lekérdezésnek rendelkeznie kell egy egyenlőségi szűrővel, amely tartalmazza a partíciókulcsot:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Többpartíciós kiterjedő lekérdezés

A következő lekérdezés nem rendelkezik szűrővel`DeviceId`a partíciókulcson ( ). Ezért meg kell fan-out minden fizikai partíciót, ahol fut az egyes partíciók indexe:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Minden fizikai partíció saját indexszel rendelkezik. Ezért ha partíciók közötti lekérdezést futtat egy tárolón, fizikai partíciónként gyakorlatilag egy *lekérdezést* futtat. Az Azure Cosmos DB automatikusan összesíti az eredményeket a különböző fizikai partíciók között.

A különböző fizikai partíciók indexei függetlenek egymástól. Nincs globális index az Azure Cosmos DB-ben.

## <a name="parallel-cross-partition-query"></a>Párhuzamos többpartíciós lekérdezés

Az Azure Cosmos DB SDKs 1.9.0-s és újabb verziói támogatják a párhuzamos lekérdezés-végrehajtási beállításokat. A párhuzamos többpartíciós lekérdezésekkel kis késésű többpartíciós lekérdezések hajthatók végre.

A lekérdezések párhuzamos végrehajtását az alábbi paraméterek beállításával kezelheti:

- **MaxConcurrency**: A tároló partícióihoz való egyidejű hálózati kapcsolatok maximális számát állítja be. Ha ezt a `-1`tulajdonságot a tulajdonságra állítja, az SDK kezeli a párhuzamosság mértékét. Ha `MaxConcurrency` a `0`készlet, akkor egyetlen hálózati kapcsolat van a tároló partícióival.

- **MaxBufferedItemCount**: kompromisszumot alakít ki a lekérdezések késése és az ügyféloldali memóriahasználat között. Ha ez a beállítás nincs megadva, vagy a -1 értékre van állítva, az SDK kezeli a párhuzamos lekérdezésvégrehajtása során pufferelt elemek számát.

Mivel az Azure Cosmos DB képes párhuzamosítani a partíciók közötti lekérdezéseket, a lekérdezéskés általában skálázódik, valamint a rendszer [fizikai partíciókat](partition-data.md#physical-partitions)ad hozzá. A VT-díj azonban jelentősen megnő a fizikai partíciók teljes számának növekedésével.

Partíciók közötti lekérdezés futtatásakor lényegében egy külön lekérdezést végez egyéni fizikai partíciónként. Míg a partíciós lekérdezések lekérdezések fogja használni az indexet, ha rendelkezésre állnak, még mindig közel sem olyan hatékony, mint a partíción lévő lekérdezések.

## <a name="useful-example"></a>Hasznos példa

Az alábbi analógia a partíciók közötti lekérdezések jobb megértéséhez:

Képzeljük el, hogy ön egy futár, akinek csomagokat kell szállítania különböző apartmankomplexumokba. Minden apartmankomplexumban van egy lista a helyszínen, amely tartalmazza a rezidens összes egységszámát. Összehasonlíthatjuk az egyes apartmankomplexumokat egy fizikai partícióval és minden listával a fizikai partíció indexét.

A példa segítségével összehasonlíthatjuk a partíción és a partíciókközötti lekérdezéseket:

### <a name="in-partition-query"></a>Partíción belüli lekérdezés

Ha a szállítási illesztőprogram ismeri a megfelelő apartman komplexumot (fizikai partíció), akkor azonnal a megfelelő épületbe vezethetnek. A sofőr ellenőrizheti a lakópark listáját a rezidens egységszámáról (az indexről), és gyorsan kézbesítheti a megfelelő csomagokat. Ebben az esetben a vezető nem vesztegeti az időt vagy erőfeszítést vezetés egy apartman komplexum, hogy ellenőrizze, ha a csomag címzettjei élnek ott.

### <a name="cross-partition-query-fan-out"></a>Partícióközi lekérdezés (fan-out)

Ha a kézbesítő nem ismeri a megfelelő apartman komplexumot (fizikai partíció), akkor minden egyes lakóépületbe kell vezetnie, és ellenőriznie kell a listát a rezidens egységszámaival (az index). Amint megérkeznek az egyes lakóparkba, továbbra is használhatják az egyes lakosok címét. Azonban ellenőrizniük kell minden apartmankomplexum listáját, függetlenül attól, hogy a csomag címzettjei ott élnek-e vagy sem. Így működnek a partíciók közötti lekérdezések. Bár használhatják az indexet (nem kell minden egyes ajtón kopogtatni), külön-külön ellenőrizniük kell az indexet minden fizikai partícióhoz.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Partícióközi lekérdezés (hatóköre csak néhány fizikai partícióra)

Ha a kézbesítő tudja, hogy a csomag címzettjei egy bizonyos néhány apartmankomplexumon belül élnek, akkor nem kell mindegyikhez vezetniük. Vezetés közben néhány apartman komplexumok továbbra is több munkát igényel, mint a látogató csak egy épület, a szállítási vezető még mindig jelentős időt és energiát takarít meg. Ha egy lekérdezés a `IN` kulcsszót a szűrőjében a partíciókulcssal rendelkezik, csak a megfelelő fizikai partíció indexeit ellenőrzi az adatokért.

## <a name="avoiding-cross-partition-queries"></a>Partíciók közötti lekérdezések elkerülése

A legtöbb tároló esetében elkerülhetetlen, hogy néhány partíciót használó lekérdezések lesznek. Miután néhány partíción átnyúló lekérdezések rendben van! Szinte az összes lekérdezési művelet támogatott partíciók között (logikai partíciókulcsok és fizikai partíciók). Az Azure Cosmos DB számos optimalizálással is rendelkezik a lekérdezési motorban és az ügyfél SDK-kban a lekérdezésvégrehajtás fizikai partíciók közötti párhuzamosításához.

A legtöbb olvasási nehéz esetekben azt javasoljuk, hogy egyszerűen válassza ki a leggyakoribb tulajdonság a lekérdezési szűrők. Azt is meg kell győződnie arról, hogy a partíciókulcs megfelel a [partíciókulcs kiválasztásának egyéb bevált módszereinek.](partitioning-overview.md#choose-partitionkey)

A partíciók közötti lekérdezések elkerülése általában csak nagy tárolók esetén számít. A fizikai partíció indexének eredményére vonatkozó minden alkalommal legalább 2,5 RU-t kell fizetnie, még akkor is, ha a fizikai partíció egyetlen eleme sem felel meg a lekérdezés szűrőjének. Mint ilyen, ha csak egy (vagy csak néhány) fizikai partícióval rendelkezik, a partíciók közötti lekérdezések nem használnak fel lényegesen több RU-t, mint a partíción belül i lekérdezések.

A fizikai partíciók száma a kiépített VT mennyiségéhez van kötve. Minden fizikai partíció lehetővé teszi, hogy legfeljebb 10.000 kiépített RU-k, és legfeljebb 50 GB-nyi adatot tárolhat. Az Azure Cosmos DB automatikusan kezeli a fizikai partíciókat az Ön számára. A tárolóban lévő fizikai partíciók száma a kiosztott átviteli és a felhasznált tárolási függ.

Ha a számítási feladatok megfelelnek az alábbi feltételeknek, próbálja meg elkerülni a partíciók közötti lekérdezéseket:
- Azt tervezi, hogy több mint 30 000 VT-t
- Több mint 100 GB adatot szeretne tárolni

## <a name="next-steps"></a>További lépések

Az alábbi cikkekből megtudhatja, hogy miként particionálhat az Azure Cosmos DB-ben:

- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
