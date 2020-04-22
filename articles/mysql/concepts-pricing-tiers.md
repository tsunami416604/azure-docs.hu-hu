---
title: Tarifacsomagok – Azure Database for MySQL
description: Ismerje meg az Azure Database for MySQL különböző tarifacsomagjait, beleértve a számítási generációkat, a tárolási típusokat, a tárhelyméretet, a virtuális magokat, a memóriát és a biztonsági mentés megőrzési időszakait.
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 0123436eab2cdfa91066a2bd0652e16896ee838a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767850"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database a MySQL-díjszabási szintekhez

Létrehozhat egy Azure Database for MySQL-kiszolgálót a három különböző tarifacsomag egyikében: Alapszintű, általános célú és memóriaoptimalizált. A tarifacsomagok a kiépíthető virtuális magok, a virtuális magonkénti memória és az adatok tárolására használt tárolási technológia által differenciált számítási mennyiség alapján vannak megkülönböztetve. Minden erőforrás a MySQL-kiszolgáló szintjén van kiépítve. A kiszolgálónak egy vagy több adatbázisa lehet.

|    | **Basic** | **Általános cél** | **Memória optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Számításgenerálás | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 (1998. |
| virtuális magok | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória virtuális magonként | 2 GB | 5 GB | 10 GB |
| Tárhely mérete | 5 GB és 1 TB között | 5 GB és 16 TB között | 5 GB és 16 TB között |
| Adatbázis biztonsági mentési megőrzési időszaka | 7-35 nap | 7-35 nap | 7-35 nap |

Tarifacsomag kiválasztásához használja az alábbi táblázatot kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Basic | Könnyű számítási és I/O-teljesítményt igénylő munkaterhelések. Ilyenek például a fejlesztéshez vagy teszteléshez használt kiszolgálók, vagy a kis méretű, ritkán használt alkalmazások. |
| Általános célú | A legtöbb üzleti számítási feladatok, amelyek megkövetelik a kiegyensúlyozott számítási és memória méretezhető I/O átviteli. Ilyenek például a web- és mobilalkalmazások, valamint más vállalati alkalmazások üzemeltetésére szolgáló kiszolgálók.|
| Memóriaoptimalizált | Nagy teljesítményű adatbázis-munkaterhelések, amelyek a gyorsabb tranzakciófeldolgozás és a nagyobb egyidejűség érdekében memórián belüli teljesítményt igényelnek. Ilyenek például a valós idejű adatok feldolgozására szolgáló kiszolgálók és a nagy teljesítményű tranzakciós vagy analitikus alkalmazások.|

A kiszolgáló létrehozása után a virtuális magok, a hardvergenerálás és a tarifacsomag száma (kivéve az alapszintű és az alapszintű) másodpercek alatt módosítható. Emellett önállóan módosíthatja a tárhely et és a biztonsági mentés megőrzési időszakot fel- vagy leközben, alkalmazásállás nélkül. A kiszolgáló létrehozása után nem módosíthatja a biztonsági másolat tárolási típusát. További információt az [Erőforrások méretezése](#scale-resources) című szakaszban talál.

## <a name="compute-generations-and-vcores"></a>Számítási generációk és virtuális magok

A számítási erőforrások virtuális magként vannak megadva, amelyek az alapul szolgáló hardver logikai processzorát képviselik. Kína Kelet 1, Kína Északi 1, MINKET DoD Központi, és MINKET DoD Kelet hasznosít Gen 4 logikus CPU amit van azon alapszik Intel E5-2673 v3 (Haswell) 2.4-GHz folyamat. Minden más régió az Intel E5-2673 v4 (Broadwell) 2,3 GHz-es processzorokon alapuló Gen 5 logikai processzorokat használja.

## <a name="storage"></a>Storage

A kiépített tárterület az Azure Database for MySQL-kiszolgáló számára rendelkezésre álló tárkapacitása. A tároló az adatbázisfájlokhoz, az ideiglenes fájlokhoz, a tranzakciónaplókhoz és a MySQL kiszolgálónaplóihoz használatos. A kiépített tárterület teljes mennyisége meghatározza a kiszolgáló számára elérhető I/O-kapacitást is.

|    | **Basic** | **Általános cél** | **Memória optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Egyszerű tárhely | Általános célú tárolás | Általános célú tárolás |
| Tárhely mérete | 5 GB és 1 TB között | 5 GB és 16 TB között | 5 GB és 16 TB között |
| Tárolási növekményméret | 1 GB | 1 GB | 1 GB |
| IOPS | Változó |3 IOPS/GB<br/>Min. 100 IOPS<br/>Max 20 000 IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Max 20 000 IOPS |

> [!NOTE]
> A 16 TB-os és 20 000 IOPS-os tárhely a következő régiókban támogatott: USA keleti régiója, USA keleti régiója 2, USA középső régiója, USA északi régiója, USA középső régiója, Észak-Európa, Nyugat-Európa, Az Egyesült Királyság déli része, az Egyesült Királyság nyugati, délkelet-ázsiai, kelet-ázsiai, japán kelet-, nyugat-ázsiai, közép-, dél-koreai, dél-amerikai, délkeletbeli egyesült királyságbeli régiója.
>
> Minden más régió legfeljebb 4 TB tárhelyet és akár 6000 IOPS-t támogat.
>

A kiszolgáló létrehozása során és után további tárolókapacitást adhat hozzá, és lehetővé teheti, hogy a rendszer automatikusan bővítse a tárhelyet a számítási feladatok tárolási felhasználása alapján. 

>[!NOTE]
> A tárhely csak felskálázható, nem csökkenthető.

Az alapszintű szint nem biztosít IOPS-garanciát. Az általános cél és a memória optimalizált tarifacsomagok, az IOPS-skálázás a kiosztott tárolási mérete 3:1 arányban.

Az I/O-felhasználás figyelheti az Azure Portalon vagy az Azure CLI-parancsok használatával. A figyeléshez szükséges mérőszámok a [tárolási korlát, a tárolási százalék, a használt tárhely és az IO százalék](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>A tárolási korlát elérése

A 100 GB-nál kisebb kiépített tárterülettel rendelkező kiszolgálók írásvédettek, ha az ingyenes tárhely a kiosztott tárméret 5%-ánál kisebb. A 100 GB-nál nagyobb kiépített tárhellyel rendelkező kiszolgálók csak olvashatónak lesznek jelölve, ha az elérhető tárterület kisebb, mint 5 GB.

Ha például 110 GB tárhelyet létesített, és a tényleges kihasználtság meghaladja a 105 GB-ot, a kiszolgáló írásvédettként van megjelölve. Másik lehetőségként, ha 5 GB tárhelyet létesített, a kiszolgáló írásvédettként lesz megjelölve, ha az ingyenes tárhely eléri a 256 MB-nál kisebb.

Mialatt a szolgáltatás csak olvashatóvá próbálja tenni a kiszolgálót, minden új írási tranzakció kérését blokkolja a rendszer, és a meglévő aktív tranzakciók végrehajtása folytatódik. A kiszolgáló csak olvashatóként való beállításakor minden későbbi írási művelet és tranzakció meghiúsul. Az olvasási lekérdezések továbbra is zavartalanul működnek. A kiépített tárterület növelése után a kiszolgáló ismét készen fog állni az írási tranzakciók elfogadására.

Azt javasoljuk, hogy kapcsolja be a tárolás automatikus növekedését, vagy állítson be egy riasztást, amely értesíti, ha a kiszolgáló tárhelye megközelíti a küszöbértéket, így elkerülheti az írásvédett állapotba való bejutást. További információt a riasztás [beállításáról](howto-alert-on-metric.md)szóló dokumentációban talál.

### <a name="storage-auto-grow"></a>A tárolás automatikus annekta

Az automatikus tárhelynövekedés megakadályozza, hogy a kiszolgáló kifogyjon a tárhelyből, és csak olvashatóvá váljon. Ha a tároló automatikus növekedése engedélyezve van, a tároló automatikusan növekszik anélkül, hogy befolyásolna a számítási feladatokat. A 100 GB-nál kisebb kiépített tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-tal nő, ha az ingyenes tárterület a kiosztott tár 10%-a alatt van. A 100 GB-nál több kiépített tárhellyel rendelkező kiszolgálók esetében a kiosztott tárterület 5%-kal nő, ha a szabad tárhely a kiosztott tárterület 10 GB-ja alatt van. A fent meghatározott maximális tárolási korlátok érvényesek.

Ha például 1000 GB tárhelyet létesített, és a tényleges kihasználtság meghaladja a 990 GB-ot, a kiszolgáló tárhelymérete 1050 GB-ra nő. Másik lehetőségként, ha 10 GB tárhelyet létesített, a tárterület mérete 15 GB-ra nő, ha kevesebb, mint 1 GB tárterület ingyenes.

Ne feledje, hogy a tároló csak felskálázható, nem lefelé.

## <a name="backup"></a>Backup

A szolgáltatás automatikusan biztonsági mentést készít a kiszolgálóról. 7 és 35 nap közötti megőrzési időszakot választhat. Az általános célú és memóriaoptimalizált kiszolgálók dönthetnek úgy, hogy georedundáns tárolással rendelkeznek a biztonsági mentések számára. További információ a biztonsági mentésekről a [fogalmakról szóló cikkben.](concepts-backup.md)

## <a name="scale-resources"></a>Erőforrások skálázása

A kiszolgáló létrehozása után egymástól függetlenül módosíthatja a virtuális magokat, a hardvergenerálást, a tarifacsomagot (kivéve az alapszintű kiszolgálót és az alapterületről), a tárterület mennyiségét és a biztonsági mentés megőrzési időszakát. A kiszolgáló létrehozása után nem módosíthatja a biztonsági másolat tárolási típusát. A virtuális magok száma fel- vagy leskálázható. A biztonsági mentés megőrzési időszak a 7-35 nap fel- vagy leskálázható. A tároló mérete csak növelhető. Az erőforrások méretezése a portálon vagy az Azure CLI-n keresztül végezhető el. Az Azure CLI használatával történő méretezésről a [Monitor and Scale a Azure Database for MySQL-kiszolgáló](scripts/sample-scale-server.md)az Azure CLI használatával című témakörben található.

Ha módosítja a virtuális magok számát, a hardvergenerálást vagy a tarifacsomagot, az új számítási foglalással létrejön az eredeti kiszolgáló másolata. Amint a kiszolgáló üzemel, a kapcsolatok átállnak az új kiszolgálóra. Az új kiszolgálóra való váltás pillanatában nem hozható létre új kapcsolat, és a nem véglegesített tranzakciók vissza lesznek állítva. Ez az időtartam változó, de a legtöbb esetben egy percnél kevesebb időt vesz igénybe.

A tárhely méretezése és a biztonsági mentési megőrzési időszak módosítása valódi online műveletek. Nincs állásidő, és az alkalmazás nem érinti. Ahogy az IOPS a kiosztott tárterület méretével skálázódik, növelheti a kiszolgáló számára elérhető IOPS-t a tárterület bővítésével.

## <a name="pricing"></a>Díjszabás

A legfrissebb árképzési információkat a szolgáltatás [díjszabási oldalán](https://azure.microsoft.com/pricing/details/mysql/)találja. A kívánt konfiguráció költségének megtekintéséhez az [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) a **csomagdíj** lapon jeleníti meg a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure díjkalkulátor használatával lehívhatja a becsült árat. Az [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **Elemek hozzáadása**lehetőséget, bontsa ki az **Adatbázisok kategóriát,** és válassza az **Azure Database for MySQL** lehetőséget a beállítások testreszabásához.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre MySQL szervert a portálon](howto-create-manage-server-portal.md).
- További információ a [szolgáltatási korlátokról.](concepts-limits.md)
- További információ az [olvasási replikákkal való horizontális felskálázásról.](howto-read-replicas-portal.md)
