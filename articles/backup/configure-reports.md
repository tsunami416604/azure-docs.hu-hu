---
title: Azure Backup-jelentések konfigurálása
description: Azure Backup-jelentések konfigurálása és megtekintése Log Analytics és Azure-munkafüzetek használatával
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161201"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

A biztonsági mentési rendszergazdákra vonatkozó gyakori követelmény, hogy a biztonsági mentések során elemzéseket szerezzen a hosszú időn alapuló adatátfedések alapján. Az ilyen megoldások esetében több felhasználási eset is lehetséges – a felhasznált felhőalapú tárhely kiosztása és előrejelzése, a biztonsági mentések és-visszaállítások naplózása, valamint a kulcsfontosságú trendek azonosítása különböző részletességi szinteken.

A Azure Backup jelenleg olyan jelentéskészítési megoldást biztosít, amely [Azure monitor naplókat](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) és [Azure-munkafüzetek](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)használatát teszi lehetővé, így részletes elemzéseket készíthet a biztonsági másolatokról a teljes biztonsági mentési hagyatékon. Ez a cikk a biztonsági mentési jelentések konfigurálását és megtekintését ismerteti.

## <a name="supported-scenarios"></a>Támogatott esetek

* A biztonsági mentési jelentések az Azure-beli virtuális gépek, az SQL Azure-beli virtuális gépek SAP HANA, az Azure-beli virtuális gépek, a Azure Backup Agent (MARS), a Azure Backup Server (MABS) és a System Center DPM szolgáltatásokban támogatottak.
* A DPM számítási feladatokhoz a biztonsági mentési jelentések a DPM 5.1.363.0-es vagy újabb verziójával, valamint az ügynök 2.0.9127.0 és újabb verzióival támogatottak.
* A MABS számítási feladatokhoz a biztonsági mentési jelentések a MABS 13.0.415.0-es vagy újabb verziójával, valamint az ügynök 2.0.9170.0 és újabb verzióival támogatottak.
* A biztonsági mentési jelentések az összes biztonsági mentési elemben, tárolóban, előfizetésben és régióban is megtekinthetők, ha az adatokat egy olyan Log Analytics (LA) munkaterületre küldik, amelyhez a felhasználó hozzáfér. 
* Ha Ön [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -felhasználó, aki delegált hozzáféréssel rendelkezik ügyfelei előfizetéséhez, a jelentéseket az Azure Lighthouse használatával tekintheti meg az összes bérlőn.
* A naplók biztonsági mentési feladataihoz tartozó adatok jelenleg nem jelennek meg a jelentésekben.

## <a name="getting-started"></a>Bevezetés

A jelentések használatának megkezdéséhez kövesse az alábbi három lépést:

1. **Hozzon létre egy Log Analytics (LA) munkaterületet (vagy használjon egy meglévőt):**

Be kell állítania egy vagy több LA munkaterületet a biztonsági mentési jelentéskészítési adatai tárolásához. A hely és az előfizetés, ahol ez a LA munkaterület létrehozható, független attól a helytől és előfizetéstől, ahol a tárolók találhatók. 

Tekintse meg a következő cikket: [hozzon létre egy log Analytics munkaterületet a Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) egy La munkaterület beállításához.

Alapértelmezés szerint a LA munkaterületen tárolt adat 30 napig tart. Ha hosszabb ideig szeretné megtekinteni az adatok számát, módosítsa a LA munkaterület megőrzési időtartamát. A megőrzési időtartam módosításához tekintse meg a következő cikket: a [használat és a költségek kezelése Azure monitor naplók](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)használatával.

2. **Diagnosztikai beállítások konfigurálása a tárolók számára:**

Azure Resource Manager erőforrások, például Recovery Services-tárolók, rögzítik az ütemezett műveletekkel és a felhasználó által aktivált műveletekkel kapcsolatos információkat diagnosztikai adatként. 

A Recovery Services-tároló figyelés szakaszában válassza a **diagnosztikai beállítások** elemet, és adja meg a Recovery Services tároló diagnosztikai adataihoz tartozó célt. [További információ a diagnosztikai események használatáról](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Diagnosztikai beállítások panel](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

A Azure Backup beépített Azure Policy is biztosít, amely automatizálja az adott hatókörben lévő összes tároló diagnosztikai beállításainak konfigurációját. A következő cikkből megtudhatja, hogyan használhatja ezt a házirendet: tár [diagnosztikai beállításainak konfigurálása nagy méretekben](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> A diagnosztika konfigurálása után 24 órát is igénybe vehet, amíg a kezdeti adattovábbítás befejeződik. Ha az adatok beindulnak a LA munkaterületre, előfordulhat, hogy a jelentésekben nem jelenik meg azonnal az adatok, mert a jelenlegi részleges nap adatai nem jelennek meg a jelentésekben (további részletek [itt](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)). Ezért azt javasoljuk, hogy a-tárolók konfigurálása után 2 nappal az adatküldés Log Analytics.

3. **Jelentések megtekintése a Azure Portalon:**

Ha úgy konfigurálta a tárolókat, hogy az adatait a LA-nek küldje, tekintse meg a biztonsági mentési jelentéseket úgy, hogy a tár egyik paneljére navigál, és a **biztonsági mentési jelentések** menüpontra kattint. 

![Tár irányítópultja](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

A hivatkozásra kattintva megnyílik a biztonsági mentési jelentés munkafüzete.

> [!NOTE]
> * Jelenleg a jelentés kezdeti terhelése akár 1 percet is igénybe vehet.
> * A Recovery Services-tároló csupán a biztonsági mentési jelentések belépési pontja. Miután a biztonsági mentési jelentések munkafüzet megnyílik egy tároló paneljén, az összes tárolóban összesíteni fogja az adatokat (a megfelelő LA munkaterületek kiválasztásával).

Alább látható a jelentés által tartalmazott különböző lapok leírása:

* **Összefoglalás** – az összefoglalás lapon a Backup-hagyaték magas szintű áttekintése látható. Az összefoglalás lapon gyorsan áttekintheti a biztonsági másolati elemek teljes számát, a felhasznált Felhőbeli tárterületet, a védett példányok számát és a feladat sikerességi arányát a munkaterhelés típusától függően. Egy adott biztonsági mentési összetevő típusával kapcsolatos részletesebb információkért navigáljon a megfelelő lapokhoz.

![Összefoglalás lap](./media/backup-azure-configure-backup-reports/summary.png)

* **Biztonsági másolati elemek** – a biztonsági mentési elemek lapon megtekintheti a biztonsági mentési elem szintjén felhasznált Felhőbeli tárterület információit és trendit. Ha például az SQL-t használja az Azure virtuális gép biztonsági mentésében, a biztonsági mentés alatt álló SQL-adatbázisokhoz felhasznált felhőalapú tárterületet láthatja. Azt is megteheti, hogy megtekinti az adott védelmi állapot biztonsági másolati elemeinek adatait. Ha például a lap tetején található **védelem leállítva** csempére kattint, a az alábbi widgetek alapján szűri az adatokat csak a védelemmel ellátni kívánt biztonsági mentési elemekhez.

![Biztonsági másolati elemek lap](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Használat** – a használat lapon megtekintheti a biztonsági mentések legfontosabb számlázási paramétereit. Az ezen a lapon megjelenő információk számlázási entitás (védett tároló) szintjén találhatók. Ha például egy DPM-kiszolgáló az Azure-ba készít biztonsági mentést, akkor megtekintheti a védett példányok és a DPM-kiszolgáló által felhasznált Felhőbeli tárterület trendjét. Hasonlóképpen, ha az SQL-t használja Azure Backup vagy SAP HANA a Azure Backupban, ezen a lapon a használattal kapcsolatos információk jelennek meg a virtuális gép szintjén, amelyre ezek az adatbázisok tartoznak.

![Használat lap](./media/backup-azure-configure-backup-reports/usage.png)

* **Feladatok** – a feladatok lapon megtekintheti a feladatok hosszú futású tendenciáit, például a sikertelen feladatok számát naponta, valamint a feladat meghibásodásának leggyakoribb okait. Ezeket az információkat összesített szinten és biztonsági mentési elemszintű szinten is megtekintheti. A rács egy adott biztonsági másolati elemére kattintva részletes információkat jeleníthet meg a kijelölt időtartományban a biztonsági mentési elemen aktivált összes feladatról.

![Feladatok lap](./media/backup-azure-configure-backup-reports/jobs.png)

* **Házirendek** – a házirendek lapon megtekintheti az összes aktív házirend adatait, például a társított elemek számát, valamint az adott szabályzat keretében biztonsági mentés alatt álló elemek által felhasznált teljes felhőalapú tárterületet. Egy adott szabályzatra kattintva megtekintheti az egyes kapcsolódó biztonsági másolati elemekre vonatkozó információkat.

![Házirendek lap](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exportálás Excelbe

Bármely widget (táblázat/diagram) jobb felső sarkában található lefelé mutató nyílra kattintva exportálhatja a widget tartalmát egy Excel-táblázatként, amely a meglévő szűrőket alkalmazza. Ha egy táblázat több sorát szeretné exportálni az Excelbe, növelheti a lapon megjelenő sorok számát az egyes rácsok tetején lévő **sorok/oldal** legördülő lista használatával.

## <a name="pinning-to-dashboard"></a>Rögzítés az irányítópulton

Az egyes widgetek tetején található rögzítés ikonra kattintva rögzítheti a widgetet a Azure Portal irányítópulton. Ez segít a szükséges legfontosabb információk megjelenítéséhez igazított testreszabott irányítópultok létrehozásában.

## <a name="cross-tenant-reports"></a>Több-bérlős jelentések

Ha Ön egy [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -felhasználó, aki több bérlői környezetben delegált hozzáféréssel rendelkezik az előfizetésekhez, az alapértelmezett előfizetés-szűrőt használhatja (a Azure Portal jobb felső részén található szűrő ikonra kattintva kiválaszthatja az összes olyan előfizetést, amelynél meg szeretné tekinteni a kívánt adatmennyiséget. Ezzel lehetővé teszi, hogy a több-bérlős jelentések megjelenítéséhez a bérlők által választott LA munkaterületeket válassza.

## <a name="conventions-used-in-backup-reports"></a>A biztonsági mentési jelentésekben használt konvenciók

* A szűrők balról jobbra, illetve felülről lefelé haladva működnek az egyes lapokon, azaz az összes szűrő csak azokra a widgetekre vonatkozik, amelyek a szűrő jobb oldalán vagy a szűrő alatt vannak elhelyezve. 
* Ha egy színes csempére kattint, a csempe alatti widgetek az adott csempe értékére vonatkozó rekordokat szűrik. Ha például a *védelem leállított* csempére kattint a biztonsági mentési elemek lapon, az alábbi rácsokat és diagramokat szűrve jeleníti meg a "védelem leállítva" állapotú biztonsági másolati elemek adatai.
* A nem színezett csempék nem kattintanak.
* Az aktuális részleges nap adatai nem láthatók a jelentésekben. Így ha az **időtartomány** kiválasztott értéke, az ***utolsó 7 nap***, a jelentés az utolsó 7 befejezett nap rekordjait jeleníti meg (amely nem tartalmazza az aktuális napot).
* A jelentés a feladatok részleteit jeleníti meg (a naplózási feladatok kivételével), amelyek a kijelölt időtartományban voltak **aktiválva** . 
* A Felhőbeli tárolás és a védett példányok számára megjelenített értékek a kijelölt időtartomány **végén** találhatók.
* A jelentésekben megjelenő biztonsági másolati elemek azok az elemek, amelyek a kijelölt időtartomány **végén** találhatók. A kijelölt időtartomány közepén törölt biztonsági másolati elemek nem jelennek meg. Ugyanez az egyezmény érvényes a biztonsági mentési szabályzatokra is.

## <a name="query-load-times"></a>Lekérdezés betöltési ideje

A biztonsági mentési jelentésben szereplő widgeteket a felhasználó LA munkaterületein futó Kusto-lekérdezések működtetik. Mivel ezek a lekérdezések általában nagy mennyiségű adatfeldolgozást foglalnak magukban, több összekapcsolással a gazdagabb elemzések lehetővé tételéhez, előfordulhat, hogy a widgetek nem töltődnek be azonnal, ha a felhasználó egy nagyméretű Backup-birtokon tekinti meg a jelentéseket. Az alábbi táblázat a különböző widgetek betöltéséhez szükséges idő durva becslését nyújtja a biztonsági mentési elemek száma és a jelentés megtekintésének időtartománya alapján:

| **Adatforrások száma**                         | **Időbeli horizont** | **Betöltési idő (kb.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 hónap          | Csempék: 5-10 mp <br> Rácsok: 5-10 mp <br> Diagramok: 5-10 mp <br> Jelentési szintű szűrők: 5-10 mp|
| ~ 5 K                       | 3 hónap          | Csempék: 5-10 mp <br> Rácsok: 5-10 mp <br> Diagramok: 5-10 mp <br> Jelentési szintű szűrők: 5-10 mp|
| ~ 10 K                       | 3 hónap          | Csempék: 15-20 mp <br> Rácsok: 15-20 mp <br> Diagramok: 1-2 perc <br> Jelentési szintű szűrők: 25-30 mp|
| ~ 15 K                       | 1 hónap          | Csempék: 15-20 mp <br> Rácsok: 15-20 mp <br> Diagramok: 50-60 mp <br> Jelentési szintű szűrők: 20-25 mp|
| ~ 15 K                       | 3 hónap          | Csempék: 20-30 mp <br> Rácsok: 20-30 mp <br> Diagramok: 2-3 perc <br> Jelentési szintű szűrők: 50-60 mp |

## <a name="what-happened-to-the-power-bi-reports"></a>Mi történt a Power BI jelentésekkel?
* A korábbi Power BI template alkalmazás jelentéskészítéshez (amely egy Azure Storage-fiókból származó adatokból származik) elavult elérési úton van. Javasoljuk, hogy a jelentések megtekintéséhez indítsa el a tár diagnosztikai adatok küldését Log Analytics a fentiekben leírtak szerint.

* Emellett a diagnosztikai adatok egy Storage-fiókba vagy egy LA-munkaterületre való küldésének v1-sémája is egy elavult útvonalon található. Ez azt jelenti, hogy ha a v1 séma alapján egyéni lekérdezéseket vagy automatizálásokat írt, javasoljuk, hogy frissítse ezeket a lekérdezéseket a jelenleg támogatott v2 séma használatára.

## <a name="next-steps"></a>Következő lépések
[További információ a Azure Backup figyeléséről és jelentéskészítéséről](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)