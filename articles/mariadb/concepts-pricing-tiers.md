---
title: Az Azure Database for MariaDB tarifacsomagok
description: Ez a cikk ismerteti a tarifacsomagok MariaDB-hez készült Azure Database-hez.
author: jan-eng
ms.author: janeng
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 92db6442352242d5c7f25d39442d208d6007621b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984340"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB tarifacsomagok

Létrehozhat egy Azure Database for MariaDB-kiszolgáló különböző három tarifacsomag közül: alapszintű, általános célú és memóriahasználatra optimalizált. A tarifacsomag, amelyek kioszthatóak virtuális magok, memória / virtuális mag és a tárolási technológiát, az adatok tárolására használt számítási mennyisége alapján különbözteti meg. Az összes erőforrások kiépítése a MariaDB kiszolgálói szinten. A kiszolgáló egy vagy több adatbázissal rendelkezhet.

|    | **Basic** | **Általános célú** | **Memóriahasználatra optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Számítási generáció | <!--Gen 4,--> A gen 5 |<!--Gen 4,-->A gen 5 |<!--Gen 4,--> A gen 5 |
| Virtuális magok | 1, 2 | 2, 4, 8, 16 és 32 |2, 4, 8, 16 |
| Memória / virtuális mag | 2 GB | 5 GB | 10 GB |
| Tároló mérete | 5 GB – 1 TB | 5 GB-tól 4 TB-ig | 5 GB-tól 4 TB-ig |
| Tárolási típus | Az Azure standard szintű Storage | Azure Premium Storage | Azure Premium Storage |
| Adatbázis biztonsági másolatainak megőrzési ideje | 7 – 35 nap | 7 – 35 nap | 7 – 35 nap |

Válasszon egy tarifacsomagot, használja az alábbi táblázat kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Alapszintű | Kis számítási és i/o-teljesítményt igénylő munkaterhelések. Ilyenek például fejlesztési vagy tesztelési célokra használt kiszolgálók vagy kisméretű, ritkán használt alkalmazásokhoz. |
| Általános rendeltetés | A legtöbb üzleti számítási feladathoz, amely kiegyensúlyozott számítást és memóriát, valamint méretezhető i/o-teljesítményt igényelnek. Ilyenek például a kiszolgálók üzemeltetésére, webes és mobilalkalmazások és egyéb nagyvállalati alkalmazások.|
| Memóriára optimalizált | Kiemelkedő teljesítményű adatbázisokat igénylő számítási feladatok memórián belüli teljesítmény szükséges a gyorsabb tranzakciófeldolgozás és a magasabb szintű egyidejűség érdekében. Ilyenek például a kiszolgálók feldolgozásához, valós idejű és nagy teljesítményű tranzakciós vagy elemző alkalmazások.|

