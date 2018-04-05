---
title: Tarifacsomagok MySQL az Azure-adatbázis
description: Ez a cikk ismerteti a tarifacsomagok MySQL az Azure-adatbázis.
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: c9a74aa00ee263b8fb4e19b77ad5be418e31c7d6
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure tarifacsomagok MySQL-adatbázis

Létrehozhat egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis az egyik három különböző árképzési szinteket: alapvető, általános célú és Memóriaoptimalizált. A tarifacsomagok a számítási, amelyek létesíthetők vCores, vCore memória és az adatok tárolására használt tárolótechnológián szerint megkülönböztetett forgalomosztályból. Minden erőforrás törlődnek MySQL kiszolgálói szinten. Egy egy vagy több adatbázist is van.

|    | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Számítási létrehozása | 4, 5. generációból generációs | 4, 5. generációból generációs | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Memória mennyisége vCore | Alapkonfiguráció | 2 x Basic | Általános célú x 2 |
| Tároló mérete | 5 GB és 1 TB | 5 GB és 2 TB | 5 GB és 2 TB |
| Tárolási típus | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Adatbázis biztonsági mentés megőrzési időtartam | 7-35 nap | 7-35 nap | 7-35 nap |

Válasszon egy tarifacsomagot, használja a következő táblázat kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Alapszintű | Könnyű számítási és adatátviteli teljesítményt igénylő munkaterhelésekhez. Ilyen például a fejlesztési vagy tesztelési használt kiszolgálók, vagy csak kevés számítógépet érintő ritkán használt alkalmazások. |
| Általános rendeltetés | A legtöbb üzleti szolgáltatások, amelyek kiegyensúlyozott számítási és memóriakapacitást méretezhető i/o-átviteli. Például a webes és mobilalkalmazások és más vállalati alkalmazásokat futtató kiszolgálók.|
| Memóriára optimalizált | Gyorsabb tranzakció-feldolgozást és magasabb szintű párhuzamosság memórián belüli teljesítményt igénylő nagy teljesítményű adatbázis munkaterhelésekhez. Például a kiszolgálók a valós idejű adatok és nagy teljesítményű tranzakciós vagy analitikai alkalmazások feldolgozásához.|

Miután létrehozott egy kiszolgálót, vCores száma módosítható felfelé vagy lefelé másodpercen belül. Is egymástól függetlenül beállíthatja másolatot tároló és a biztonsági mentés megőrzési időszak felfelé vagy lefelé alkalmazás állásidő nélkül. További információkért lásd: "Válik erőforrások" szakasz.

## <a name="compute-generations-vcores-and-memory"></a>Számítási generációja, vCores és memória

A számítási erőforrások vannak megadva, a vCores, amelyek tartalmazzák az alapul szolgáló hardverben, a logikai Processzor. Jelenleg két számítási generációja, generációs 4. és 5. generációból közül választhat. 4 logikai processzorok Intel E5-2673 v3 alapulnak. generációból (Haswell) 2.4-GHz-es processzor. 5 logikai processzorok Intel E5-2673 v4 alapulnak. generációból (Broadwell) 2.3-GHz-es processzor. 4. generációból és generációs 5 (az "X" jelöli elérhető), a következő régiókban érhetők el. 

| **Az Azure-régió** | **4. generációból** | **5. generációból** |
|:---|:----------:|:--------------------:|
| USA középső régiója |  | X |
| USA keleti régiója | X | X |
| USA 2. keleti régiója | X | X |
| USA északi középső régiója | X |  |
| USA déli középső régiója | X | X |
| USA nyugati régiója | X | X |
| USA nyugati régiója, 2. |  | X |
| Közép-Kanada | X | X |
| Kelet-Kanada | X | X |
| Dél-Brazília | X |  |
| Észak-Európa | X | X |
| Nyugat-Európa | X | X |
| Az Egyesült Királyság nyugati régiója |  | X |
| Az Egyesült Királyság déli régiója |  | X |
| Kelet-Ázsia | X |  |
| Délkelet-Ázsia | X |  |
| Kelet-Ausztrália |  | X |
| Közép-India | X |  |
| Nyugat-India | X |  |
| Kelet-Japán | X | X |
| Nyugat-Japán | X | X |
| Korea déli régiója |  | X |

Attól függően, hogy ez a tarifacsomag minden vCore ki van építve a meghatározott RAM mennyiséggel. Növeléséhez vagy csökkentéséhez tegye a következőket vCores a kiszolgáló, a memória növeli vagy csökkenti arányosan. Az általános célú réteg biztosít kettős az alapszintű rétegben képest vCore memória mennyisége. A Memóriaoptimalizált réteget biztosít duplán az általános célú réteghez képest memória mennyisége.

## <a name="storage"></a>Tárolás

