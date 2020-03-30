---
title: Egységek és átviteli műveletek kérése az Azure Cosmos DB-ben
description: A kérelemegység követelményeinek megadása és becslése az Azure Cosmos DB-ben
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246590"
---
# <a name="request-units-in-azure-cosmos-db"></a>Az Azure Cosmos DB kérelemegységei

Az Azure Cosmos DB használatakor csak a kiosztott átviteli sebességért és a felhasznált tárolásért kell fizetnie, óránkénti alapon. Az átviteli sebességet úgy kell kiosztani, hogy mindig elegendő rendszererőforrás álljon rendelkezésre az Azure Cosmos-adatbázishoz. Elegendő erőforrásra van szüksége az [Azure Cosmos DB SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)eléréséhez vagy túllépéséhez.

Az Azure Cosmos DB számos API-t támogat, például az SQL, a MongoDB, a Cassandra, a Gremlin és a Table. Minden API saját adatbázis-műveletkészlettel rendelkezik. Ezek a műveletek az egyszerű pontolvasásoktól és írásoktól az összetett lekérdezésekig terjednek. Minden adatbázis-művelet a művelet összetettsége alapján használja fel a rendszererőforrásokat. 

Az összes adatbázis-műveletek költségét az Azure Cosmos DB normalizálja, és *a kérelemegységek* (röviden a kérelem egységek) fejezik ki. A másodpercenkénti kérelemegység az átviteli sebesség pénznemeként is felfogható. A másodpercenkénti kérelemegység díjalapú pénznem. Absztrahálja azon rendszer-erőforrásokat, például a CPU-t, az IOPS-ot és a memóriát, amelyekre az Azure Cosmos DB által támogatott adatbázis-műveletek elvégzéséhez szükség van. 

Egy 1 KB-os elem beolvasásának költsége 1 kérelemegység (1 RU). Minden 1 GB adat tárolásához legalább 10 RU/s szükséges. Ugyanígy az összes többi adatbázis-művelethez is rendelve van egy költség kérelemegységben megadva. A rendszer mindig kérelemegységben számítja a költségeket attól függetlenül, hogy melyik API segítségével kommunikál az Azure Cosmos-tárolóval. A költségek számítása mindig kérelemegységben történik függetlenül attól, hogy az adatbázis-művelet írás, olvasás vagy lekérdezés-e.

A következő képen a kérelemegységek áttekintése látható:

![Az adatbázis-műveletek kérelemegységeket használnak fel](./media/request-units/request-units.png)