Miután létrehozott egy kiszolgálót, a virtuális magok és tarifacsomagjának számát (kivéve, és az alapszintű) módosíthatja felfelé vagy lefelé másodpercen belül. Is egymástól függetlenül módosíthatja felfelé storage és a biztonsági másolat megőrzési idejének felfelé vagy lefelé az alkalmazás leállása nélkül. A biztonsági mentési tár írja be a kiszolgáló létrehozása után nem módosítható. További információkért lásd: a [méretezni az erőforrásokat](#scale-resources) szakaszban.

## <a name="compute-generations-and-vcores"></a>Számítási generáció és a virtuális magok

A számítási erőforrások szolgálnak, amelyek jelölik az alapul szolgáló hardver logikai CPU, a virtuális magok.<!--Currently, you can choose from two compute generations, Gen 4 and Gen 5. Gen 4 logical CPUs are based on Intel E5-2673 v3 (Haswell) 2.4-GHz processors.--> Gen 5 logikai CPU k Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorral.

<!--
| **Azure region** | **Gen 5** |
|:---|:----------:|:--------------------:|
| Central US | X | X |
| East US | X | X |
| East US 2 | X | X |
| North Central US | X | X |
| South Central US | X | X |
| West US | X | X |
| West US 2 |  | X |
| Canada Central | X | X |
| Canada East | X | X |
| Brazil South | X | X |
| North Europe | X | X |
| West Europe |  | X |
| France Central |  | X |
| UK West |  | X |
| UK South |  | X |
| East Asia | X | X |
| Southeast Asia | X | X |
| Australia East |  | X |
| Australia Central |  | X |
| Australia Central 2 |  | X |
| Australia Southeast |  | X |
| Central India | X | X |
| West India | X | X |
| South India |  | X |
| Japan East | X | X |
| Japan West | X | X |
| Korea Central |  | X |
| Korea South |  | X |
-->

## <a name="storage"></a>Storage

A tároló üzembe helyezi az érték a tárolási kapacitás érhető el az Azure database for MariaDB-kiszolgáló. A storage szolgál az adatbázisfájlokat, az ideiglenes fájlok, tranzakciós naplókhoz és a MariaDB-kiszolgáló naplóit. Tároló üzembe helyezésekor teljes mennyisége is meghatározza a rendelkezésre álló i/o-kapacitás a kiszolgálóhoz.

|    | **Basic** | **Általános célú** | **Memóriahasználatra optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Az Azure standard szintű Storage | Azure Premium Storage | Azure Premium Storage |
| Tároló mérete | 5 GB – 1 TB | 5 GB-tól 4 TB-ig | 5 GB-tól 4 TB-ig |
| Növekmény tárméret | 1 GB | 1 GB | 1 GB |
| IO | Változó |3 IOPS/GB<br/>Minimum 100 IOPS<br/>Maximális 6000 iops-érték | 3 IOPS/GB<br/>Minimum 100 IOPS<br/>Maximális 6000 iops-érték |

Hozzáadhat további tárolókapacitás alatt és a kiszolgáló létrehozása után. Az alapszintű csomag nem biztosítanak az iops-t. Az általános célú és memóriahasználatra optimalizált tarifacsomagok az iops-t a kiépített tárhely méretére vonatkozik egy 3:1 arányt a skálázás.

Az i/o-használat az Azure Portalon vagy Azure CLI-parancsok használatával követheti nyomon. A releváns metrikákat kíván monitorozni olyan tárhelykorlátozás, tárolási százalékos aránya, felhasznált tárterület és i/o-százalék.
<!--[storage limit, storage percentage, storage used, and IO percent](concepts-monitoring.md)-->

### <a name="reaching-the-storage-limit"></a>Skálázhatósági méretkorlátot

A kiszolgáló van megjelölve csak olvasható, amikor szabad tárterület mérete eléri a kisebb, mint 5 GB-os vagy a felhasznált tárterület 5 %-át, amelyik érték kisebb. Ha például 100 GB tárhelyet kiépítése, és a tényleges használat halad 95 GB, a kiszolgáló van megjelölve, csak olvasható. Azt is megteheti, ha 5 GB adattárolás kiépítése, a kiszolgáló van megjelölve csak olvasható, ha a szabad tárhely eléri a 250 MB-nál kevesebb.  

A szolgáltatás megpróbál, hogy a kiszolgáló írásvédett, amíg minden új írási tranzakció kérés le vannak tiltva, és továbbra is meglévő aktív tranzakciók végrehajtása. Ha a kiszolgáló értékre van állítva, csak olvasható, minden ezt követő írási műveletek és a tranzakciós érvényesítése sikertelen. Olvasási lekérdezések továbbra is zavartalanul működni fog. Miután megnöveli a felhasznált tárterület, a kiszolgáló készen áll majd újra el kell fogadniuk írási tranzakciók.

Azt javasoljuk, hogy beállította egy riasztás arra az esetre, ha a kiszolgáló tárhelyét hamarosan eléri a küszöbértéket a csak olvasható állapotba első elkerülése érdekében. <!--For more information, see the documentation on [how to set up an alert](howto-alert-on-metric.md).-->

## <a name="backup"></a>Backup

A szolgáltatás automatikusan biztonsági másolatokat a kiszolgálóról vesz igénybe. A minimális megőrzési idő a biztonsági másolatok hét napon. Beállíthatja, hogy egy legfeljebb 35 napos megőrzési idővel. A megőrzési bármikor módosítható a kiszolgáló élettartama során. Helyileg redundáns és georedundáns biztonsági mentések közül választhat. Georedundáns biztonsági mentéseket is vannak tárolva a [földrajzilag párosított régióba](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) a régió, ahol a kiszolgáló akkor jön létre. Ezt a redundanciát biztosít védelmet egy esetleges vészhelyzet esetén. Bármely más Azure-régió, amelyben a szolgáltatás georedundáns biztonsági másolat érhető el, a kiszolgáló helyreállítására is kaphat. Nem alkalmas a kiszolgáló létrehozása után két biztonsági másolati tárhely beállításai között módosíthatja.

## <a name="scale-resources"></a>Erőforrások skálázása

Miután létrehozta a kiszolgáló, módosíthatja a virtuális magok, egymástól függetlenül <!--the hardware generation,--> a tarifacsomagot (kivéve a, illetve onnan alapszintű), storage és a biztonsági másolat megőrzési idejének összege. A biztonsági mentési tár írja be a kiszolgáló létrehozása után nem módosítható. A virtuális magok számát felfelé és lefelé skálázhatók. A biztonsági másolat megőrzési idejének méretezhetők felfelé vagy lefelé 7-ről 35 napon belül. A tároló mérete pedig csak növelni. Az erőforrások méretezése elvégezhető a portál vagy az Azure CLI-vel felületén keresztül. <!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Amikor módosítja a virtuális magok, száma <!--the hardware generation,--> vagy a tarifacsomag egy másolatot az eredeti kiszolgáló jön létre az új lefoglalni a számítási erőforrásokat. Miután az új kiszolgáló üzembe helyezéséig, kapcsolatok szálról az új kiszolgálóra. Során a pillanatban, amikor a rendszer átvált az új kiszolgáló új kapcsolat nem hozható létre, és az összes nem véglegesített tranzakció vissza legyen állítva. Ebben az ablakban változik, de a legtöbb esetben kevesebb mint egy percet.

Tároló méretezése és a biztonsági másolat megőrzési idejének módosítása is igaz online. Nem jár, és a folyamatot nem befolyásolja az alkalmazás. IOPS és a felhasznált tárterület mérete logikákat tárolási vertikális felskálázásával növelheti a rendelkezésre álló IOPS a kiszolgáló.

## <a name="pricing"></a>Díjszabás

A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/mariadb/). A költség, a konfiguráció azt szeretné, hogy a [az Azure portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) havi költségét jeleníti meg a **tarifacsomag** lapon a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure díjkalkulátorát segítségével a becsült ár beolvasása. A a [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) webhely, válassza **elemek hozzáadása**, bontsa ki a **adatbázisok** kategória, és válassza **, Azure Database for MariaDB**testre szabhatja beállítások.

## <a name="next-steps"></a>További lépések
- További információ a [szolgáltatás korlátozásai](concepts-limits.md).
- Ismerje meg, hogyan [MariaDB kiszolgáló létrehozása az Azure Portalon](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->