A tároló, kiépítése nem MySQL-kiszolgáló az Azure-adatbázishoz rendelkezésre álló tárolókapacitás mennyiségét. A tárolót használja a rendszer az adatbázisfájlokat, az ideiglenes fájlok, a tranzakciós naplók és a a MySQL-kiszolgáló naplói. A teljes rendszermemóriához viszonyítva kiépítése tárolási-kiszolgálóhoz is határozza meg a rendelkezésre álló i/o-kapacitás.

|    | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Tároló mérete | 5 GB és 1 TB | 5 GB és 2 TB | 5 GB és 2 TB |
| Tárolási növekmény mérete | 1 GB | 1 GB | 1 GB |
| IO | Változó |3 IOPS/GB<br/>100 minimális IOPS | 3 IOPS/GB<br/>100 minimális IOPS |

Hozzáadhat további tárolási kapacitás alatt és után a kiszolgáló létrehozása. Az alapszintű rétegben nem biztosít egy IOPS garantált. Az általános célú és Memóriaoptimalizált tarifacsomagok az IOPS méretezést 3:1 arányt a kiépített méretét.

Az i/o-felhasználás az Azure portálon vagy az Azure CLI-parancsok segítségével figyelheti. Figyelésére vonatkozó adatok gyűjtése le van [tárolási kapacitása, tárolási százalékos, használt tárolási és IO százalék](concepts-monitoring.md).

## <a name="backup"></a>Biztonsági mentés

A szolgáltatás automatikusan felveszi a kiszolgáló biztonsági másolatait. Biztonsági mentések minimális megőrzési időtartama hét nap. Megadhat egy legfeljebb 35 napos megőrzési idővel. A megőrzési bármikor módosítható a kiszolgáló élettartama során. Helyileg redundáns és georedundáns biztonsági mentések közül választhat. Georedundáns biztonsági mentéseket is tárolódnak a [párosítása, földrajzi régió](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) a régió, ahol a kiszolgáló akkor jön létre. A redundancia katasztrófa esetén védelmet biztosít. Ezenkívül igénybe veheti a kiszolgáló tárolt bármely más Azure-régió, ahol a szolgáltatás érhető georedundáns biztonsági való. Már nem módosíthatja, miután a kiszolgáló akkor jön létre két biztonsági másolatok tárolási lehetőségek közötti.

## <a name="scale-resources"></a>Erőforrások skálázása

Miután létrehozta a kiszolgáló, a vCores tárolókapacitást és a biztonsági mentés megőrzési időszak egymástól függetlenül módosíthatja. Az árképzési szint vagy a biztonsági másolatok tárolási típus nem módosítható, miután a kiszolgáló akkor jön létre. vCores és a biztonsági mentés megőrzési időszak felfelé vagy lefelé méretezhetők. A tárhely méretét pedig csak növelni. Az erőforrások skálázás végezhető vagy a portál vagy az Azure parancssori felület használatával. Az Azure parancssori felület használatával skálázás példáért lásd: [figyelő és a skála egy Azure parancssori felület használatával MySQL-kiszolgálóhoz tartozó Azure-adatbázis](scripts/sample-scale-server.md).

Ha megváltoztatja a vCores számát, az eredeti kiszolgáló hozza létre az új számítási felosztás. Miután az új kiszolgálón fut, kapcsolatok bekapcsolt állapotban az új kiszolgálóra. Során, amikor a rendszer való működésre vált az új kiszolgáló jelenleg nincs új kapcsolatok hozhatók létre, és minden nem véglegesített tranzakciót visszaállít a rendszer. Ebben az ablakban platformonként változó, de a legtöbb esetben egy percen belül.

Tárolási méretezés és a biztonsági másolatok megőrzésének időszakának módosításával olyan online true műveletekkel. Nincs állásidő nélkül, és az alkalmazás nincs hatással. IOPS méretezést a kiépített tárterület mérete, mert tárolási vertikális felskálázásával növelheti az IOPS érhető el a kiszolgáló.

## <a name="pricing"></a>Díjszabás

A legfrissebb információkat, lásd: a szolgáltatás [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/mysql/). A költség, a konfiguráció azt szeretné, hogy a [Azure-portálon](https://portal.azure.com/#create/Microsoft.MySQLServer) havi költségét jeleníti meg a **tarifacsomag** lapon a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure árképzési Számológép segítségével egy becsült ár. A a [Azure árképzési Számológép](https://azure.microsoft.com/pricing/calculator/) webhelyen, jelölje be **elemek hozzáadása**, bontsa ki a **adatbázisok** kategória, és válassza a **MySQLazAzure-adatbázis**a beállítások testreszabása.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [MySQL-kiszolgáló létrehozása a portálon](howto-create-manage-server-portal.md).
- Megtudhatja, hogyan [figyelése és az Azure parancssori felület használatával egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis méretezése](scripts/sample-scale-server.md).
- További tudnivalók a [korlátozások szolgáltatás](concepts-limits.md).
