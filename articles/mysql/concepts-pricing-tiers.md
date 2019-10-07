---
title: A Azure Database for MySQL díjszabási szintjei
description: Ez a cikk a Azure Database for MySQL díjszabási szintjeit ismerteti.
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 83581b22389efe5c9c19689e3116b39ea049ccdc
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694203"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Árképzési szintek Azure Database for MySQL

Egy Azure Database for MySQL-kiszolgálót a három különböző díjszabási csomag egyikében hozhat létre: Alapszintű, általános célú és memória optimalizálva. Az árképzési csomagokat a virtuális mag olyan számítási mennyisége különbözteti meg, amely kiépíthető, memóriát virtuális mag, valamint az adatok tárolására szolgáló tárolási technológiát. Minden erőforrást a MySQL-kiszolgáló szintjén kell kiépíteni. A kiszolgálók egy vagy több adatbázissal rendelkezhetnek.

|    | **Basic** | **általános célú** | **Memória optimalizálva** |
|:---|:----------|:--------------------|:---------------------|
| Számítási generáció | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| Virtuális magok | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória/virtuális mag | 2 GB | 5 GB | 10 GB |
| Tároló mérete | 5 GB – 1 TB | 5 GB – 4 TB | 5 GB – 4 TB |
| Tárolótípus | Azure standard Storage | Azure Premium Storage | Azure Premium Storage |
| Adatbázis biztonsági másolatának megőrzési időtartama | 7 – 35 nap | 7 – 35 nap | 7 – 35 nap |

Árképzési szintek kiválasztásához használja a következő táblázatot kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Alapszintű | Könnyű számítási és I/O-teljesítményt igénylő munkaterhelések. Ilyenek például a fejlesztéshez és teszteléshez használt kiszolgálók, vagy a kisméretű, ritkán használt alkalmazások. |
| Általános rendeltetés | A legtöbb üzleti számítási feladat, amely kiegyensúlyozott számítást és memóriát igényel a méretezhető I/O-átviteli sebességgel. Ilyenek például a web-és mobil alkalmazások és más vállalati alkalmazások üzemeltetésére szolgáló kiszolgálók.|
| Memóriára optimalizált | Nagy teljesítményű adatbázis-munkaterhelések, amelyek memóriabeli teljesítményt igényelnek a gyorsabb tranzakció-feldolgozáshoz és a nagyobb egyidejűséghez. Ilyenek például a valós idejű és a nagy teljesítményű tranzakciós vagy analitikai alkalmazások feldolgozására szolgáló kiszolgálók.|

