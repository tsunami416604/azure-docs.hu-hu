---
title: Az Azure Database for MySQL tarifacsomagok
description: Ez a cikk ismerteti a tarifacsomagok az Azure Database for MySQL-hez.
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 726da603b5f583c85f4b7c4262c6e97f565a3678
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67339932"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database for MySQL-hez tarifacsomagok

Létrehozhat egy Azure Database for MySQL-kiszolgáló a másik három tarifacsomag közül: Alapszintű, általános célú és memóriahasználatra optimalizált. A tarifacsomag, amelyek kioszthatóak virtuális magok, memória / virtuális mag és a tárolási technológiát, az adatok tárolására használt számítási mennyisége alapján különbözteti meg. Az összes erőforrás a MySQL-kiszolgáló szintjén vannak üzembe helyezve. A kiszolgáló egy vagy több adatbázissal rendelkezhet.

|    | **Basic** | **Általános célú** | **Memóriahasználatra optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Számítási generáció | A gen 4, 5 általános | A gen 4, 5 általános | Gen 5 |
| Virtuális magok | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória / virtuális mag | 2 GB | 5 GB | 10 GB |
| Tároló mérete | 5 GB – 1 TB | 5 GB-tól 4 TB-ig | 5 GB-tól 4 TB-ig |
| Tárolási típus | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Adatbázis biztonsági másolatainak megőrzési ideje | 7 – 35 nap | 7 – 35 nap | 7 – 35 nap |

Válasszon egy tarifacsomagot, használja az alábbi táblázat kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Alapszintű | Kis számítási és i/o-teljesítményt igénylő munkaterhelések. Ilyenek például fejlesztési vagy tesztelési célokra használt kiszolgálók vagy kisméretű, ritkán használt alkalmazásokhoz. |
| Általános rendeltetés | A legtöbb üzleti számítási feladathoz, amely kiegyensúlyozott számítást és memóriát, valamint méretezhető i/o-teljesítményt igényelnek. Ilyenek például a kiszolgálók üzemeltetésére, webes és mobilalkalmazások és egyéb nagyvállalati alkalmazások.|
| Memóriára optimalizált | Kiemelkedő teljesítményű adatbázisokat igénylő számítási feladatok memórián belüli teljesítmény szükséges a gyorsabb tranzakciófeldolgozás és a magasabb szintű egyidejűség érdekében. Ilyenek például a kiszolgálók feldolgozásához, valós idejű és nagy teljesítményű tranzakciós vagy elemző alkalmazások.|

