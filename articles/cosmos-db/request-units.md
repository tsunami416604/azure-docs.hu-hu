---
title: Kérelmek egysége átviteli sebesség és teljesítmény pénznemben Azure Cosmos DB
description: Tudnivalók a kérések egységre vonatkozó követelményeinek megadásáról és becsléséről Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604824"
---
# <a name="request-units-in-azure-cosmos-db"></a>Az Azure Cosmos DB kérelemegységei

Azure Cosmos DB számos olyan API-t támogat, mint például az SQL, a MongoDB, a Cassandra, a Gremlin és a table. Mindegyik API saját adatbázis-műveletekkel rendelkezik. Ezek a műveletek az egyszerű pont olvasási és írási műveleteiből származnak összetett lekérdezésekre. Minden adatbázis-művelet a művelet bonyolultsága alapján használja a rendszererőforrásokat.

Az összes adatbázis-művelet díját a Azure Cosmos DB normalizálja, és a *kérelmek egységei* (vagy RUs, röviden) szerint vannak kifejezve. Azt is megteheti, hogy az RUs olyan teljesítmény pénznemként van kiértékelve, amely a Azure Cosmos DB által támogatott adatbázis-műveletek végrehajtásához szükséges rendszererőforrásokat, például a PROCESSZORt, a IOPS és a memóriát is elvégzi.

Egy pont olvasásának (azaz egyetlen elemnek az azonosító és a partíciós kulcs értékének beolvasása) 1 KB-os elem esetén 1 kérelem egység (vagy 1 RU) lehet. Ugyanígy az összes többi adatbázis-művelethez is rendelve van egy költség kérelemegységben megadva. A rendszer mindig kérelemegységben számítja a költségeket attól függetlenül, hogy melyik API segítségével kommunikál az Azure Cosmos-tárolóval. Azt határozza meg, hogy az adatbázis-művelet írási, olvasási pont vagy lekérdezés-e, a költségeket mindig az RUs méri.

A következő képen a kérelemegységek áttekintése látható:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Adatbázis-műveletek felhasználásának kérelmezési egységei" border="false":::

A kapacitás kezelése és tervezése érdekében az Azure Cosmos DB biztosítja, hogy egy adott adatbázis-művelethez a kérelemegységek száma egy adott adatkészletre determinisztikus legyen. Megvizsgálhatja a válasz fejlécét bármely adatbázis-művelet által felhasznált kérelemegységek számának nyomon követéséhez. Ha megérti [azokat a tényezőket, amelyek hatással vannak az ru-díjakra](request-units.md#request-unit-considerations) és az alkalmazás átviteli sebességére vonatkozó követelményekre, akkor az alkalmazás költségei hatékonyan futtathatók.

Az Ön által használt Azure Cosmos-fiók típusa határozza meg, hogy milyen módon történik a felhasznált RUs felszámítása:

- A [kiépített átviteli sebességi](set-throughput.md) módban az alkalmazáshoz tartozó RUs másodpercenkénti számát a 100 RUS/másodperc értékben kell kiépíteni. Az alkalmazás kiépített átviteli sebességének méretezése érdekében a 100 RUs növekmények vagy csökkentései révén bármikor növelheti vagy csökkentheti az RUs számát. A módosításokat elvégezheti programozással vagy az Azure Portal használatával is. Az Ön által kiépített RUs-mennyiségért óradíjat számolunk fel. Az átviteli sebességet két különböző részletességgel is kiépítheti:
  - **Tárolók**: további információkért lásd az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítését ismertető témakört.
  - **Adatbázisok**: további információt az [átviteli sebesség kiépítése Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md)című témakörben talál.
- [Kiszolgáló](serverless.md) nélküli módban nem szükséges átviteli sebességet kiépíteni az Azure Cosmos-fiókban lévő erőforrások létrehozásakor. A számlázási időszak végén az adatbázis-műveletek által felhasznált kérelmek mennyiségét számoljuk fel.

## <a name="request-unit-considerations"></a>A kérelemegységekkel kapcsolatos megfontolások

A számítási feladatok által felhasznált RUs számának becslése közben vegye figyelembe a következő tényezőket:

* **Elem mérete**: Az elem méretének növekedésével az elem olvasásához vagy írásához szükséges kérelemegységek száma is növekszik.

* **Elem indexelése**: Alapértelmezés szerint minden elem automatikusan indexelve lesz. Kevesebb kérelemegység lesz felhasználva, ha egy tárolóban egyes elemeket nem indexel.

* **Elemtulajdonságok száma**: Feltéve, hogy az alapértelmezett indexelés az összes tulajdonságra van állítva, az elem írásához szükséges kérelemegységek száma az elemtulajdonságok számának növekedésével együtt növekszik.

* **Indexelt tulajdonságok**: Az indexelési szabályzat az egyes tárolókra azt határozza meg, hogy alapértelmezés szerint mely tulajdonságok lesznek indexelve. Ha csökkenteni szeretné az írási műveletek fogyasztását, korlátozza az indexelt tulajdonságok számát.

* **Adatkonzisztencia**: az erős és a határos inkonzisztens konzisztencia-szintek körülbelül kétszer használják az olvasási műveleteket a többi nyugodt konzisztencia-szinthez képest.

* **Az olvasások típusa: a**Point olvasási díja jóval kevesebb, mint a lekérdezéseknél.

* **Lekérdezési mintázatok**: A lekérdezés összetettsége hatással van arra, hogy egy művelethez hány kérelemegység szükséges. A lekérdezési műveletek költségét befolyásoló tényezők többek között: 
    
    - A lekérdezési eredmények száma
    - A predikátumok száma
    - A predikátumok természete
    - Felhasználó által definiált függvények száma
    - A forrásadat mérete
    - Az eredményhalmaz mérete
    - Leképezések

  Az Azure Cosmos DB garantálja, hogy az ugyanazon az adatokon futtatott ugyanaz a lekérdezés ismételt végrehajtás esetén ugyanannyi kérelemegységbe kerül.

* **Parancsfájlok használata**: a lekérdezésekhez hasonlóan a tárolt eljárások és eseményindítók a végrehajtott műveletek összetettsége alapján használják az RUs-ket. Az alkalmazás fejlesztése során vizsgálja meg a [kérelemdíj fejlécet](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query), hogy pontosabb képet kapjon arról, hány kérelemegységet fogyasztanak az egyes műveletek.

## <a name="next-steps"></a>Következő lépések

* További információ az [átviteli sebesség Azure Cosmos-tárolók és-adatbázisok létesítéséről](set-throughput.md).
* További információ a [Azure Cosmos db kiszolgáló](serverless.md)nélküli használatáról.
* További információ a [logikai partíciókhoz](partition-data.md).
* További információ a [kiépített átviteli sebesség globális skálázásáról](scaling-throughput.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.
* Megtudhatja, hogyan [keresheti meg a műveletre vonatkozó kérési egység díját](find-request-unit-charge.md).
* Ismerje meg, hogyan [optimalizálható a kiépített átviteli sebesség a Azure Cosmos DBban](optimize-cost-throughput.md).
* Megtudhatja, hogyan [optimalizálhatja az olvasási és írási költségeket a Azure Cosmos DBban](optimize-cost-reads-writes.md).
* Megtudhatja, hogyan [optimalizálhatja Azure Cosmos db lekérdezési költségeit](optimize-cost-queries.md).
* Megtudhatja, hogyan [használhatja a mérőszámokat az átviteli sebesség figyelésére](use-metrics.md).