A kiszolgáló létrehozása után a virtuális mag, a hardver-létrehozási és az árképzési szint (kivéve az alapszintű és az alapszintű) számát másodpercek alatt módosíthatja. A tárolási mennyiség és a biztonsági mentés megőrzési időszaka egymástól függetlenül is beállítható az alkalmazás leállása nélkül. A biztonsági mentési tár típusa nem módosítható a kiszolgáló létrehozása után. További információ: Scale Resources ( [erőforrások méretezése](#scale-resources) ) szakasz.

## <a name="compute-generations-and-vcores"></a>Számítási generációk és virtuális mag

A számítási erőforrások virtuális mag-ként vannak megadva, amely az alapul szolgáló hardver logikai PROCESSZORát jelöli. Kelet-Kína 1, Észak-Kína 1, US DoD – középső régió és US DoD – keleti régió az Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorokon alapuló Gen 4 logikai processzorokat használ. Minden más régió az Intel E5-2673 v4 (Broadwell) 2,3 GHz-es processzorokon alapuló Gen 5 logikai CPU-ket használ.

## <a name="storage"></a>Storage

Az Ön által kiépített tárterület a Azure Database for MySQL-kiszolgáló számára elérhető tárolási kapacitás mennyisége. A tárterületet az adatbázisfájlok, az ideiglenes fájlok, a tranzakciónaplók és a MySQL-kiszolgáló naplófájljai használják. A kiépített tárterület teljes mennyisége határozza meg a kiszolgáló számára elérhető I/O-kapacitást is.

|    | **Basic** | **általános célú** | **Memória optimalizálva** |
|:---|:----------|:--------------------|:---------------------|
| Tárolótípus | Azure standard Storage | Azure Premium Storage | Azure Premium Storage |
| Tároló mérete | 5 GB – 1 TB | 5 GB – 4 TB | 5 GB – 4 TB |
| Tárolási növekmény mérete | 1 GB | 1 GB | 1 GB |
| IOPS | Változó |3 IOPS/GB<br/>Minimális 100 IOPS<br/>Max. 6000 IOPS | 3 IOPS/GB<br/>Minimális 100 IOPS<br/>Max. 6000 IOPS |

A kiszolgáló létrehozásakor és után további tárolókapacitást is hozzáadhat, és lehetővé teheti, hogy a rendszer automatikusan növelje a tárterületet a számítási feladatok tárolási feladatainak megfelelően. 

>[!NOTE]
> A tárterületet csak felfelé, nem lefelé lehet méretezni.

Az alapszintű csomag nem biztosít IOPS garanciát. A általános célú és a memória optimalizált díjszabási szintjein a IOPS a kiépített tároló méretével, 3:1 arányban méretezhető.

Az I/O-használatot a Azure Portal vagy az Azure CLI-parancsok használatával figyelheti. A figyelni kívánt mérőszámok a [tárolási korlát, a tárolási százalék, a felhasznált tárterület és az IO százalék](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Nagyméretű tároló (előzetes verzió)

A tárolási korlátokat a általános célú és a memória optimalizált szintjein növeljük. Az előzetes verzióra engedélyező újonnan létrehozott kiszolgálók legfeljebb 16 TB tárhellyel rendelkezhetnek. A IOPS-skála 3:1-ig terjedő arányban 20 000 IOPS. A jelenlegi általánosan elérhető tárolóhoz hasonlóan további tárolókapacitást is hozzáadhat a kiszolgáló létrehozása után, és lehetővé teszi, hogy a rendszer automatikusan növelje a tárterületet a számítási feladatok tárolási feladatainak megfelelően.

|              | **általános célú** | **Memória optimalizálva** |
|:-------------|:--------------------|:---------------------|
| Tárolótípus | Azure Premium Storage | Azure Premium Storage |
| Tároló mérete | 32 GB – 16 TB| 32 GB – 16 TB |
| Tárolási növekmény mérete | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>Minimális 100 IOPS<br/>Max. 20 000 IOPS| 3 IOPS/GB<br/>Minimális 100 IOPS<br/>Max. 20 000 IOPS |

> [!IMPORTANT]
> A nagyméretű tárterület jelenleg nyilvános előzetes verzióban érhető el a következő régiókban: USA keleti régiója, USA 2. keleti régiója, USA középső régiója, USA nyugati régiója, Észak-Európa, Nyugat-Európa, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Délkelet-Ázsia, Kelet-Ázsia, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, Kelet-Ausztrália, Ausztrália déli
>
> A nagyméretű tárolási előnézet jelenleg nem támogatja a következőket:
>
> * Földrajzilag redundáns biztonsági másolatok
> * Régiók közötti replikáció

### <a name="reaching-the-storage-limit"></a>A tárolási korlát elérése

A 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók csak olvashatóként vannak megjelölve, ha az ingyenes tárterület kevesebb, mint 512 MB vagy 5% a kiépített tároló mérete. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók csak olvashatóként vannak megjelölve, ha az ingyenes tárterület 5 GB-nál kevesebb.

Ha például 110 GB tárhellyel rendelkezik, és a tényleges kihasználtság meghaladja az 105 GB-ot, a kiszolgáló csak olvashatóként van megjelölve. Ha 5 GB tárterületet osztott ki, akkor a kiszolgáló csak olvashatóként van megjelölve, ha az ingyenes tárterület 512 MB-nál kevesebbet ér el.

Mialatt a szolgáltatás csak olvashatóvá próbálja tenni a kiszolgálót, minden új írási tranzakció kérését blokkolja a rendszer, és a meglévő aktív tranzakciók végrehajtása folytatódik. A kiszolgáló csak olvashatóként való beállításakor minden későbbi írási művelet és tranzakció meghiúsul. Az olvasási lekérdezések továbbra is zavartalanul működnek. A kiépített tárterület növelése után a kiszolgáló ismét készen fog állni az írási tranzakciók elfogadására.

Javasoljuk, hogy kapcsolja be a tárterület automatikus növekedését, vagy hozzon létre egy riasztást, amely értesíti, ha a kiszolgáló tárterülete eléri a küszöbértéket, így elkerülhető a csak olvasható állapot. További információt a [riasztás beállításával](howto-alert-on-metric.md)kapcsolatos dokumentációban talál.

### <a name="storage-auto-grow"></a>Tárterület automatikus növekedése

A tárterület automatikus növekedése megakadályozza, hogy a kiszolgáló kifogyjon a tárolóból, és csak olvasható legyen. Ha engedélyezve van a tárterület automatikus növekedése, a tárterület automatikusan növekszik a munkaterhelés befolyásolása nélkül. A 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiépített tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete a kiosztott tárterület méretének 5%-a alá esik. A fent megadott maximális tárolási korlátok érvényesek.

Ha például 1000 GB tárhelyet használ, és a tényleges kihasználtság meghaladja az 950 GB-ot, a kiszolgáló tárterületének mérete az 1050 GB-ra nő. Ha 10 GB tárterületet telepített, akkor a tárterület mérete 15 GB-ra nő, ha kevesebb, mint 1 GB tárterület ingyenes.

Ne feledje, hogy a tárterület csak akkor méretezhető, ha nem.

## <a name="backup"></a>Tartalék

A szolgáltatás automatikusan biztonsági másolatot készít a kiszolgálóról. A biztonsági mentések minimális megőrzési ideje hét nap. 35 napos megőrzési időtartamot is beállíthat. A megőrzés a kiszolgáló élettartama alatt bármikor módosítható. A helyileg redundáns és a Geo-redundáns biztonsági mentések közül választhat. A Geo-redundáns biztonsági mentéseket a kiszolgáló által létrehozott régió [földrajzi](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) összepárosítású régiójában is tárolja a rendszer. Ez a redundancia katasztrófa esetén nyújt védelmet. Lehetősége van arra is, hogy a kiszolgálót bármely olyan Azure-régióba visszaállítsa, amelyben a szolgáltatás a Geo-redundáns biztonsági mentésekkel elérhető. A kiszolgáló létrehozása után a két biztonsági mentési tárolási lehetőség között nem lehet módosítani.

## <a name="scale-resources"></a>Erőforrások skálázása

A kiszolgáló létrehozása után egymástól függetlenül módosíthatja a virtuális mag, a hardverek generációját, az árképzési szintet (kivéve az alapszintű és az alapszintű), a tárterület mennyiségét és a biztonsági mentés megőrzési időtartamát. A biztonsági mentési tár típusa nem módosítható a kiszolgáló létrehozása után. A virtuális mag száma növelhető felfelé vagy lefelé. A biztonsági másolat megőrzési időtartama 7 – 35 nap között méretezhető. A tárterület mérete csak növelni lehet. Az erőforrások méretezése történhet a portálon vagy az Azure CLI-n keresztül. Az Azure CLI-vel történő skálázásra példa: Azure Database for MySQL- [kiszolgáló figyelése és méretezése az Azure CLI használatával](scripts/sample-scale-server.md).

Ha megváltoztatja a virtuális mag számát, a hardver generálását vagy az árképzési szintet, az eredeti kiszolgáló egy példánya létrejön az új számítási foglalással. Az új kiszolgáló elindítása után a kapcsolatok átkerülnek az új kiszolgálóra. Az új kiszolgálóra való váltás pillanatában nem hozható létre új kapcsolat, és a nem véglegesített tranzakciók vissza lesznek állítva. A folyamat időtartama változó, de a legtöbb esetben kevesebb, mint egy perc.

A tárterület skálázása és a biztonsági mentés megőrzési időtartamának módosítása igaz online művelet. Nincs leállás, és az alkalmazása nincs hatással. A kiépített tároló méretével IOPS a IOPS elérhetővé teheti a kiszolgáló számára a tárterület növelésével.

## <a name="pricing"></a>Díjszabás

A legfrissebb díjszabási információkért tekintse meg a szolgáltatás díjszabását ismertető [oldalt](https://azure.microsoft.com/pricing/details/mysql/). A kívánt konfiguráció költségének megtekintéséhez a [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) a kiválasztott beállítások alapján megjeleníti a havi költséget a **díjszabási szintek** lapon. Ha még nem rendelkezik Azure-előfizetéssel, az Azure díjszabási kalkulátor használatával megbecsülheti a becsült árat. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **elemek hozzáadása**, majd az **adatbázisok** kategóriát, és **Azure Database for MySQL** a beállítások testreszabásához.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [hozhat létre MySQL-kiszolgálót a portálon](howto-create-manage-server-portal.md).
- A [szolgáltatás korlátainak](concepts-limits.md)megismerése.
- Ismerje meg, hogyan [bővítheti az olvasási replikákat](howto-read-replicas-portal.md).