Miután létrehozott egy kiszolgálót, a virtuális magok, a hardver generációja és a díjszabás számát réteg (kivéve, és az alapszintű) módosíthatja felfelé vagy lefelé másodpercen belül. Is egymástól függetlenül módosíthatja felfelé storage és a biztonsági másolat megőrzési idejének felfelé vagy lefelé az alkalmazás leállása nélkül. A biztonsági mentési tár írja be a kiszolgáló létrehozása után nem módosítható. További információkért lásd: a [méretezni az erőforrásokat](#scale-resources) szakaszban.

## <a name="compute-generations-and-vcores"></a>Számítási generáció és a virtuális magok

A számítási erőforrások szolgálnak, amelyek jelölik az alapul szolgáló hardver logikai CPU, a virtuális magok. Kína kelet 1, Kína Észak 1, USA védelmi Minisztériuma – középső régiója és USA védelmi Minisztériuma – keleti kihasználhassák a Gen 4 logikai CPU alapuló Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorral. Minden más régiókban kihasználhassák a Gen 5 logikai CPU alapuló Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorral.

## <a name="storage"></a>Storage

A tároló üzembe helyezi az érték a tárolási kapacitás érhető el az Azure database for MySQL-kiszolgálót. A storage szolgál az adatbázisfájlokat, az ideiglenes fájlok, tranzakciós naplókhoz és a MySQL-kiszolgáló naplóit. Tároló üzembe helyezésekor teljes mennyisége is meghatározza a rendelkezésre álló i/o-kapacitás a kiszolgálóhoz.

|    | **Basic** | **Általános célú** | **Memóriahasználatra optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Tároló mérete | 5 GB – 1 TB | 5 GB-tól 4 TB-ig | 5 GB-tól 4 TB-ig |
| Növekmény tárméret | 1 GB | 1 GB | 1 GB |
| IO | Változó |3 IOPS/GB<br/>Minimum 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Minimum 100 IOPS<br/>Max 6000 IOPS |

Alatt és a kiszolgáló létrehozása után adja hozzá a további tárolókapacitás, és lehetővé teszi a rendszer storage tárolófelhasználásának a számítási feladat alapján automatikusan nő. Az alapszintű csomag nem biztosítanak az iops-t. Az általános célú és memóriahasználatra optimalizált tarifacsomagok az iops-t a kiépített tárhely méretére vonatkozik egy 3:1 arányt a skálázás.

Az i/o-használat az Azure Portalon vagy Azure CLI-parancsok használatával követheti nyomon. A releváns metrikákat kíván monitorozni vannak [tárhelykorlátozás, tárolási százalékos aránya, felhasznált tárterület és i/o-százalék](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Nagy storage (előzetes verzió)

A tárfiókok korlátai növeljük az általános célú és memóriahasználatra optimalizált szinten. Az újonnan létrehozott kiszolgálót, hogy vehetnek részt a preview helyezhet üzembe akár 16 TB tárhely. Az iops-t méretezni egy 3:1 arányban legfeljebb 20 000 iops-t. Az aktuális általánosan rendelkezésre álló tárhelyet, az annak a kiszolgálónak a létrehozása után adja hozzá a további tárolókapacitás, és lehetővé teszi a rendszer storage tárolófelhasználásának a számítási feladat alapján automatikusan nő.

|              | **Általános célú** | **Memóriahasználatra optimalizált** |
|:-------------|:--------------------|:---------------------|
| Tárolási típus | Azure Premium Storage | Azure Premium Storage |
| Tároló mérete | 32 GB és 16 TB| 32-16 TB |
| Növekmény tárméret | 1 GB | 1 GB |
| IO | 3 IOPS/GB<br/>Minimum 100 IOPS<br/>Legfeljebb 20 000 iops-érték| 3 IOPS/GB<br/>Minimum 100 IOPS<br/>Legfeljebb 20 000 iops-érték |

> [!IMPORTANT]
> Nagy méretű tároló jelenleg nyilvános előzetes verzióban elérhető a következő régióban: USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA középső RÉGIÓJA, USA nyugati RÉGIÓJA, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, kelet-japán, Korea középső régiója, Kelet-Ausztrália.
>
> A nagy méretű tároló előzetes jelenleg nem támogatja:
>
> * Georedundáns biztonsági mentés
> * Adatbázisközi régió replikáció

### <a name="reaching-the-storage-limit"></a>Skálázhatósági méretkorlátot

Kevesebb mint 100 GB-os kiépített tároló kiszolgálók csak olvasható, ha a szabad tárhely kisebb, mint 512 MB-ot vagy a felhasznált tárterület mérete 5 %-át lesznek megjelölve. Több mint 100 GB-os kiépített tároló kiszolgálók lesznek megjelölve olvasási, csak akkor, ha a szabad tárterületre 5 GB-nál kisebb.

Például, ha ellátta 110 GB tárhelyet igényel, és a tényleges használat keresztül haladnak 105 GB, a kiszolgáló van megjelölve, csak olvasható. Azt is megteheti, ha 5 GB adattárolás kiépítése, a kiszolgáló van megjelölve csak olvasható 512 MB-nál kevesebb a szabad tárhely elérésekor.

Mialatt a szolgáltatás csak olvashatóvá próbálja tenni a kiszolgálót, minden új írási tranzakció kérését blokkolja a rendszer, és a meglévő aktív tranzakciók végrehajtása folytatódik. A kiszolgáló csak olvashatóként való beállításakor minden későbbi írási művelet és tranzakció meghiúsul. Az olvasási lekérdezések továbbra is zavartalanul működnek. A kiépített tárterület növelése után a kiszolgáló ismét készen fog állni az írási tranzakciók elfogadására.

Azt javasoljuk, hogy kapcsolja be a storage automatikus növekedést vagy riasztás beállításához arra az esetre, ha a kiszolgáló tárhelyét hamarosan eléri a küszöbértéket így elkerülheti a csak olvasható állapotának beolvasása. További információkért lásd a dokumentációt [riasztást beállítása](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Tárolási auto-növekedés

Ha a tárolási automatikus növekedés engedélyezve van, a storage automatikusan nő a terhelés befolyásolása nélkül. Kevesebb mint 100 GB-os kiépített tároló kiszolgálók esetében a felhasznált tárterület mérete eléri 5 GB-tal, amint a szabad tárhely nem éri el a nagyobb, mint 1 GB vagy 10 %-a felhasznált tárterület. Több mint 100 GB-os kiépített tároló kiszolgálók esetében a felhasznált tárterület mérete növekszik 5 % 5 %-a kiépített tárhely méretére alatt rendelkezésre álló szabad tárhely esetén. A fenti maximális tárolási korlátokhoz a alkalmazni.

Például ha 1000 GB tárhely kiépítése, és a tényleges használat keresztül haladnak 950 GB, a kiszolgáló tárméret 1050 GB-ra emelkedett. Azt is megteheti Ha 10 GB tárhelyet kiépítése, a tároló mérete esetén 15 GB-os növelését kevesebb mint 1 GB tárhelyet díjmentes.

## <a name="backup"></a>Backup

A szolgáltatás automatikusan biztonsági másolatokat a kiszolgálóról vesz igénybe. A minimális megőrzési idő a biztonsági másolatok hét napon. Beállíthatja, hogy egy legfeljebb 35 napos megőrzési idővel. A megőrzési bármikor módosítható a kiszolgáló élettartama során. Helyileg redundáns és georedundáns biztonsági mentések közül választhat. Georedundáns biztonsági mentéseket is vannak tárolva a [földrajzilag párosított régióba](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) a régió, ahol a kiszolgáló akkor jön létre. Ezt a redundanciát biztosít védelmet egy esetleges vészhelyzet esetén. Bármely más Azure-régió, amelyben a szolgáltatás georedundáns biztonsági másolat érhető el, a kiszolgáló helyreállítására is kaphat. Nem alkalmas a kiszolgáló létrehozása után két biztonsági másolati tárhely beállításai között módosíthatja.

## <a name="scale-resources"></a>Erőforrások skálázása

Miután létrehozta a kiszolgáló, egymástól függetlenül módosíthatja a virtuális magok, a hardver generációja, a tarifacsomag (kivéve a, illetve onnan alapszintű), a storage és a biztonsági másolat megőrzési idejének összege. A biztonsági mentési tár írja be a kiszolgáló létrehozása után nem módosítható. A virtuális magok számát felfelé és lefelé skálázhatók. A biztonsági másolat megőrzési idejének méretezhetők felfelé vagy lefelé 7-ről 35 napon belül. A tároló mérete pedig csak növelni. Az erőforrások méretezése elvégezhető a portál vagy az Azure CLI-vel felületén keresztül. A méretezés az Azure CLI-vel egy példa: [figyelés és méret egy Azure Database for MySQL-kiszolgálóhoz az Azure CLI-vel](scripts/sample-scale-server.md).

Amikor módosítja a virtuális magok száma, a hardver generációja, vagy a tarifacsomagot, az eredeti kiszolgáló egy példányát az új számítási felosztás jön létre. Miután az új kiszolgáló üzembe helyezéséig, kapcsolatok szálról az új kiszolgálóra. Során a pillanatban, amikor a rendszer átvált az új kiszolgáló új kapcsolat nem hozható létre, és az összes nem véglegesített tranzakció vissza legyen állítva. Ebben az ablakban változik, de a legtöbb esetben kevesebb mint egy percet.

Tároló méretezése és a biztonsági másolat megőrzési idejének módosítása is igaz online. Nem jár, és a folyamatot nem befolyásolja az alkalmazás. IOPS és a felhasznált tárterület mérete logikákat tárolási vertikális felskálázásával növelheti a rendelkezésre álló IOPS a kiszolgáló.

## <a name="pricing"></a>Díjszabás

A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/mysql/). A költség, a konfiguráció azt szeretné, hogy a [az Azure portal](https://portal.azure.com/#create/Microsoft.MySQLServer) havi költségét jeleníti meg a **tarifacsomag** lapon a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure díjkalkulátorát segítségével a becsült ár beolvasása. A a [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) webhely, válassza **elemek hozzáadása**, bontsa ki a **adatbázisok** kategória, és válassza **, Azure Database for MySQL**testre szabhatja beállítások.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [MySQL-kiszolgáló létrehozása a portálon](howto-create-manage-server-portal.md).
- Ismerje meg [szolgáltatási korlátozásaival](concepts-limits.md).
- Ismerje meg, hogyan [horizontális felskálázás a olvasható replikák](howto-read-replicas-portal.md).
