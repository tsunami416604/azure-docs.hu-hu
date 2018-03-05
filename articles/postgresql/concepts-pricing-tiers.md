---
title: "Tarifacsomagok PostgreSQL Azure-adatbázisban"
description: "Ez a cikk ismerteti az Azure-adatbázis árképzési szinteket a PostgreSQL."
services: postgresql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ee75af9b1d7b77799fe02d87da257ff73bc567e6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Azure tarifacsomagok PostgreSQL-adatbázishoz

Egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis három különböző árképzési szinteket - alapvető, általános célú és Memóriaoptimalizált is létrehozható. A tarifacsomagok a számítási, amelyek létesíthetők vCores, vCore memória és az adatok tárolására használt tárolótechnológián szerint megkülönböztetett forgalomosztályból. Minden erőforrás kiépített PostgreSQL kiszolgálói szinten. Egy egy vagy több adatbázist is van.

|    | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Számítási létrehozása | 4, 5. generációból generációs | 4, 5. generációból generációs | 5. generációból |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16, 32 |
| Memória mennyisége vCore | 1x | 2 x Basic | Általános célú x 2 |
| Storage mérete | 5 GB és 1 TB | 5 GB és 1 TB | 5 GB és 1 TB |
| Tárolási típus | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Adatbázis biztonsági mentés megőrzési időtartam | 7 – 35 nap | 7 – 35 nap | 7 – 35 nap |

Tarifacsomag kiválasztása az alábbi táblázat használható kiindulási pontként:

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Alapszintű | Kis számítási és I/O-teljesítményt igénylő számítási feladatok. Ilyenek például a fejlesztői vagy tesztelő kiszolgálók, illetve a kis méretű és ritkán használt alkalmazások. |
| Általános rendeltetés | A legtöbb üzleti számítási feladathoz, amely kiegyensúlyozott számítást és memóriát, valamint méretezhető I/O-teljesítményt igényel. Például a kiszolgáló, webes és mobilalkalmazások és más vállalati alkalmazások üzemeltetésére.|
| Memóriára optimalizált | Nagy teljesítményű adatbázis számítási feladatait a memóriában teljesítmény igénylése a gyorsabb tranzakció-feldolgozási és magasabb szintű párhuzamosság. Például a kiszolgáló a valós idejű adatokat, és nagy teljesítményű tranzakciós vagy analitikai alkalmazások feldolgozásához.|

Miután létrehozott egy kiszolgálót, vCores száma módosítható felfelé vagy lefelé másodpercen belül. Másolatot tároló és a biztonsági mentés megőrzési időszak felfelé vagy lefelé alkalmazás állásidő nélkül függetlenül is módosíthatja. További részletekért alatt méretezési című szakaszában talál.

## <a name="compute-generations-vcores-and-memory"></a>Számítási generációja, vCores és memória

A számítási erőforrások vannak megadva, a vCores, a logikai Processzor, a mögöttes hardver jelző. Jelenleg két számítási generációja, generációs 4. és Gen 5, kínálják választhat. A Gen 4 logikai CPU-k Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorokkal működnek. A Gen 5 logikai CPU-k Intel E5-2673 v4 (Broadwell) 2,3 GHz-es processzorokkal működnek.

Attól függően, hogy ez a tarifacsomag minden vCore ki van építve a meghatározott RAM mennyiséggel. Növeléséhez vagy csökkentéséhez tegye a következőket vCores a kiszolgáló, a memória növeli vagy csökkenti arányosan. Az általános célú réteg biztosít kettős az alapszintű rétegben képest vCore memória mennyisége. A Memóriaoptimalizált réteget biztosít duplán az általános célú réteghez képest memória mennyisége.

## <a name="storage"></a>Tárolás

A tároló, kiépítése nem PostgreSQL-kiszolgáló az Azure-adatbázishoz rendelkezésre álló tárolókapacitás mennyiségét. A tárolót használja a rendszer az adatbázisfájlokat, az ideiglenes fájlok, a tranzakciós naplók és a a PostgreSQL-kiszolgáló naplói. A teljes rendszermemóriához viszonyítva kiépítése tárolási is meghatározza, hogy a rendelkezésre álló i/o-kapacitás-kiszolgálóhoz:

