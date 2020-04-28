---
title: Azure Backup-jelentések konfigurálása
description: Azure Backup jelentések konfigurálása és megtekintése Log Analytics és Azure-munkafüzetek használatával
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: c1af9a532b390b428e74957c455988dfd4df3967
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184945"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

A biztonsági mentési rendszergazdákra vonatkozó gyakori követelmény, hogy a biztonsági másolatok alapján bepillantást nyerjen a hosszú idő alatt álló adatokat. Ilyen megoldás esetén a következő esetekben használhatók:

- A Felhőbeli tárhely kiosztása és előrejelzése.
- Biztonsági mentések és visszaállítások naplózása.
- A legfontosabb trendek azonosítása különböző részletességi szinteken.

A Azure Backup jelenleg olyan jelentéskészítési megoldást biztosít, amely [Azure monitor naplókat](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) és [Azure-munkafüzeteket](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)használ. Ezekkel az erőforrásokkal részletes információkhoz juthat a biztonsági másolatokról a teljes Backup-hagyatékon keresztül. Ez a cikk azt ismerteti, hogyan lehet konfigurálni és megtekinteni Azure Backup jelentéseket.

## <a name="supported-scenarios"></a>Támogatott esetek

- A biztonsági mentési jelentések az Azure-beli virtuális gépek, az SQL Azure-beli virtuális gépek SAP HANA, az Azure-beli virtuális gépek, a Microsoft Azure Recovery Services-(MARS-) ügynök, a Microsoft Azure Backup-kiszolgáló (MABS) és a System Center Data Protection Manager (DPM) esetében támogatottak. Az Azure-fájlmegosztás biztonsági mentésére vonatkozó jelentések jelenleg nem láthatók a biztonsági mentési jelentésekben.
- A DPM számítási feladatokhoz a biztonsági mentési jelentések támogatottak a DPM 5.1.363.0-es vagy újabb verziójával, valamint az ügynök 2.0.9127.0 és újabb verziójával.
- A MABS számítási feladatokhoz a biztonsági mentési jelentések támogatottak a MABS 13.0.415.0-es vagy újabb verziójával, valamint az ügynök 2.0.9170.0 és újabb verziójával.
- A biztonsági mentési jelentések az összes biztonsági mentési elemben, tárolóban, előfizetésben és régióban is megtekinthetők, ha az adatokat egy olyan Log Analytics munkaterületre küldik, amelyhez a felhasználó hozzáfér. A jelentések egy készlethez való megtekintéséhez csak olvasási hozzáféréssel kell rendelkeznie ahhoz a Log Analytics munkaterülethez, amelyhez a tárolók küldik az adatokat. Nincs szükség az egyes tárak elérésére.
- Ha Ön [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -felhasználó, aki delegált hozzáféréssel rendelkezik ügyfelei előfizetéséhez, a jelentéseket az Azure Lighthouse használatával tekintheti meg az összes bérlőn.
- A naplók biztonsági mentési feladatai jelenleg nem jelennek meg a jelentésekben.

## <a name="get-started"></a>Bevezetés

A jelentések használatának megkezdéséhez kövesse az alábbi lépéseket.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. hozzon létre egy Log Analytics munkaterületet, vagy használjon egy meglévőt

Állítson be egy vagy több Log Analytics munkaterületet a biztonsági mentési jelentéskészítési adatai tárolásához. A hely és az előfizetés, ahol a Log Analytics munkaterület létrehozható, független a tárolók helyétől és előfizetéstől.

Log Analytics munkaterület beállításához tekintse meg a [log Analytics munkaterület létrehozása a Azure Portalben](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)című témakört.

Alapértelmezés szerint a Log Analytics munkaterületen lévő adat 30 napig tart. Ha hosszabb ideig szeretné megtekinteni az adatok számát, módosítsa a Log Analytics munkaterület megőrzési időtartamát. A megőrzési időtartam módosításához tekintse meg a [használat és a költségek kezelése Azure monitor naplókkal](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)című témakört.

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. a tárolók diagnosztikai beállításainak konfigurálása

Azure Resource Manager erőforrások, például Recovery Services-tárolók, rögzítik az ütemezett műveletekkel és a felhasználó által aktivált műveletekkel kapcsolatos információkat diagnosztikai adatként.

A Recovery Services-tároló figyelés szakaszában válassza a **diagnosztikai beállítások** lehetőséget, és adja meg a Recovery Services tároló diagnosztikai adataihoz tartozó célt. További információ a diagnosztikai események használatáról: Recovery Services-tárolók [diagnosztikai beállításainak használata](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Diagnosztikai beállítások ablaktábla](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

A Azure Backup beépített Azure Policy-definíciót is biztosít, amely automatizálja a diagnosztikai beállítások konfigurációját egy adott hatókörben lévő összes tárolónál. A szabályzat használatának megismeréséhez tekintse meg a tár [diagnosztikai beállításainak konfigurálása skálán](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)című témakört.

> [!NOTE]
> A diagnosztika konfigurálása után akár 24 óráig is eltarthat, amíg a kezdeti adattovábbítás befejeződik. Miután az adat elindult a Log Analytics munkaterületre, előfordulhat, hogy a jelentésekben nem jelenik meg azonnal az adat, mert az aktuális részleges napi adat nem jelenik meg a jelentésekben. További információ: [a biztonsági mentési jelentésekben használt konvenciók](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Javasoljuk, hogy a tárolók konfigurálása után két nappal megtekintse a jelentéseket, hogy az adatküldés Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. jelentések megtekintése a Azure Portal

Miután konfigurálta a tárolókat, hogy az adatküldés Log Analytics, tekintse meg a biztonsági mentési jelentéseket a tár ablaktábláján, majd válassza a **biztonsági mentési jelentések**elemet.

![Tár irányítópultja](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Válassza ezt a hivatkozást a biztonsági mentési jelentés munkafüzetének megnyitásához.

> [!NOTE]
>
> - A jelentés kezdeti terhelése jelenleg akár 1 percet is igénybe vehet.
> - A Recovery Services-tároló csupán a biztonsági mentési jelentések belépési pontja. Miután a biztonsági mentési jelentés munkafüzet megnyílik egy tár ablaktábláján, válassza ki a megfelelő Log Analytics munkaterületeket az összes tárolóban összesíteni kívánt adatok megtekintéséhez.

A jelentés különböző lapokat tartalmaz:

- **Összefoglalás**: ezen a lapon magas szintű áttekintést kaphat a Backup-hagyatékról. Gyorsan áttekintheti a biztonsági másolati elemek teljes számát, a felhasznált Felhőbeli tárterületet, a védett példányok számát, valamint a feladat sikerességi arányát a munkaterhelés típusától függően. Az adott biztonsági mentési összetevő típusával kapcsolatos részletesebb információkért nyissa meg a megfelelő lapokat.

   ![Összefoglalás lap](./media/backup-azure-configure-backup-reports/summary.png)

- **Biztonsági másolati elemek**: ezen a lapon megtekintheti a biztonsági mentési elem szintjén felhasznált Felhőbeli tárolással kapcsolatos információkat és trendeket. Ha például az SQL-t használja egy Azure-beli virtuális gép biztonsági mentésében, megtekintheti a biztonsági mentés alatt álló SQL-adatbázisokhoz felhasznált felhőalapú tárhelyet. Azt is megteheti, hogy megtekinti az adott védelmi állapot biztonsági másolati elemeinek adatait. Ha például a lap tetején található **védelem leállított** csempét választja, a rendszer az összes widgetet kiszűri, hogy csak a védelem leállított állapotának biztonsági mentési elemeire vonatkozó adatokat jelenítse meg.

   ![Biztonsági másolati elemek lap](./media/backup-azure-configure-backup-reports/backup-items.png)

- **Használat**: ezen a lapon megtekintheti a biztonsági másolatok legfontosabb számlázási paramétereit. Az ezen a lapon megjelenő információk számlázási entitás (védett tároló) szintjén találhatók. Ha például egy DPM-kiszolgáló az Azure-ba készít biztonsági mentést, akkor megtekintheti a védett példányok és a DPM-kiszolgáló által felhasznált Felhőbeli tárterület trendjét. Hasonlóképpen, ha az SQL-t használja Azure Backup vagy SAP HANA a Azure Backupban, ezen a lapon a használattal kapcsolatos információk jelennek meg a virtuális gép szintjén, amelyben ezeket az adatbázisokat tartalmazza.

   ![Használat lap](./media/backup-azure-configure-backup-reports/usage.png)

- **Feladatok**: ezen a lapon megtekintheti a feladatok hosszan futó trendjét, például a sikertelen feladatok számát napi bontásban, valamint a feladat meghibásodásának leggyakoribb okait. Ezeket az információkat összesített szinten és biztonsági mentési elemszintű szinten is megtekintheti. A rács egy adott biztonsági másolati elemének kiválasztásával megtekintheti a kijelölt időtartományban a biztonsági mentési elemen aktivált összes feladatra vonatkozó részletes információkat.

   ![Feladatok lap](./media/backup-azure-configure-backup-reports/jobs.png)

- **Szabályzatok**: ezen a lapon megtekintheti az összes aktív házirend adatait, például a társított elemek számát, valamint az adott házirendben biztonsági mentés alatt álló elemek által felhasznált teljes felhőalapú tárterületet. Válasszon ki egy olyan házirendet, amellyel megtekintheti az egyes kapcsolódó biztonsági másolati elemekkel kapcsolatos információkat.

   ![Házirendek lap](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportálás Excelbe

Válassza a jobb felső sarokban lévő lefelé mutató nyilat, például egy táblázatot vagy diagramot, hogy a widget tartalmát Excel-táblázatként exportálja, amely a meglévő szűrőket alkalmazza. Ha egy táblázat több sorát szeretné exportálni az Excelbe, növelheti a lapon megjelenő sorok számát az egyes rácsok tetején lévő **sorok/lap** legördülő lista használatával.

## <a name="pin-to-dashboard"></a>Rögzítés az irányítópulton

Válassza az egyes widgetek tetején található rögzítés gombot, hogy rögzítse a widgetet a Azure Portal irányítópulton. Ezzel a funkcióval testreszabott irányítópultokat hozhat létre, amelyek a szükséges legfontosabb információk megjelenítéséhez vannak igazítva.

## <a name="cross-tenant-reports"></a>Több-bérlős jelentések

Ha az [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -t az előfizetések több bérlős környezetben való delegált hozzáférésével használja, használhatja az alapértelmezett előfizetési szűrőt. Válassza a szűrő gombot a Azure Portal jobb felső sarkában, és válassza ki az összes olyan előfizetést, amelynek az adatait meg szeretné jeleníteni. Így kiválaszthatja Log Analytics munkaterületeket a bérlők között a több-bérlős jelentések megtekintéséhez.

## <a name="conventions-used-in-backup-reports"></a>A biztonsági mentési jelentésekben használt konvenciók

- A szűrők balról jobbra, illetve felülről lefelé haladva működnek az egyes lapokon. Ez azt is megteheti, hogy az összes szűrő csak az összes olyan widgetre vonatkozik, amely a szűrő jobb oldalán vagy a szűrő alatt helyezkedik el.
- A színes csempe kiválasztásával a csempe alatti widgetek a csempe értékéhez tartozó rekordokat szűrik. Például a védelmi **elemek** lapon a **védelem leállított** csempe kiválasztásával az alábbi rácsokat és diagramokat szűrve jelenítheti meg a biztonsági másolati elemek adatai a védelem leállított állapotában.
- A nem színes csempék nem kattintanak.
- A jelentésekben nem jelennek meg az aktuális részleges napi adat. Tehát ha az **időtartomány** kiválasztott értéke **utolsó 7 nap**, a jelentés az utolsó hét befejezett nap rekordjait jeleníti meg. Az aktuális nap nem szerepel.
- A jelentés a feladatok részleteit jeleníti meg (a naplózási feladatok kivételével), amelyek a kijelölt időtartományban voltak *aktiválva* .
- A **Felhőbeli tárolás** és a **védett példányok** értéke a kijelölt időtartomány *végén* található.
- A jelentésekben megjelenő biztonsági másolati elemek azok az elemek, amelyek a kijelölt időtartomány *végén* találhatók. A kijelölt időtartomány közepén törölt biztonsági másolati elemek nem jelennek meg. Ugyanez az egyezmény érvényes a biztonsági mentési szabályzatokra is.

## <a name="query-load-times"></a>Lekérdezés betöltési ideje

A biztonsági mentési jelentésben szereplő widgeteket Kusto-lekérdezések működtetik, amelyek a felhasználó Log Analytics munkaterületein futnak. Ezek a lekérdezések általában nagy mennyiségű adatfeldolgozást foglalnak magukban, több illesztéssel a gazdagabb elemzések lehetővé tételéhez. Ennek eredményeképpen előfordulhat, hogy a widgetek nem töltődnek be azonnal, amikor a felhasználó egy nagyméretű Backup-birtokon tekint át jelentéseket. Ez a táblázat a különböző widgetek betöltéséhez szükséges idő durva becslését nyújtja a biztonsági mentési elemek száma és a jelentés megtekintésének időtartománya alapján.

| **Adatforrások száma**                         | **Időbeli horizont** | **Megközelítő betöltési idő**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 hónap          | Csempék: 5-10 mp <br> Rácsok: 5-10 mp <br> Diagramok: 5-10 mp <br> Jelentési szintű szűrők: 5-10 mp|
| ~ 5 K                       | 3 hónap          | Csempék: 5-10 mp <br> Rácsok: 5-10 mp <br> Diagramok: 5-10 mp <br> Jelentési szintű szűrők: 5-10 mp|
| ~ 10 K                       | 3 hónap          | Csempék: 15-20 mp <br> Rácsok: 15-20 mp <br> Diagramok: 1-2 perc <br> Jelentési szintű szűrők: 25-30 mp|
| ~ 15 K                       | 1 hónap          | Csempék: 15-20 mp <br> Rácsok: 15-20 mp <br> Diagramok: 50-60 mp <br> Jelentési szintű szűrők: 20-25 mp|
| ~ 15 K                       | 3 hónap          | Csempék: 20-30 mp <br> Rácsok: 20-30 mp <br> Diagramok: 2-3 perc <br> Jelentési szintű szűrők: 50-60 mp |

## <a name="what-happened-to-the-power-bi-reports"></a> Mi történt a Power BI-jelentésekkel? 

- Az Azure Storage-fiókból származó, a jelentéskészítéshez korábban Power BI sablon alkalmazás egy elavult útvonalon található. Javasoljuk, hogy a jelentések megtekintéséhez indítsa el a tár diagnosztikai adatok küldését Log Analytics.

- * Emellett a diagnosztikai adatok egy Storage-fiókba vagy egy LA-munkaterületre való küldésének [v1-sémája](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#v1-schema-vs-v2-schema) is egy elavult útvonalon található. Ez azt jelenti, hogy ha a v1 séma alapján egyéni lekérdezéseket vagy automatizálásokat írt, javasoljuk, hogy frissítse ezeket a lekérdezéseket a jelenleg támogatott v2 séma használatára.

## <a name="next-steps"></a>További lépések

[További információ a Azure Backup figyeléséről és jelentéskészítéséről](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
