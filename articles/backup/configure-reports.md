---
title: Azure Backup-jelentések konfigurálása
description: Jelentések konfigurálása és megtekintése az Azure Backup számára a Log Analytics és az Azure-munkafüzetek használatával
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617811"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

A biztonsági mentési rendszergazdák gyakori követelménye, hogy hosszú ideig tartó adatok alapján betekintést nyerjenek a biztonsági mentések beszerzéseibe. Használati esetek egy ilyen megoldás a következők:

 - A felhasznált felhőalapú tárhely lefoglalása és előrejelzése.
 - Biztonsági mentések és visszaállítások naplózása.
 - A legfontosabb trendek azonosítása a granularitás különböző szintjein.

Az Azure Backup ma olyan jelentéskészítési megoldást kínál, amely [az Azure Monitor-naplókat](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) és [az Azure-munkafüzeteket](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)használja. Ezek az erőforrások segítségével gazdag betekintést nyerhet a biztonsági mentések teljes biztonsági mentési állapotába. Ez a cikk ismerteti, hogyan konfigurálhatja és tekintheti meg az Azure Backup-jelentéseket.

## <a name="supported-scenarios"></a>Támogatott esetek

* A biztonsági mentési jelentések támogatottak az Azure virtuális gépek, az SQL az Azure virtuális gépekben, az SAP HANA/ASE az Azure virtuális gépeken, a Microsoft Azure Recovery Services (MARS) ügynök, a Microsoft Azure Backup Server (MABS) és a System Center Data Protection Manager (DPM).
* A DPM-számítási feladatok esetében a biztonsági mentési jelentések a DPM 5.1.363.0-s és újabb verziói, valamint a 2.0.9127.0-s vagy újabb ügynökverzió konszern számára támogatottak.
* A MABS-számítási feladatok esetében a biztonsági mentési jelentések a 13.0.415.0-s és újabb verziójú MABS-verzió, valamint a 2.0.9170.0-s vagy újabb verziójú ügynöki verzió esetében támogatottak.
* A biztonsági mentési jelentések megtekinthetők az összes biztonsági mentési elem, tároló, előfizetés és régió között, feltéve, hogy az adatokat egy Log Analytics-munkaterületre küldi, amelyhez a felhasználó hozzáfér. A tárolók egy készletének megtekintéséhez csak a Log Analytics munkaterülethez való hozzáféréssel kell rendelkeznie, amelyre a tárolók küldik az adataikat. Nem kell hozzáférnie az egyes páncéltermekhez.
* Ha Ön [Azure Lighthouse-felhasználó,](https://docs.microsoft.com/azure/lighthouse/) aki delegált hozzáféréssel rendelkezik az ügyfelek előfizetéseihez, ezekkel a jelentésekkel az Azure Lighthouse segítségével megtekintheti az összes bérlő jelentéseit.
* A naplóbiztonsági feladatok adatai jelenleg nem jelennek meg a jelentésekben.

## <a name="get-started"></a>Bevezetés

A jelentések használatának megkezdéséhez kövesse az alábbi lépéseket.

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Log Analytics-munkaterület létrehozása vagy meglévő munkaterület használata

Állítson be egy vagy több Log Analytics-munkaterületet a biztonsági mentési jelentési adatok tárolásához. A hely és az előfizetés, ahol ez a Log Analytics-munkaterület hozható létre, független a ttól a helytől és az előfizetéstől, ahol a tárolók léteznek. 

A Log Analytics-munkaterület beállításáról a [Log Analytics-munkaterület létrehozása az Azure Portalon](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)című témakörben található.

Alapértelmezés szerint a Log Analytics-munkaterületen lévő adatok 30 napig megőrződnek. Hosszabb időhorizontadatainak megtekintéséhez módosítsa a Log Analytics munkaterület megőrzési időszakát. Az adatmegőrzési időszak módosításáról a [Használat és költségek kezelése az Azure Monitor naplóival című témakörben jelenik meg.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. A tárolók diagnosztikai beállításainak konfigurálása

Az Azure Resource Manager-erőforrások, például a Recovery Services-tárolók, diagnosztikai adatokként rögzítik az ütemezett műveletekre és a felhasználó által aktivált műveletekre vonatkozó információkat. 

A figyelési szakaszban a Recovery Services-tároló, válassza **diagnosztikai beállításokat,** és adja meg a cél a Recovery Services-tároló diagnosztikai adatok. A diagnosztikai események használatáról a [Helyreállítási szolgáltatások tárolóinak diagnosztikai beállításainak használata](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)című témakörben olvashat bővebben.

![Diagnosztikai beállítások ablaktábla](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Az Azure Backup egy beépített Azure-szabályzatot is biztosít, amely automatizálja a diagnosztikai beállítások konfigurálását egy adott hatókör összes tárolójához. A házirend használatáról a [Tároló diagnosztikai beállításainak nagyléptékben című](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)témakörben olvashat.

> [!NOTE]
> A diagnosztika konfigurálása után akár 24 órát is igénybe vehet a kezdeti adatleküldéses művelet. Miután az adatok beáramlanak a Log Analytics munkaterületre, előfordulhat, hogy nem jelennek meg azonnal az adatok a jelentésekben, mert az aktuális részleges nap adatai nem jelennek meg a jelentésekben. További információt a [Biztonsági másolat jelentésekben használt konvenciók című témakörben talál.](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports) Azt javasoljuk, hogy kezdje el a jelentések megtekintését két nappal azután, hogy konfigurálja a tárolók adatok küldése a Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Jelentések megtekintése az Azure Portalon

Miután beállította a tárolókat, hogy adatokat küldjön a Log Analytics szolgáltatásnak, tekintse meg a biztonsági mentési jelentéseket a tároló ablaktáblájára, és válassza a **Biztonsági másolat jelentések**lehetőséget.

![A trezor irányítópultja](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Ezzel a hivatkozással megnyithatja a Biztonsági másolat jelentés munkafüzetét.

> [!NOTE]
> * Jelenleg a jelentés kezdeti terhelése akár 1 percet is igénybe vehet.
> * A Helyreállítási szolgáltatások tároló csupán egy belépési pont a biztonsági mentési jelentések. Miután a biztonsági mentési jelentés munkafüzete megnyílik a tároló ablaktáblájáról, válassza ki a Log Analytics-munkaterületek megfelelő készletét az összes tárolóban összesített adatok megtekintéséhez.

A jelentés különböző lapokat tartalmaz:

* **Összegzés:** Használja ezt a lapot, hogy egy magas szintű áttekintést a biztonsági mentési állapot. Gyors áttekintést kaphat a biztonsági mentési elemek teljes számáról, a felhasznált teljes felhőbeli tárhelyről, a védett példányok számáról és a feladat számítási feladatok típusonkénti sikerességi arányáról. Egy adott biztonsági másolat összetevőtípusával kapcsolatos részletesebb információkért látogasson el a megfelelő lapokra.

   ![Összefoglalás lap](./media/backup-azure-configure-backup-reports/summary.png)

* **Biztonsági másolat elemei:** Ezen a lapon megtekintheti a biztonsági másolat elem szintjén felhasznált felhőalapú tárolással kapcsolatos információkat és trendeket. Ha például az SQL-t egy Azure virtuális gép biztonsági mentésében használja, láthatja a felhőbeli tárhelyet, amelyet minden egyes SQL-adatbázishoz felhasznált, és amelyről biztonsági mentés készül. Azt is beállíthatja, hogy egy adott védelmi állapotú biztonsági mentési elemek adatai jelenjenek meg. Ha például a lap tetején a **Védelem leállított** csempe lehetőséget választja, az alatta lévő összes widgetet szűri, hogy csak a Védelmi védelem leállított állapotú biztonsági mentési elemek adatait jelenítse meg.

   ![Biztonsági másolat elemei lap](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Használat:** Ezen a lapon megtekintheti a biztonsági mentések legfontosabb számlázási paramétereit. Az ezen a lapon megjelenő információk számlázási entitás (védett tároló) szintjén vannak. Például abban az esetben, ha egy DPM-kiszolgálóazure-ba készül, megtekintheti a védett példányok és a DPM-kiszolgáló számára felhasznált felhőalapú tárhely trendjét. Hasonlóképpen, ha az SQL az Azure Backup vagy az SAP HANA az Azure Backup, ez a lap a használattal kapcsolatos információkat a virtuális gép szintjén, amelyben ezeket az adatbázisokat tartalmazza.

   ![Használat lap](./media/backup-azure-configure-backup-reports/usage.png)

* **Feladatok:** Ezen a lapon megtekintheti a feladatok hosszú ideig futó trendjeit, például a sikertelen feladatok napi számát és a feladat sikertelenssőssedésének fő okait. Ezeket az információkat összesítési szinten és biztonsági másolat szintjén is megtekintheti. Jelöljön ki egy adott biztonsági másolat elemet a rácsban, ha meg szeretné tekinteni a kijelölt időtartományban az adott biztonsági másolat elemén aktivált minden egyes feladat részletes adatait.

   ![Feladatok lap](./media/backup-azure-configure-backup-reports/jobs.png)

* **Házirendek:** Ezen a lapon megtekintheti az összes aktív szabályzat adatait, például a társított elemek számát és az adott házirend ben biztonsági másolatban szereplő elemek által felhasznált teljes felhőtárhelyet. Válasszon ki egy adott házirendet az egyes kapcsolódó biztonsági mentési elemek adatainak megtekintéséhez.

   ![Házirendek lap](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportálás Excelbe

Jelölje ki a lefelé mutató nyílgombot bármely widget jobb felső részén, például egy táblázatban vagy diagramban, hogy a widget tartalmát Excel-lapként exportálhassa a meglévő szűrőkkel együtt. Ha egy táblázat több sorát szeretné excelbe exportálni, az egyes rácsok tetején található **Lap/oldal** legördülő nyíllal növelheti az oldalon megjelenő sorok számát.

## <a name="pin-to-dashboard"></a>Rögzítés az irányítópulton

Az egyes widgetek tetején található pin gomb kiválasztásával rögzítheti a widgetet az Azure Portal irányítópultján. Ezzel a funkcióval személyre szabott irányítópultokat hozhat létre, amelyek a legfontosabb információk megjelenítésére vannak szabva.

## <a name="cross-tenant-reports"></a>Több-bérlős jelentések

Ha az [Azure Lighthouse-t](https://docs.microsoft.com/azure/lighthouse/) több bérlői környezetben delegált hozzáféréssel használja, használhatja az alapértelmezett előfizetési szűrőt. Válassza ki a szűrő gombot az Azure Portal jobb felső sarkában az összes olyan előfizetés kiválasztásához, amelyhez adatokat szeretne látni. Ezzel lehetővé teszi, hogy válassza ki a Log Analytics-munkaterületek a bérlők között a több-bérlős jelentések megtekintéséhez.

## <a name="conventions-used-in-backup-reports"></a>A biztonsági mentési jelentésekben használt konvenciók

* A szűrők balról jobbra, felülről lefelé haladnak minden lapon. Ez azt, hogy minden szűrő csak azokra a widgetekre vonatkozik, amelyek vagy az adott szűrő jobb oldalán, vagy az adott szűrő alatt helyezkednek el. 
* A színes csempe kiválasztása szűri a csempe alatti widgeteket az adott csempe értékére vonatkozó rekordok esetében. Ha például a **Biztonsági másolat elemek** lapon a Védelem **leállítva** csempe kiválasztásával szűri az alábbi rácsokat és diagramokat, hogy a Biztonsági mentési elemek adatai megjelenjenek a Védelem leállítva állapotban.
* A nem színes csempék nem kattinthatók.
* Az aktuális részleges nap adatai nem jelennek meg a jelentésekben. Így ha az **Időtartomány** kiválasztott értéke az **Elmúlt 7 nap,** a jelentés az elmúlt hét befejezett nap rekordjait jeleníti meg. Az aktuális napot nem tartalmazza.
* A jelentés a kiválasztott időtartományban *aktivált* feladatok részleteit jeleníti meg (a naplófeladatokon kívül). 
* A **Felhőalapú tároló** és a **védett példányok** megjelenített értékek a kiválasztott időtartomány *végén* találhatók.
* A jelentésekben megjelenő Biztonsági másolat elemek azok az elemek, amelyek a kijelölt időtartomány *végén* találhatók. A kijelölt időtartomány közepén törölt biztonsági másolat elemek nem jelennek meg. Ugyanez a konvenció vonatkozik a biztonsági mentési szabályzatokra is.

## <a name="query-load-times"></a>Lekérdezésbetöltési idők

A biztonsági mentési jelentésben lévő widgeteket a Kusto-lekérdezések biztosítják, amelyek a felhasználó Log Analytics-munkaterületeken futnak. Ezek a lekérdezések általában nagy mennyiségű adat feldolgozását foglalják magukban, több illesztéssel a gazdagabb elemzések érdekében. Ennek eredményeképpen előfordulhat, hogy a widgetek nem töltődnek be azonnal, amikor a felhasználó megtekinti a jelentéseket egy nagy biztonsági mentési állapoton keresztül. Ez a táblázat a különböző widgetek betöltéséhez szükséges idő durva becslését tartalmazza a biztonsági mentési elemek száma és a jelentés megtekintési időtartománya alapján.

| **# Adatforrások**                         | **Időhorizont** | **Hozzávetőleges betöltési idők**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 hónap          | Csempe: 5-10 mp <br> Rácsok: 5-10 mp <br> Térképek: 5-10 mp <br> Jelentésszintű szűrők: 5-10 mp|
| ~5 K                       | 3 hónap          | Csempe: 5-10 mp <br> Rácsok: 5-10 mp <br> Térképek: 5-10 mp <br> Jelentésszintű szűrők: 5-10 mp|
| ~10 K                       | 3 hónap          | Csempe: 15-20 mp <br> Rácsok: 15-20 mp <br> Térképek: 1-2 mins <br> Jelentésszintű szűrők: 25-30 mp|
| ~15 Ezer                       | 1 hónap          | Csempe: 15-20 mp <br> Rácsok: 15-20 mp <br> Térképek: 50-60 mp <br> Jelentésszintű szűrők: 20-25 mp|
| ~15 Ezer                       | 3 hónap          | Csempe: 20-30 mp <br> Rácsok: 20-30 mp <br> Térképek: 2-3 mins <br> Jelentésszintű szűrők: 50-60 mp |

## <a name="what-happened-to-the-power-bi-reports"></a> Mi történt a Power BI-jelentésekkel? 
* A korábbi Power BI-sablonalkalmazás a jelentéskészítéshez, amely adatokat szerzett egy Azure-tárfiókból, egy eprecation elérési úton található. Azt javasoljuk, hogy indítsa el a tároló diagnosztikai adatok küldését a Log Analytics jelentések megtekintéséhez.

* Emellett a V1-es séma diagnosztikai adatok küldése egy tárfiókba vagy a Log Analytics-munkaterület is egy eprúciós elérési út. Ha a V1 sémán alapuló egyéni lekérdezéseket vagy automatizálásokat írt, javasoljuk, hogy frissítse ezeket a lekérdezéseket a jelenleg támogatott V2-séma használatára.

## <a name="next-steps"></a>További lépések
[További információ a figyelésről és a jelentéskészítésről az Azure Backup segítségével](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