|    | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Storage mérete | 5 GB és 1 TB | 5 GB és 1 TB | 5 GB és 1 TB |
| Tárolási növekmény mérete | 1 GB | 1 GB | 1 GB |
| IO | Változó |3 IOPS/GB<br/>100 minimális IOPS | 3 IOPS/GB<br/>100 minimális IOPS |

További tárolási kapacitás alatt és a kiszolgáló létrehozása után lehet hozzáadni. Az alapszintű rétegben nem biztosít egy IOPS garantált. Az általános célú és Memóriaoptimalizált tarifacsomagok az IOPS méretezést 3:1 arányt a kiépített méretét.

A i/o-felhasználás az Azure-portálon vagy az Azure CLI-parancsok használatával figyelheti. Figyelésére vonatkozó adatok gyűjtése le van [tárolási kapacitása, tárolási százalékos, használt tárolási és IO százalék](concepts-monitoring.md).

## <a name="backup"></a>Biztonsági mentés

A szolgáltatás automatikusan felveszi a kiszolgáló biztonsági másolatait. Biztonsági mentések minimális megőrzési időtartama hét nap. Megadhat egy legfeljebb 35 napos megőrzési idővel. A megőrzési bármikor módosítható a kiszolgáló élettartama során. Helyileg redundáns és georedundáns biztonsági mentések közül választhat. Georedundáns biztonsági másolatai is a [párosítása, földrajzi régió](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) régió a kiszolgáló akkor jön létre a. Ez biztosítja a szintű védelem katasztrófa esetén. Ezenkívül igénybe veheti a kiszolgáló tárolt bármely más Azure-régió, ahol a szolgáltatás érhető georedundáns biztonsági való. Ne feledje, hogy nem módosíthatja, ha a kiszolgáló akkor jön létre két biztonsági másolatok tárolási lehetőségek közötti.

## <a name="scale-resources"></a>Erőforrások skálázása

Miután létrehozta a kiszolgáló, a vCores, tárolás és a biztonsági mentés megőrzési idő mennyiségét egymástól függetlenül módosíthatja. Az árképzési szint vagy a biztonsági másolatok tárolási típus nem módosítható, miután a kiszolgáló akkor jön létre. vCores és a biztonsági mentés megőrzési időszak felfelé vagy lefelé méretezhetők. A tárhely méretét pedig csak növelni. Az erőforrások skálázás végezhető vagy a portál vagy az Azure parancssori felület használatával. Parancssori felület használatával méretezéshez példa található [Itt](scripts/sample-scale-server-up-or-down.md).

Amikor vCores számának módosítása, az eredeti kiszolgáló hozza létre az új számítási felosztás. Ha az új kiszolgálón fut, kapcsolatok bekapcsolt állapotban az új kiszolgálóra. A rövid pillanatban, amikor a rendszer való működésre vált az új kiszolgálóra, során nem új kapcsolatok hozhatók létre, és minden nem véglegesített tranzakciót visszaállít a rendszer. Ebben az ablakban platformonként változó, de a legtöbb esetben egy percen belül.

Tárolási méretezés és a biztonsági másolatok megőrzésének időszakának módosításával olyan online true műveletekkel. Nincs leállás vagy az alkalmazás hatással van. IOPS méretezést a kiépített tárterület mérete, mert tárolási vertikális felskálázásával növelheti az IOPS érhető el a kiszolgáló.

## <a name="pricing"></a>Díjszabás

Tekintse át a szolgáltatás [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/PostgreSQL/) a legfrissebb tartozó díjszabási információkat. Milyen a kívánt konfigurációs költségek, hogy a [Azure-portálon](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) a havi költségét jeleníti meg a **tarifacsomag** lapon kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure árképzési Számológép segítségével egy becsült ár. Látogasson el a [Azure árképzési Számológép](https://azure.microsoft.com/pricing/calculator/) webhely, kattintson a **elemek hozzáadása**, bontsa ki a **adatbázisok** kategóriát, és válassza a **PostgreSQL az Azure-adatbázis**  a beállítások testreszabása.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [PostgreSQL-kiszolgáló létrehozása a portálon](tutorial-design-database-using-azure-portal.md)
- Megtudhatja, hogyan [figyelése és az Azure parancssori felület használatával PostgreSQL kiszolgálóhoz tartozó Azure-adatbázis méretezése](scripts/sample-scale-server-up-or-down.md)
- További tudnivalók a [szolgáltatás korlátozásai](concepts-limits.md)
