---
title: Globálisan elosztott tranzakciós és analitikus (privát előzetes verzióban) tároló az Azure Cosmos-tárolókhoz
description: Ismerje meg a tranzakciós és analitikus tárolást, valamint az Azure Cosmos-tárolók konfigurációs beállításait.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623382"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globálisan elosztott tranzakciós és analitikus tárolás az Azure Cosmos-tárolókhoz

Az Azure Cosmos tárolóját két tárolómotor – tranzakciós tárolási motor és egy upatable analytical storage-motor – belső legeltetése támogatja (privát előzetes verzióban). Mindkét tárolómotor naplószerkezetű és írásra optimalizált a gyorsabb frissítések érdekében. Azonban mindegyik másképp van kódolva:

* **Tranzakciós tárolómotor** – Sororientált formátumban van kódolva a gyors tranzakciós olvasáshoz és lekérdezésekhez.

* **Analitikai tárolómotor** – Oszlopos formátumban van kódolva a gyors analitikus lekérdezésekhez és vizsgálatokhoz.

![Tárolómotorok és Az Azure Cosmos DB API-leképezés](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

A tranzakciós tárolómotort helyi SSD-k támasztják alá, míg az analitikus tároló egy olcsó, fürtön kívüli SSD-tárolón van tárolva. Az alábbi táblázat a tranzakciós és az analitikai tároló közötti jelentős különbségeket rögzíti.


|Szolgáltatás  |Tranzakciós tárolás  |Analitikai tárolás |
|---------|---------|---------|
|Maximális tárhely egy Azure Cosmos-tárolóban |   Korlátlan      |    Korlátlan     |
|Maximális tárhely logikai partíciókulcsonként   |   20 GB      |   Korlátlan      |
|Tárolási kódolás  |   Sororientált, belső formátumban.   |   Oszlop-orientált, Apache Parketta formátumban. |
|Tároló helysége |   Helyi/fürtön belüli SSD-k által támogatott replikált tároló. |  Replikált tároló, amelyet olcsó távoli/fürtön kívüli SSD-k támogatnak.       |
|Tartósság  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|Az adatokhoz hozzáférő API-k  |   SQL, MongoDB, Cassandra, Gremlin, Táblázatok, és etcd.       | Apache Spark         |
|Megőrzés (Idő az élethez vagy A TTL)   |  Szabályzat alapú, konfigurálva az Azure Cosmos-tároló a `DefaultTimeToLive` tulajdonság használatával.       |   Szabályzat alapú, konfigurálva az Azure Cosmos-tároló a `ColumnStoreTimeToLive` tulajdonság használatával.      |
|Ár GB-onként    |   Az [árképzési oldal](https://azure.microsoft.com/pricing/details/cosmos-db/) megtekintése     |   Az [árképzési oldal](https://azure.microsoft.com/pricing/details/cosmos-db/) megtekintése        |
|Tárolási tranzakciók ára    |  Az [árképzési oldal](https://azure.microsoft.com/pricing/details/cosmos-db/) megtekintése         |   Az [árképzési oldal](https://azure.microsoft.com/pricing/details/cosmos-db/) megtekintése        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>A tranzakciós és analitikai tárolás előnyei

### <a name="no-etl-operations"></a>Nincs ETL-művelet

A hagyományos analitikus folyamatok összetettek, több szakaszban minden igénylő Kivonat-átalakítási-load (ETL) műveletek etl és a számítási és tárolási szintek. Összetett adatfeldolgozási architektúrákat eredményez. Ami azt jelenti, magas költségek több szakaszában a tárolás és a számítási, és a nagy késés miatt nagy mennyiségű adatok átvitele a különböző tárolási és számítási szakaszok között.  

Az Azure Cosmos DB-vel nem hajt sújt a TL-műveletek végrehajtása. Minden Azure Cosmos-tároló tegyét tranzakciós és analitikus tárolómotorok, és a tranzakciós és analitikus tárolómotor közötti adatátvitel történik az adatbázis-motor, és anélkül, hogy a hálózati ugrások. Az így létrejövő késés és költség jelentősen alacsonyabb, mint a hagyományos analitikai megoldások. És kap egy globálisan elosztott tárolási rendszer mind a tranzakciós és analitikus számítási feladatokhoz.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Több verzió tárolása, frissítés és lekérdezés az analitikus tároló

Az analitikus tároló teljes mértékben frissíthető, és az Azure Cosmos-tárolóban történt összes tranzakciós frissítés teljes verzióelőzményeit tartalmazza.

A tranzakciós tároló bármely frissítése garantáltan 30 másodpercen belül látható lesz az analitikai tároló számára. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globálisan elosztott, többfős analitikai tárolás

Ha az Azure Cosmos-fiók hatóköre egyetlen régióban van, a tárolókban tárolt adatok (tranzakciós és analitikus tárolóban) is egyetlen régióra lesznek hatóköre. Másrészt, ha az Azure Cosmos-fiók globálisan elosztott, a tárolókban tárolt adatok is globálisan elosztott.

A több írási régióval konfigurált Azure Cosmos-fiókok írási műveletek a tárolóba (mind a tranzakciós, mind az analitikus tárolóba) mindig a helyi régióban történik, és így azok gyorsak.

Egy- vagy többrégiós Azure Cosmos-fiókok esetén, függetlenül attól, hogy egyírási régió (egy főkiszolgáló) vagy több írási régió (más néven többfőkiszolgálós) tranzakciós és analitikus olvasási/lekérdezési helyileg történik az adott régióban.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Teljesítményelkülönítés a tranzakciós és az analitikus munkaterhelések között

Egy adott régióban a tranzakciós számítási feladatok a tároló tranzakciós/sortárolója ellen működnek. Másrészt az analitikus számítási feladatok a tároló analitikai/oszloptárolójával szemben működnek. A két tárolómotor egymástól függetlenül működik, és szigorú teljesítmény-elkülönítést biztosít a munkaterhelések között.

A tranzakciós számítási feladatok a kiosztott átviteli -átbocsátás (RT). A tranzakciós számítási feladatoktól eltérően az analitikus számítási feladatok átviteli igénye a tényleges felhasználáson alapul. Az analitikus munkaterhelések igény szerint használnak erőforrásokat.

## <a name="next-steps"></a>További lépések

* [Ideje élni az Azure Cosmos DB](time-to-live.md)
