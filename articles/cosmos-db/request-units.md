---
title: Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben
description: Adja meg, hogyan, és az Azure Cosmos DB kérelem egység követelményeinek becslése
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: c43b718002267cd33135c130c6c41cabd8ee9c3b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310036"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB-kérésegységeiről

Az Azure Cosmos DB kell fizetnie az átviteli sebességet, üzembe helyezése és az óránként felhasznált storage. Átviteli sebesség arról, hogy elegendő rendszererőforrásra érhető el az Azure Cosmos database folyamatosan teljesítésére vagy az Azure Cosmos DB SLA ki kell építeni.

Az Azure Cosmos DB támogatja a különböző alkalmazásprogramozási felületek (SQL, MongoDB, Cassandra, Gremlin és Table). Minden API rendelkezik a saját adatbázis-műveleteket, és a egyszerű pont olvassa be és összetett lekérdezéseket ír. Minden egyes adatbázis-művelet attól függően, hogy a művelet összetettsége rendszer-erőforrásokat használ fel.  Az összes adatbázis-műveletek díja Azure Cosmos DB által van normalizálva, és fejezzük kérelemegység (RU-kkal) fejezzük ki. Egy 1 KB-os cikk elolvasható költsége 1 kérelemegység (1 RU) és 1 GB-os tárhelyet felhasználásához szükséges minimális RUs 40. Minden más adatbázis-műveletek egy költséget RUs hasonlóképpen hozzá vannak rendelve. Az API használata kezelheti az Azure Cosmos-tároló és az adatbázis-művelet (olvasási, írási, lekérdezés), függetlenül a költségek mindig mérik RUs.

RU/s átviteli sebességet pénznemként is felfoghatók. RU/s sebesség alapú pénznem, amely kivonatolja a rendszererőforrásokat akadályozhatnak, mint a Processzor, az IOPS és a memória az Azure Cosmos DB által támogatott adatbázis-műveletek végrehajtásához szükséges. Az alábbi képen látható a különböző adatbázis-műveletek által felhasznált kérelemegységek:

![Adatbázis-műveletek kérelemegységek felhasználása](./media/request-units/request-units.png)

Kezelheti és kapacitásának megtervezése, az Azure Cosmos DB biztosítja, hogy a fenntartott egységek számát egy adott adatbázis-művelet fölé egy adott adatkészlet determinisztikus. Nyomon követheti a válaszfejléc megvizsgálásával bármilyen adatbázis-művelet által felhasznált Kérelemegységek számát. Miután megismerkedett a kérelem egységekre vonatkozó díjakon és az alkalmazás átviteli sebességet megkövetelő befolyásoló tényezők, költséghatékonyan futtathatja az alkalmazást.

Óradíjat számítjuk fel, amíg az alkalmazás lépésekben másodpercenkénti alapon 100 RU/s kiépítése a fenntartott egységek számát. A kiosztott átviteli kapacitás méretezését, az alkalmazást, akkor növelheti vagy csökkentheti (a lépésekkel, vagy 100 kérelemegység-gyel csökken) fenntartott egységek száma bármikor, vagy programozott módon, vagy az Azure portal használatával.

Átviteli sebességet két különböző granularitással is kioszthatja: 

* **Tárolók**. További információkért lásd: [hogyan helyezhet üzembe egy Azure Cosmos-tároló átviteli sebességet.](how-to-provision-container-throughput.md)
* **Adatbázisok**. További információkért lásd: [hogyan kioszthatja az átviteli sebességet egy Azure Cosmos database.](how-to-provision-database-throughput.md)

## <a name="request-unit-considerations"></a>Kérelem egység kapcsolatos szempontok

Közben RU/s üzembe helyezni számának becslése, fontos, hogy a következő tényezőket kell figyelembe venni:

* **Konfigurációelem-méret** – ahogy a cikk mérete növekszik, olvasására vagy írására a cikket is nő felhasznált Kérelemegységek számát.

* **Elem indexelő** – alapértelmezés szerint minden elem automatikusan indexelt. Ha úgy dönt, hogy nem a tárolóban lévő elemek index kevesebb kérelemegység felhasznált.

* **Konfigurációelem-tulajdonság száma** – feltéve, hogy az indexelő az összes tulajdonság alapértelmezett, fenntartott egységek számát felhasznált írhat egy elemet a cikk tulajdonság száma növekszik, növeli.

* **Indexelt tulajdonságok** – az egyes tárolók index szabályzatokhoz meghatározza, hogy mely tulajdonságok indexelt alapértelmezés szerint. A kérelem-egységek felhasználását az írási műveletek száma a indexelt tulajdonságok csökkenthető.

* **Az adatkonzisztencia** – az erős és a korlátozott frissesség konzisztenciaszint körülbelül 2 X további Kérelemegységek felhasználása képest, hogy az egyéb enyhe konzisztenciaszintek olvasási műveletek végrehajtása közben.

* **Lekérdezési minták** – a lekérdezés összetettségétől hatással van egy művelet felhasznált kérelemegységek számát. A lekérdezés eredményeit, predikátumok száma, a predikátumok jellegét, felhasználó által definiált függvények száma, az adatok méretétől, az eredmény méretének száma, és leképezések hatással a lekérdezési műveletek költségét. Az Azure Cosmos DB garantálja, hogy ugyanazokat az adatokat az ugyanabból a lekérdezés mindig költség ismétlési végrehajtások a RUs azonos számú.

* **Parancsfájl-használat** - lekérdezéseket, a tárolt eljárások és eseményindítók alapján fogyasztanak végrehajtott műveletek bonyolultságát. Az alkalmazás fejlesztését, vizsgálja meg jobb megértése érdekében minden művelet használ fel, mekkora kérelem egység kapacitást kérelemfejlécből díj.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Cosmos-tárolók és adatbázisok üzembe helyezési átviteli sebesség](set-throughput.md)
* Tudjon meg többet [logikai partíció](partition-data.md)
* Tudjon meg többet [globálisan méretezési kiosztott átviteli sebesség](scaling-throughput.md)
* Ismerje meg, [hogyan helyezhet üzembe egy Azure Cosmos-tároló átviteli sebesség](how-to-provision-container-throughput.md)
* Ismerje meg, [átviteli sebességet egy Azure Cosmos-adatbázis kiépítése](how-to-provision-database-throughput.md)
