---
title: Kérelmek egységei és átviteli sebessége Azure Cosmos DB
description: Tudnivalók a kérések egységre vonatkozó követelményeinek megadásáról és becsléséről Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394216"
---
# <a name="request-units-in-azure-cosmos-db"></a>Kérelmek egységei Azure Cosmos DB

Az Azure Cosmos DB használatakor csak a kiosztott átviteli sebességért és a felhasznált tárolásért kell fizetnie, óránkénti alapon. Az átviteli sebességet úgy kell kiosztani, hogy mindig elegendő rendszererőforrás álljon rendelkezésre az Azure Cosmos-adatbázishoz. Elegendő erőforrásra van szüksége ahhoz, hogy elérje vagy túllépje a [Azure Cosmos db SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)-kat.

Azure Cosmos DB számos olyan API-t támogat, mint például az SQL, a MongoDB, a Cassandra, a Gremlin és a table. Mindegyik API saját adatbázis-műveletekkel rendelkezik. Ezek a műveletek az egyszerű pont olvasási és írási műveleteiből származnak összetett lekérdezésekre. Minden adatbázis-művelet a művelet bonyolultsága alapján használja a rendszererőforrásokat. 

Az összes adatbázis-művelet díját a Azure Cosmos DB normalizálja, és a *kérelmek egységei* (vagy RUs, röviden) szerint vannak kifejezve. A másodpercenkénti kérelemegység az átviteli sebesség pénznemeként is felfogható. A másodpercenkénti kérelemegység díjalapú pénznem. Absztrahálja azon rendszer-erőforrásokat, például a CPU-t, az IOPS-ot és a memóriát, amelyekre az Azure Cosmos DB által támogatott adatbázis-műveletek elvégzéséhez szükség van. 

Egy 1 KB-os elem beolvasásának költsége 1 kérelemegység (1 RU). 1 GB-nyi adat tárolásához legalább 10 RU/s szükséges. Ugyanígy az összes többi adatbázis-művelethez is rendelve van egy költség kérelemegységben megadva. A rendszer mindig kérelemegységben számítja a költségeket attól függetlenül, hogy melyik API segítségével kommunikál az Azure Cosmos-tárolóval. A költségek számítása mindig kérelemegységben történik függetlenül attól, hogy az adatbázis-művelet írás, olvasás vagy lekérdezés-e.

A következő képen a kérelemegységek áttekintése látható:

![Adatbázis-műveletek felhasználásának kérelmezési egységei](./media/request-units/request-units.png)

A kapacitás kezelése és tervezése érdekében az Azure Cosmos DB biztosítja, hogy egy adott adatbázis-művelethez a kérelemegységek száma egy adott adatkészletre determinisztikus legyen. Megvizsgálhatja a válasz fejlécét bármely adatbázis-művelet által felhasznált kérelemegységek számának nyomon követéséhez. Ha megérti [azokat a tényezőket, amelyek hatással vannak az ru-díjakra](request-units.md#request-unit-considerations) és az alkalmazás átviteli sebességére vonatkozó követelményekre, akkor az alkalmazás költségei hatékonyan futtathatók.

Az alkalmazáshoz a kérelemegységeket másodpercalapon oszthatja ki, másodpercenként 100 kérelemegységnyi lépésekben. Ha méretezni szeretné az alkalmazáshoz kiosztott átviteli sebességet, bármikor növelheti vagy csökkentheti a kérelemegységek számát. A skálázást 100 kérelemegység értékű lépésekben végezheti. A módosításokat elvégezheti programozással vagy az Azure Portalon is. A díjak felszámítása óraalapú.

Az átviteli sebességet két különböző részletességgel is kiépítheti: 

* **Tárolók**: további információkért lásd az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítését ismertető témakört.
* **Adatbázisok**: további információt az [átviteli sebesség kiépítése Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md)című témakörben talál.

## <a name="request-unit-considerations"></a>Kérési egység szempontjai

A kiépítés során a másodpercenkénti számú RUs becslése során vegye figyelembe a következő tényezőket:

* **Elem mérete**: Ha az elem mérete növekszik, az elem olvasásához vagy írásához felhasznált RUs száma is növekszik.

* **Elemek indexelése**: alapértelmezés szerint minden elem automatikusan indexelve lesz. Ha úgy dönt, hogy nem indexel néhány elemet egy tárolóban, a rendszer kevesebb RUs-t használ fel.

* **Elem tulajdonságainak száma**: feltételezve, hogy az alapértelmezett indexelés az összes tulajdonságra vonatkozik, az elem írásához felhasznált RUs száma nő, mivel az elem tulajdonságainak száma nő.

* **Indexelt tulajdonságok**: az egyes tárolók indexelési szabályzata határozza meg, hogy alapértelmezés szerint mely tulajdonságok vannak indexelve. Az írási műveletek esetében az RU-felhasználás csökkentése érdekében korlátozza az indexelt tulajdonságok számát.

* **Adatkonzisztencia**: az erős és a határos inkonzisztens konzisztencia-szintek körülbelül kétszer használják az olvasási műveleteket a többi nyugodt konzisztencia-szinthez képest.

* **Lekérdezési minták**: a lekérdezés bonyolultsága befolyásolja, hogy hány RUs van felhasználva egy művelethez. A lekérdezési műveletek költségeit befolyásoló tényezők a következők: 
    
    - A lekérdezés eredményeinek száma
    - A predikátumok száma
    - A predikátumok természete
    - A felhasználó által definiált függvények száma
    - A forrásadatok mérete
    - Az eredményhalmaz mérete
    - Leképezések

  Azure Cosmos DB garantálja, hogy ugyanaz a lekérdezés ugyanazokat az adatmennyiségeket is felszámítja, mint az azonos számú RUs az ismétlődő végrehajtásokban.

* **Parancsfájlok használata**: a lekérdezésekhez hasonlóan a tárolt eljárások és eseményindítók a végrehajtott műveletek összetettsége alapján használják az RUs-ket. Az alkalmazás fejlesztése során vizsgálja meg a [kérelem díjszabási fejlécét](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) , hogy jobban megértse az egyes műveletek által felhasznált ru-kapacitás mennyiségét.

## <a name="next-steps"></a>Következő lépések

* További információ az [átviteli sebesség Azure Cosmos-tárolók és-adatbázisok létesítéséről](set-throughput.md).
* További információ a [logikai partíciókhoz](partition-data.md).
* További információ a [kiépített átviteli sebesség globális skálázásáról](scaling-throughput.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.
* Megtudhatja, hogyan [keresheti meg a műveletre vonatkozó kérési egység díját](find-request-unit-charge.md).
* Ismerje meg, hogyan [optimalizálható a kiépített átviteli sebesség a Azure Cosmos DBban](optimize-cost-throughput.md).
* Megtudhatja, hogyan [optimalizálhatja az olvasási és írási költségeket a Azure Cosmos DBban](optimize-cost-reads-writes.md).
* Megtudhatja, hogyan [optimalizálhatja Azure Cosmos db lekérdezési költségeit](optimize-cost-queries.md).
* Megtudhatja, hogyan [használhatja a mérőszámokat az átviteli sebesség figyelésére](use-metrics.md).
