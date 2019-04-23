---
title: Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben
description: Megtudhatja, hogyan adja meg, és az Azure Cosmos DB-ben a kérelemegység követelményeinek becslése
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 72ec8332a3363d5336fb84b3390d5e44ced0b2e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798963"
---
# <a name="request-units-in-azure-cosmos-db"></a>Az Azure Cosmos DB-kérésegységeiről

Az Azure Cosmos DB kell fizetnie az átviteli sebességet, üzembe helyezése és az óránként felhasznált storage. Átviteli sebesség arról, hogy elegendő rendszererőforrásra elérhető az Azure Cosmos-adatbázis mindig ki kell építeni. Elérik vagy túllépik a elegendő erőforrása van szüksége a [Azure Cosmos DB SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Az Azure Cosmos DB támogatja a több API-k, például az SQL, a MongoDB, a Cassandra, a Gremlin és a tábla. Minden API rendelkezik a saját adatbázis-műveletek. E műveletek közé eső egyszerű pont beolvassa és az összetett lekérdezéseket. Minden egyes adatbázis-művelet alapján a művelet összetettsége rendszer-erőforrásokat használ fel. 

Minden adatbázis-műveletek díja az Azure Cosmos DB által van normalizálva, és van kifejezve *kérelemegység* (vagy röviden a RUs). Átviteli sebességet a pénznemként másodpercenkénti kérelemegység is felfoghatók. Kérelemegység / másodperc az a sebesség-alapú pénznem. Ez kivonatolja a rendszererőforrásokat akadályozhatnak, mint a Processzor, az IOPS és a memória, amelyek szükségesek az Azure Cosmos DB által támogatott adatbázis-műveletek végrehajtásához. 

Elem a rendszer 1 kérelemegység egy 1 KB-os olvasási költség (vagy 1 RU). Minden más adatbázis-műveletek egy fenntartott egységek használata költség hasonlóképpen hozzá vannak rendelve. Függetlenül attól, hogy melyik API-t, amellyel kommunikálhat az Azure Cosmos-tároló költségek mindig mérik által fenntartott egységek. -E az adatbázis-művelet az írási, olvasási, vagy a lekérdezés, költségek mindig a fenntartott egységek mérése történik.

Az alábbi képen látható a magas szintű kiindulópont annak megértéséhez, kérelemegység:

![Adatbázis-műveletek Kérelemegységet használhat.](./media/request-units/request-units.png)

Kezelheti és kapacitásának megtervezése, az Azure Cosmos DB biztosítja, hogy a fenntartott egységek számát egy adott adatbázis-művelet fölé egy adott adatkészlet determinisztikus. Tekintse meg a válasz fejléce bármilyen adatbázis-művelet által felhasznált Kérelemegységek számát nyomon követéséhez. Ha már megismerte a [tényező befolyásolja a Kérelemegység díjai](request-units.md#request-unit-considerations) és az alkalmazás átviteli sebességet megkövetelő, költséghatékony alkalmazását futtathatja.

A fenntartott egységek számát 100 kérelemegység / másodperc, másodpercalapú alapon lépésekben az alkalmazás üzembe helyezése. Az alkalmazás a kiosztott átviteli kapacitás méretezését, növelheti vagy csökkentheti a fenntartott egységek számát bármikor. A lépésekben vagy 100 kérelemegységet csökkentésére skálázhatja. A módosításokat végezheti el, vagy programozott módon, vagy az Azure portal használatával. Óradíjat számítunk fel.

Átviteli sebességet két különböző granularitással is kioszthatja: 

* **Tárolók**: További információkért lásd: [üzembe helyezése egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).
* **Adatbázisok**: További információkért lásd: [üzembe helyezése egy Azure Cosmos database átviteli sebességet](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Kérelem egység kapcsolatos szempontok

Becslései szerint a fenntartott egységek száma másodpercenként üzembe helyezni, míg a következő tényezőket kell figyelembe venni:

* **Konfigurációelem-méret**: Egy elem mérete növekszik, olvasására vagy írására elem felhasznált Kérelemegységek számát is növekszik.

* **Elem indexelő**: Alapértelmezés szerint automatikusan indexelt egyes elemek. Ha úgy dönt, hogy nem a tárolóban lévő elemek index kevesebb Kérelemegységet felhasznált.

* **Konfigurációelem-tulajdonság száma**: A fenntartott egységek számát írhat egy elem növeli az elem tulajdonság száma növekszik, feltéve, hogy az összes tulajdonság az alapértelmezett indexelési van, felhasznált.

* **Indexelt tulajdonságok**: Az egyes tárolók index szabályzatokhoz meghatározza, hogy mely tulajdonságok indexelt alapértelmezés szerint. Az írási műveletek a fogyasztott csökkentése érdekében az indexelt tulajdonságok számának korlátozásához.

* **Az adatkonzisztencia**: Az erős és a korlátozott frissesség konzisztenciaszint körülbelül kétszer több fogyasztanak képest, hogy az egyéb enyhe konzisztenciaszintek olvasási műveletek végrehajtása közben.

* **Lekérdezési minták**: A lekérdezés összetettségétől hatással van egy művelet felhasznált Kérelemegységek számát. A lekérdezési műveletek költségét befolyásoló tényezőket a következők: 
    
    - A lekérdezési eredmények száma
    - A predikátum száma
    - A predikátum jellege
    - A felhasználó által definiált függvények száma
    - Az adatok mérete
    - Az eredményhalmaz mérete
    - Kivetítések

  Az Azure Cosmos DB garantálja, hogy ugyanabból a lekérdezés ugyanazokat az adatokat a always költségek ismételt végrehajtás RUs azonos számú.

* **Parancsfájl-használat**: Lekérdezések, a tárolt eljárások és eseményindítók alapján fogyasztanak végrehajtott műveleteket bonyolultságát. Mivel az alkalmazás fejlesztését, vizsgálja meg a [díj fejléc](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) segít jobban megérteni, mekkora igényel minden egyes művelet RU kapacitást.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli](set-throughput.md).
* Tudjon meg többet [logikai partíció](partition-data.md).
* Ismerje meg, hogyan [globálisan méretezési kiosztott átviteli sebesség](scaling-throughput.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos database átviteli sebességet](how-to-provision-database-throughput.md).
* Ismerje meg, hogyan [keresse meg a kérelem egységek használata után egy művelet](find-request-unit-charge.md).
* Ismerje meg, hogyan [optimalizálása az Azure Cosmos DB kiosztott átviteli sebesség költség](optimize-cost-throughput.md).
* Ismerje meg, hogyan [olvasása és írása az Azure Cosmos DB költségek optimalizálása](optimize-cost-reads-writes.md).
* Ismerje meg, hogyan [optimalizálása az Azure Cosmos DB lekérdezési költség](optimize-cost-queries.md).