A kapacitás kezelése és tervezése érdekében az Azure Cosmos DB biztosítja, hogy egy adott adatbázis-művelethez a kérelemegységek száma egy adott adatkészletre determinisztikus legyen. Megvizsgálhatja a válasz fejlécét bármely adatbázis-művelet által felhasznált kérelemegységek számának nyomon követéséhez. Ha tisztában van a [ru-költségeket](request-units.md#request-unit-considerations) és az alkalmazás átviteli követelményeit befolyásoló tényezőket, hatékonyan futtathatja az alkalmazást.

Az alkalmazáshoz a kérelemegységeket másodpercalapon oszthatja ki, másodpercenként 100 kérelemegységnyi lépésekben. Ha méretezni szeretné az alkalmazáshoz kiosztott átviteli sebességet, bármikor növelheti vagy csökkentheti a kérelemegységek számát. A skálázást 100 kérelemegység értékű lépésekben végezheti. A módosításokat elvégezheti programozással vagy az Azure Portalon is. A díjak felszámítása óraalapú.

Az átviteli-átbocsátás két különböző részletességgel építhető ki: 

* **Tárolók:** További információkért lásd: [Átviteli átbocsátás kiépítése egy Azure Cosmos-tárolóban.](how-to-provision-container-throughput.md)
* **Adatbázisok:** További információ: [Átviteli átbocsátás kiépítése egy Azure Cosmos-adatbázisban.](how-to-provision-database-throughput.md)

## <a name="request-unit-considerations"></a>A kérelemegységekkel kapcsolatos megfontolások

A másodpercenkénti kérelemegységek kiosztásának becslésekor az alábbi tényezőket érdemes figyelembe vennie:

* **Cikk méret:** Egy elem méretének növekedésével az elem olvasásához vagy írásához felhasznált termékazonosítók száma is nő.

* **Cikkindexelés**: Alapértelmezés szerint minden elem automatikusan indexelésre kerül. Kevesebb kérelemegység lesz felhasználva, ha egy tárolóban egyes elemeket nem indexel.

* **Cikktulajdonságok száma**: Feltéve, hogy az alapértelmezett indexelés minden tulajdonságon szerepel, az elem írásához felhasznált Javak száma nő a cikktulajdonság számának növekedésével.

* **Indexelt tulajdonságok:** Az egyes tárolók indexházirendje határozza meg, hogy alapértelmezés szerint mely tulajdonságok indexelése történik. Ha csökkenteni szeretné az írási műveletek fogyasztását, korlátozza az indexelt tulajdonságok számát.

* **Adatkonzisztencia:** Az erős és határolt frissesség konzisztenciaszintek körülbelül kétszer több rt-t fogyasztanak olvasási műveletek végrehajtása közben, mint más nyugodt konzisztenciaszintek.

* **Lekérdezési minták**: A lekérdezés összetettsége befolyásolja, hogy hány rt-k egy művelet hez felhasznált. A lekérdezési műveletek költségét befolyásoló tényezők többek között: 
    
    - A lekérdezési eredmények száma
    - A predikátumok száma
    - A predikátumok természete
    - Felhasználó által definiált függvények száma
    - A forrásadat mérete
    - Az eredményhalmaz mérete
    - Leképezések

  Az Azure Cosmos DB garantálja, hogy az ugyanazon az adatokon futtatott ugyanaz a lekérdezés ismételt végrehajtás esetén ugyanannyi kérelemegységbe kerül.

* **Parancsfájl-használat:** A lekérdezésekhez ugyanúgy, a tárolt eljárások és eseményindítók a végrehajtott műveletek összetettsége alapján használják fel a rt-eket. Az alkalmazás fejlesztése során vizsgálja meg a [kérelemdíj fejlécet](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query), hogy pontosabb képet kapjon arról, hány kérelemegységet fogyasztanak az egyes műveletek.

## <a name="next-steps"></a>További lépések

* További információ az [Azure Cosmos-tárolók és -adatbázisok átviteli átviteli módjáról.](set-throughput.md)
* További információ a [logikai partíciókról.](partition-data.md)
* További információ a [kiépített átviteli forgalom globális méretezéséről.](scaling-throughput.md)
* Ismerje meg, hogyan [építheti ki az átviteli fazonát egy Azure Cosmos-tárolóban.](how-to-provision-container-throughput.md)
* Ismerje meg, hogyan [építheti ki az átviteli mertét egy Azure Cosmos-adatbázisban.](how-to-provision-database-throughput.md)
* További információ [aművelet kérésegység-díjának megkereséséhez.](find-request-unit-charge.md)
* Ismerje meg, hogyan optimalizálhatja a [kiépített átviteli költség az Azure Cosmos DB.Learn how to optimizeprovisionprovision throughputput cost in Azure Cosmos DB.](optimize-cost-throughput.md)
* Ismerje meg, hogyan [optimalizálhatja az olvasási és írási költségeket az Azure Cosmos DB-ben.](optimize-cost-reads-writes.md)
* Ismerje meg, hogyan [optimalizálhatja a lekérdezési költségeket az Azure Cosmos DB-ben.](optimize-cost-queries.md)
* Ismerje meg, hogyan [használhatja a metrikákat az átviteli teljesítmény figyelésére.](use-metrics.md)
