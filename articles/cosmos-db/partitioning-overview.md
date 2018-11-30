---
title: Az Azure Cosmos DB particionálási
description: Az Azure Cosmos DB particionálási áttekintése
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: b89830d566b36b0446836d8f32aee5756e2d0991
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498428"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Az Azure Cosmos DB particionálási

Az egyes tárolók méretezése az adatbázis teljesítménye az alkalmazás igényeinek a Cosmos DB által használt módszer a particionálás. A particionálás révén a tárolóban lévő elemek különböző alkészleteiben nevű logikai partíció vannak felosztva. A logikai partíció tulajdonságának értéke egy partíciós kulcs az egyes elemek alapján hozzák létre.

A logikai partíció része a különböző elemek egy tárolóban. A logikai partíció található elemek a partíciós kulcs értékét a logikai partíciót az összes elem által közösen használt azonosítja.  Vegyük példaként egy olyan tároló, amely dokumentumot, és minden egyes dokumentum rendelkezik egy `UserID` tulajdonság.  Ha `UserID` adattárat biztosít, a partíciós kulcs a tárolóban lévő elemek, és egyedi 1000 `UserID` értékek, 1000 logikai partíciót hoz létre a tárolót.

Tárolóban lévő egyes elemek rendelkezik egy **partíciókulcs** , amely meghatározza, hogy az elem **logikai partíció**, és az egyes elemeket is tartalmaz egy **elemazonosító** (azaz egyedinek és a egy logikai a partíció).  A **index** , egy elem azt egyedileg azonosító és a partíciókulcs és a munkaelem azonosítója kombinálásával létrehozva.

Olyan partíciókulcsot egy fontos döntés, melyek hatással lesznek az alkalmazás teljesítményét.  További tudnivalókért lásd: [olyan Partíciókulcsot](partitioning-overview.md#choose-partitionkey) cikkben részletes útmutatást.

## <a name="logical-partition-management"></a>Felügyeleti logikai partíció

A cosmos DB átláthatóan és automatikusan felügyeli a logikai partíció alakzatot hatékonyan a tároló méretezhetőséget és teljesítményt igényeinek kielégítéséhez (infrastruktúra-kiszolgáló) a fizikai partíciók elhelyezését. Növelje az alkalmazás adatátviteli és tárolási követelményeinek, mert a Cosmos DB automatikusan a kiszolgálók nagyobb számú között elosztani a terhelést logikai partíciót helyezi át. Hogyan kezeli a Cosmos DB a partíciók kapcsolatos további információkért lásd: [logikai partíció](partition-data.md) cikk. Nem kell tudni, hogy ezeket az adatokat, illetve az alkalmazások futtatásához.

A cosmos DB használ a kivonat-alapú particionálás érdekében logikai partíció fizikai partíciók között.  Cosmos DB által kivonatolt egy elem a partíciós kulcs értékét, és a kivonatolt eredmény meghatározza, hogy a fizikai partíciók. A cosmos DB foglal le a kulcsfontosságú terület partíció kulcs kivonatok egyenlően a "n" fizikai partíciók között.

Ugyanazon a partíción belül adatokat elérő lekérdezésekben, költséghatékonyabb, mint a több partíciót lekérdezések. Tranzakciók (a tárolt eljárások és eseményindítók) csak engedélyezett elemek belül egyetlen logikai partíciót ellen.  

## <a id="choose-partitionkey"></a>Olyan partíciókulcsot

Ha olyan partíciókulcsot, vegye figyelembe a következő adatokat:

* Egy logikai partíció engedélyezett felső korlátja 10 GB tárhelyet.  

* A particionált tárolók minimális átviteli sebesség 400 RU/s vannak konfigurálva. Ugyanazzal a partíciókulccsal kérelmeket nem haladhatja meg a partíció kiosztott átviteli sebesség. Ha meghaladja a kiosztott átviteli sebesség, a kérések sebessége korlátozott nem. Ezért fontos, hogy az alkalmazáson belül "hotspotok" nem eredményez olyan partíciókulcsot válasszon.

* Válassza ki, a számítási feladatok egyenletes összes partíciójára és egyenletesen idővel partíciókulcsot.  A választott partíciókulcsot kell terheléselosztást végeznie hatékony partíciós lekérdezések és/vagy tranzakciók a kitűzött célérték elemek elosztása több partíción a skálázhatóság érdekében van szükség.

* Válassza ki a partíciós kulcs, számos különféle értékeket és a hozzáférési mintákat, amelyek egyenlően vannak elosztva a logikai partíciókkal rendelkező. Alapvető, hogy az adatok és a tárolót a tevékenység elosztva a logikai partíció készletét, így erőforrásait az adattárolás és átviteli sebesség a logikai partíció szét lehetnek osztva.

* A partíciókulcsok-kompatibilis tartalmazhatják a tulajdonságokat, amelyeket gyakran jelennek meg a lekérdezéseket szűrőként. Lekérdezések a partíciókulcs a szűrőpredikátumban fel hatékonyan továbbíthatók.

## <a name="next-steps"></a>További lépések

* Ismerje meg [partíciók](partition-data.md)
* Ismerje meg [kiosztott átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* Ismerje meg [az Azure Cosmos DB globális terjesztését](distribute-data-globally.md)
