---
title: Tarifacsomagok – Azure Database for MariaDB
description: Ismerje meg az Azure Database mariadb-hez való különböző tarifacsomagjait, beleértve a számítási generációkat, a tárolási típusokat, a tárhelyméretet, a virtuális magokat, a memóriát és a biztonsági mentés megőrzési időszakait.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 39af6850810fa471003cea27ed274972fb2ff046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528030"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure-adatbázis a MariaDB-díjszabási szintekhez

Létrehozhat egy Azure Database for MariaDB-kiszolgálót a három különböző tarifacsomag egyikében: alapszintű, általános célú és memóriaoptimalizált. A tarifacsomagok a kiépíthető virtuális magok, a virtuális magonkénti memória és az adatok tárolására használt tárolási technológia által differenciált számítási mennyiség alapján vannak megkülönböztetve. Minden erőforrás a MariaDB kiszolgáló szintjén van kiépítve. A kiszolgálónak egy vagy több adatbázisa lehet.

|    | **Basic** | **Általános cél** | **Memória optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Számításgenerálás | Gen 5 (1998. |Gen 5 (1998. | Gen 5 (1998. |
| virtuális magok | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória virtuális magonként | 2 GB | 5 GB | 10 GB |
| Tárhely mérete | 5 GB és 1 TB között | 5 GB és 4 TB között | 5 GB és 4 TB között |
| Adatbázis biztonsági mentési megőrzési időszaka | 7-35 nap | 7-35 nap | 7-35 nap |

Tarifacsomag kiválasztásához használja az alábbi táblázatot kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Basic | Könnyű számítási és I/O-teljesítményt igénylő munkaterhelések. Ilyenek például a fejlesztéshez vagy teszteléshez használt kiszolgálók, vagy a kis méretű, ritkán használt alkalmazások. |
| Általános célú | A legtöbb üzleti számítási feladatok, amelyek megkövetelik a kiegyensúlyozott számítási és memória méretezhető I/O átviteli. Ilyenek például a web- és mobilalkalmazások, valamint más vállalati alkalmazások üzemeltetésére szolgáló kiszolgálók.|
| Memóriaoptimalizált | Nagy teljesítményű adatbázis-munkaterhelések, amelyek a gyorsabb tranzakciófeldolgozás és a nagyobb egyidejűség érdekében memórián belüli teljesítményt igényelnek. Ilyenek például a valós idejű adatok feldolgozására szolgáló kiszolgálók és a nagy teljesítményű tranzakciós vagy analitikus alkalmazások.|

A kiszolgáló létrehozása után a virtuális magok száma és a tarifacsomag (kivéve az alapszintű és az alapszintű) másodpercek alatt módosítható fel- vagy le. Emellett önállóan módosíthatja a tárhely et és a biztonsági mentés megőrzési időszakot fel- vagy leközben, alkalmazásállás nélkül. A kiszolgáló létrehozása után nem módosíthatja a biztonsági másolat tárolási típusát. További információt az [Erőforrások méretezése](#scale-resources) című szakaszban talál.

## <a name="compute-generations-and-vcores"></a>Számítási generációk és virtuális magok

A számítási erőforrások virtuális magként vannak megadva, amelyek az alapul szolgáló hardver logikai processzorát képviselik. A Gen 5 logikai processzorok Intel E5-2673 v4 (Broadwell) 2,3 GHz-es processzorokon alapulnak.

## <a name="storage"></a>Storage

A kiépített tárterület az Azure Database for MariaDB-kiszolgáló számára rendelkezésre álló tárkapacitás. A tároló az adatbázisfájlokhoz, az ideiglenes fájlokhoz, a tranzakciónaplókhoz és a MariaDB kiszolgáló naplóihoz használatos. A kiépített tárterület teljes mennyisége meghatározza a kiszolgáló számára elérhető I/O-kapacitást is.

|    | **Basic** | **Általános cél** | **Memória optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Egyszerű tárhely | Általános célú tárolás | Általános célú tárolás |
| Tárhely mérete | 5 GB és 1 TB között | 5 GB és 4 TB között | 5 GB és 4 TB között |
| Tárolási növekményméret | 1 GB | 1 GB | 1 GB |
| IOPS | Változó |3 IOPS/GB<br/>Min. 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Max 6000 IOPS |

A kiszolgáló létrehozása során és után további tárolókapacitást adhat hozzá, és lehetővé teheti, hogy a rendszer automatikusan bővítse a tárhelyet a számítási feladatok tárolási felhasználása alapján.

>[!NOTE]
> A tárhely csak felskálázható, nem csökkenthető.

Az alapszintű szint nem biztosít IOPS-garanciát. Az általános cél és a memória optimalizált tarifacsomagok, az IOPS-skálázás a kiosztott tárolási mérete 3:1 arányban.

Az I/O-felhasználás figyelheti az Azure Portalon vagy az Azure CLI-parancsok használatával. A figyeléshez szükséges mérőszámok a [tárolási korlát, a tárolási százalék, a használt tárhely és az IO százalék](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>A tárolási korlát elérése

A 100 GB-nál kisebb kiépített tárterülettel rendelkező kiszolgálók írásvédettek, ha az ingyenes tárhely a kiosztott tárméret 5%-ánál kisebb. A 100 GB-nál nagyobb kiépített tárhellyel rendelkező kiszolgálók csak olvashatónak lesznek jelölve, ha az elérhető tárterület kisebb, mint 5 GB.

Ha például 110 GB tárhelyet létesített, és a tényleges kihasználtság meghaladja a 105 GB-ot, a kiszolgáló írásvédettként van megjelölve. Másik lehetőségként, ha 5 GB tárhelyet létesített, a kiszolgáló írásvédettként lesz megjelölve, ha az ingyenes tárhely eléri a 256 MB-nál kisebb.

Mialatt a szolgáltatás csak olvashatóvá próbálja tenni a kiszolgálót, minden új írási tranzakció kérését blokkolja a rendszer, és a meglévő aktív tranzakciók végrehajtása folytatódik. A kiszolgáló csak olvashatóként való beállításakor minden későbbi írási művelet és tranzakció meghiúsul. Az olvasási lekérdezések továbbra is zavartalanul működnek. A kiépített tárterület növelése után a kiszolgáló ismét készen fog állni az írási tranzakciók elfogadására.

Azt javasoljuk, hogy kapcsolja be a tárolás automatikus növekedését, vagy állítson be egy riasztást, amely értesíti, ha a kiszolgáló tárhelye megközelíti a küszöbértéket, így elkerülheti az írásvédett állapotba való bejutást. További információt a riasztás [beállításáról](howto-alert-metric.md)szóló dokumentációban talál.

### <a name="storage-auto-grow"></a>A tárolás automatikus annekta

Az automatikus tárhelynövekedés megakadályozza, hogy a kiszolgáló kifogyjon a tárhelyből, és csak olvashatóvá váljon. Ha a tároló automatikus növekedése engedélyezve van, a tároló automatikusan növekszik anélkül, hogy befolyásolna a számítási feladatokat. A 100 GB-nál kisebb kiépített tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-tal nő, ha az ingyenes tárterület a kiosztott tár 10%-a alatt van. A 100 GB-nál több kiépített tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület 5%-kal nő, ha a szabad tárhely a kiosztott tárterület 10 GB-ja alatt van. A fent meghatározott maximális tárolási korlátok érvényesek.

Ha például 1000 GB tárhelyet létesített, és a tényleges kihasználtság meghaladja a 990 GB-ot, a kiszolgáló tárhelymérete 1050 GB-ra nő. Másik lehetőségként, ha 10 GB tárhelyet létesített, a tárterület mérete 15 GB-ra nő, ha kevesebb, mint 1 GB tárterület ingyenes.

Ne feledje, hogy a tároló csak felskálázható, nem lefelé.

## <a name="backup"></a>Backup

A szolgáltatás automatikusan biztonsági mentést készít a kiszolgálóról. 7 és 35 nap közötti megőrzési időszakot választhat. Az általános célú és memóriaoptimalizált kiszolgálók dönthetnek úgy, hogy georedundáns tárolással rendelkeznek a biztonsági mentések számára. További információ a biztonsági mentésekről a [fogalmakról szóló cikkben.](concepts-backup.md)

## <a name="scale-resources"></a>Erőforrások skálázása

A kiszolgáló létrehozása után önállóan módosíthatja a virtuális magokat, a tarifacsomagot (kivéve az alapszintű, a tárterület mennyiségét és a biztonsági mentés megőrzési időszakát. A kiszolgáló létrehozása után nem módosíthatja a biztonsági másolat tárolási típusát. A virtuális magok száma fel- vagy leskálázható. A biztonsági mentés megőrzési időszak a 7-35 nap fel- vagy leskálázható. A tároló mérete csak növelhető. Az erőforrások méretezése a portálon vagy az Azure CLI-n keresztül végezhető el. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Ha módosítja a virtuális magok számát, vagy a tarifacsomagot, az új számítási lefoglalással létrejön az eredeti kiszolgáló másolata. Amint a kiszolgáló üzemel, a kapcsolatok átállnak az új kiszolgálóra. Az új kiszolgálóra való váltás pillanatában nem hozható létre új kapcsolat, és a nem véglegesített tranzakciók vissza lesznek állítva. Ez az időtartam változó, de a legtöbb esetben egy percnél kevesebb időt vesz igénybe.

A tárhely méretezése és a biztonsági mentési megőrzési időszak módosítása valódi online műveletek. Nincs állásidő, és az alkalmazás nem érinti. Ahogy az IOPS a kiosztott tárterület méretével skálázódik, növelheti a kiszolgáló számára elérhető IOPS-t a tárterület bővítésével.

## <a name="pricing"></a>Díjszabás

A legfrissebb árképzési információkat a szolgáltatás [díjszabási oldalán](https://azure.microsoft.com/pricing/details/mariadb/)találja. A kívánt konfiguráció költségének megtekintéséhez az [Azure Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) a **csomagdíj** lapon jeleníti meg a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure díjkalkulátor használatával lehívhatja a becsült árat. Az [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **Elemek hozzáadása**lehetőséget, bontsa ki az **Adatbázisok kategóriát,** és válassza az **Azure Database for MariaDB** lehetőséget a beállítások testreszabásához.

## <a name="next-steps"></a>További lépések
- További információ a [szolgáltatás korlátairól.](concepts-limits.md)
- Ismerje meg, hogyan [hozhat létre MariaDB-kiszolgálót az Azure Portalon.](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
