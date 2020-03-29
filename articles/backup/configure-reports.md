---
title: Azure Backup-jelentések konfigurálása
description: Jelentések konfigurálása és megtekintése az Azure Backup számára a Log Analytics és az Azure-munkafüzetek használatával
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161201"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

A biztonsági mentési rendszergazdák gyakori követelménye, hogy a hosszú ideig tartó adatok alapján betekintést nyerjenak a biztonsági mentésekbe. Egy ilyen megoldáshoz több felhasználási eset is lehet: a felhasznált felhőalapú tárolás lefoglalása és előrejelzése, a biztonsági mentések és -visszaállítások naplózása, valamint a legfontosabb trendek azonosítása a részletesség különböző szintjein.

Ma az Azure Backup olyan jelentéskészítési megoldást kínál, amely kihasználja [az Azure Monitor naplókat](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) és [az Azure-munkafüzeteket,](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)így gazdag betekintést nyerhet a biztonsági mentések teljes biztonsági mentési állapotába. Ez a cikk a biztonsági másolatkészítési jelentések konfigurálását és megtekintését ismerteti.

## <a name="supported-scenarios"></a>Támogatott esetek

* A biztonsági mentési jelentések támogatottak az Azure virtuális gépek, az SQL az Azure virtuális gépek, SAP HANA/ASE az Azure virtuális gépek, az Azure Backup Agent (MARS), az Azure Backup Server (MABS) és a System Center DPM.
* A DPM-számítási feladatok esetében a biztonsági mentési jelentések a DPM 5.1.363.0-s és újabb verziói, valamint a 2.0.9127.0-s vagy újabb verziójú ügynöki verzió konszern számára támogatottak.
* A MABS-számítási feladatok esetében a Biztonsági mentési jelentések a 13.0.415.0-s és újabb verziójú MABS-verzió, valamint a 2.0.9170.0-s vagy újabb verziójú ügynöki verzió esetén támogatottak.
* A biztonsági mentési jelentések megtekinthetők az összes biztonsági mentési elem, tároló, előfizetés és régió között, feltéve, hogy az adatokat egy Log Analytics (LA) munkaterületre küldik, amelyhez a felhasználó hozzáfér. 
* Ha Ön [Azure Lighthouse-felhasználó,](https://docs.microsoft.com/azure/lighthouse/) aki delegált hozzáféréssel rendelkezik az ügyfelek előfizetéseihez, ezeket a jelentéseket az Azure Lighthouse segítségével megtekintheti az összes bérlő jelentései megtekintéséhez.
* A naplóbiztonsági feladatok adatai jelenleg nem jelennek meg a jelentésekben.

## <a name="getting-started"></a>Első lépések

A jelentések használatának első lépéseiaz alábbi három lépésben találhatók:

1. **Log Analytics (LA) munkaterület létrehozása (vagy meglévő használata):**

Be kell állítania egy vagy több LA-munkaterületet a biztonsági mentési jelentési adatok tárolásához. A hely és az előfizetés, ahol ez a LA-munkaterület hozható létre, független attól a helytől és előfizetéstől, ahol a tárolók léteznek. 

Tekintse meg a következő cikket: [Hozzon létre egy Log Analytics-munkaterületet az Azure Portalon](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) egy LA-munkaterület beállításához.

Alapértelmezés szerint az LA-munkaterületen lévő adatok 30 napig megmaradnak. A hosszabb időhorizontadatainak megtekintéséhez módosítsa az LA-munkaterület megőrzési idejét. A megőrzési időszak módosításához tekintse meg a következő cikket: [Használat és költségek kezelése az Azure Monitor naplók.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

2. **Konfigurálja a tárolók diagnosztikai beállításait:**

Az Azure Resource Manager-erőforrások, például a Recovery Services-tárolók, diagnosztikai adatokként rögzítik az ütemezett műveletekre és a felhasználó által aktivált műveletekre vonatkozó információkat. 

A figyelési szakaszban a Recovery Services-tároló, válassza **diagnosztikai beállításokat,** és adja meg a cél a Recovery Services-tároló diagnosztikai adatok. [További információ a diagnosztikai események használatáról.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

![Diagnosztikai beállítások panel](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Az Azure Backup egy beépített Azure-szabályzatot is biztosít, amely automatizálja a diagnosztikai beállítások konfigurálását egy adott hatókör összes tárolójához. A házirend használatát a következő cikkből [megtudhatja: A Vault diagnosztikai beállításainak konfigurálása nagy méretekben](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> A diagnosztika konfigurálása után akár 24 órát is igénybe vehet a kezdeti adatleküldéses művelet befejezése. Ha az adatok beáramlanak az LA-munkaterületre, előfordulhat, hogy nem látja azonnal az adatokat a jelentésekben, mivel az aktuális részleges nap adatai nem jelennek meg a jelentésekben (további részletek [itt).](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports) Ezért javasoljuk, hogy a jelentések megtekintését 2 nappal a tárolók konfigurálása után adatokat küldeni a Log Analytics.

3. **Jelentések megtekintése az Azure Portalon:**

Miután beállította a trezorokat, hogy adatokat küldjön LA-be, tekintse meg a biztonsági mentési jelentéseket úgy, hogy bármely trezor paneljére navigál, és a **Jelentések biztonsági mentése** menüelemre kattint. 

![A tároló irányítópultja](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Erre a hivatkozásra kattintva megnyílik a Biztonságimásolat-jelentés munkafüzete.

> [!NOTE]
> * Jelenleg a jelentés kezdeti terhelése akár 1 percet is igénybe vehet.
> * A Helyreállítási szolgáltatások tároló csupán egy belépési pont a biztonsági mentési jelentések. Miután a Biztonsági másolat jelentések munkafüzet megnyílik egy trezor panel, akkor képes lesz arra, hogy az adatok összesítve az összes boltozat (kiválasztásával a megfelelő la workspaces készlet).

Az alábbiakban a jelentés ben található különböző lapok leírása látható:

* **Összegzés** – Az Összegzés lap magas szintű áttekintést nyújt a biztonsági mentési állapotról. Az Összegzés lapon gyorsáttekintést kaphat a biztonsági mentési elemek teljes számáról, a teljes felhőbeli tárhely felhasználásáról, a védett példányok számáról és a feladat számítási feladatok típusonkénti sikerességi arányáról. Egy adott biztonsági másolat összetevőtípusával kapcsolatos részletesebb információkért keresse meg a megfelelő lapokat.

![Összefoglalás lap](./media/backup-azure-configure-backup-reports/summary.png)

* **Biztonsági másolat elemei** – A Biztonsági másolat elemek lapon megtekintheti a biztonsági másolat elem szintjén felhasznált felhőbeli tárhelyre vonatkozó információkat és trendeket. Ha például sql-t használ az Azure vm biztonsági mentésében, láthatja az egyes SQL-adatbázisok biztonsági mentéséhez felhasznált felhőalapú tárhelyet. Azt is beállíthatja, hogy egy adott védelmi állapotú biztonsági mentési elemek adatai jelenjenek meg. Ha például a lap tetején a **Védelem leállított** csempére kattint, az alábbi widgetek szűrik, hogy csak a Védelmi védelem állapotú biztonsági mentési elemek adatait jelenítse meg.

![Biztonsági másolat elemei lap](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Használat** – A Használat lapon megtekintheti a biztonsági mentések legfontosabb számlázási paramétereit. Az ezen a lapon látható információk számlázási entitás (védett tároló) szintjén. Például abban az esetben, ha egy DPM-kiszolgálóazure-ba készül, megtekintheti a védett példányok és a DPM-kiszolgáló számára felhasznált felhőalapú tárhely trendjét. Hasonlóképpen, ha az SQL az Azure Backup vagy az SAP HANA az Azure Backup, ez a lap a használattal kapcsolatos információkat a virtuális gép szintjén, amelyben ezek az adatbázisok találhatók.

![Használat lap](./media/backup-azure-configure-backup-reports/usage.png)

* **Feladatok** – A Feladatok lapon megtekintheti a feladatok hosszú ideig futó trendjeit, például a sikertelen feladatok napi számát és a feladat sikertelenségedjének fő okait. Ezeket az információkat összesítési szinten és biztonsági másolat szintjén is megtekintheti. Ha egy adott biztonsági másolat elemre kattint a rácsban, részletes információkat tekinthet meg a kijelölt időtartományban az adott biztonságimásolat-elemen aktivált minden feladatról.

![Feladatok lap](./media/backup-azure-configure-backup-reports/jobs.png)

* **Házirendek** – A Házirendek lapon megtekintheti az összes aktív szabályzat adatait, például a társított elemek számát és az adott szabályzat ban biztonsági másolatban szereplő elemek által felhasznált teljes felhőtárhelyet. Ha egy adott házirendre kattint, megtekintheti az egyes kapcsolódó biztonsági mentési elemek adatait.

![Házirendek lap](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exportálás az Excelprogramba

A widget (táblázat/diagram) jobb felső részén található lefelé mutató nyílgombra kattintva az adott widget tartalmát Excel-lapként exportálja, ahogy a meglévő szűrőkkel együtt. Ha egy táblázat több sorát szeretné excelbe exportálni, az egyes rácsok tetején található **Lapnkénti sorok** legördülő menüvel növelheti az oldalon megjelenő sorok számát.

## <a name="pinning-to-dashboard"></a>Rögzítés az irányítópultra

Kattintson az egyes widgetek tetején található Pin ikonra, és rögzítse a widgetet az Azure Portal irányítópultjára. Ez segít testre szabott irányítópultok létrehozásában, amelyek a legfontosabb információk megjelenítésére vannak szabva.

## <a name="cross-tenant-reports"></a>Bérlőközi jelentések

Ha Ön [Azure Lighthouse-felhasználó,](https://docs.microsoft.com/azure/lighthouse/) aki delegált hozzáféréssel rendelkezik az előfizetésekhez több bérlői környezetben, használhatja az alapértelmezett előfizetési szűrőt (az Azure Portal jobb felső részén található szűrőikonra kattintva) kiválaszthatja az összes olyan előfizetést, amelyhez adatokat szeretne látni. Ezzel lehetővé teszi, hogy válassza ki la workspaces a bérlők több-bérlős jelentések megtekintéséhez.

## <a name="conventions-used-in-backup-reports"></a>A biztonsági másolatjelentésekben használt konvenciók

* A szűrők balról jobbra és felülről lefelé haladnak minden lapon, azaz minden szűrő csak azokra a widgetekre vonatkozik, amelyek a szűrő től jobbra vagy a szűrő alatt helyezkednek el. 
* A színes csempére kattintva szűri a csempe alatti widgeteket az adott csempe értékére vonatkozó rekordokhoz. Ha például a Biztonsági másolat elemek lapon a *Védelem leállított* csempére kattint, szűri az alábbi rácsokat és diagramokat, hogy a biztonsági mentési elemek adatai "Védelem leállítva" állapotban jelenjenek meg.
* A nem színes csempék nem kattinthatók.
* Az aktuális részleges nap adatai nem láthatók a jelentésekben. Így, ha a kiválasztott érték az **Időtartomány** értéke, ***Az elmúlt 7 nap,*** a jelentés az elmúlt 7 befejezett nap rekordjait jeleníti meg (amely nem tartalmazza az aktuális napot).
* A jelentés a kiválasztott időtartományban **aktivált** feladatok részleteit jeleníti meg (a naplófeladatokon kívül). 
* A Felhőalapú tároló és a védett példányok megjelenített értékek a kiválasztott időtartomány **végén** találhatók.
* A jelentésekben megjelenő biztonsági másolat elemek azok az elemek, amelyek a kijelölt időtartomány **végén** találhatók. A kijelölt időtartomány közepén törölt biztonsági másolat elemek nem jelennek meg. Ugyanez a beállítás vonatkozik a biztonsági mentési házirendek is.

## <a name="query-load-times"></a>Lekérdezésbetöltési idők

A biztonsági mentési jelentésben található widgeteket a Kusto-lekérdezések biztosítják, amelyek a felhasználó LA-munkaterületeken futnak. Mivel ezek a lekérdezések általában nagy mennyiségű adat feldolgozását foglalják magukban, és több illesztéssel teszik lehetővé a gazdagabb elemzéseket, előfordulhat, hogy a widgetek nem töltődnek be azonnal, amikor a felhasználó jelentéseket tekint meg egy nagy biztonsági mentési állapoton keresztül. Az alábbi táblázat részletes becslést ad arról, hogy a különböző widgetek milyen időt vehetnek igénybe a biztonsági mentési elemek száma és a jelentés megtekintésének időtartománya alapján:

| **# Adatforrások**                         | **Időhorizont** | **Betöltési idők (kb.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 hónap          | Csempe: 5-10 mp <br> Rácsok: 5-10 mp <br> Térképek: 5-10 mp <br> Jelentésszintű szűrők: 5-10 mp|
| ~5 K                       | 3 hónap          | Csempe: 5-10 mp <br> Rácsok: 5-10 mp <br> Térképek: 5-10 mp <br> Jelentésszintű szűrők: 5-10 mp|
| ~10 K                       | 3 hónap          | Csempe: 15-20 mp <br> Rácsok: 15-20 mp <br> Térképek: 1-2 mins <br> Jelentésszintű szűrők: 25-30 mp|
| ~15 Ezer                       | 1 hónap          | Csempe: 15-20 mp <br> Rácsok: 15-20 mp <br> Térképek: 50-60 mp <br> Jelentésszintű szűrők: 20-25 mp|
| ~15 Ezer                       | 3 hónap          | Csempe: 20-30 mp <br> Rácsok: 20-30 mp <br> Térképek: 2-3 mins <br> Jelentésszintű szűrők: 50-60 mp |

## <a name="what-happened-to-the-power-bi-reports"></a>Mi történt a Power BI-jelentésekkel?
* Korábbi Power BI-sablonalkalmazásunk a jelentéskészítéshez (amely egy Azure Storage-fiókból származó adatokat szerzett) egy eprecation elérési úton található. Javasoljuk, hogy indítsa el a tároló diagnosztikai adatok küldését a Log Analytics a fent leírt módon, a jelentések megtekintéséhez.

* Emellett a Nosztikaadatok tárfiókba vagy LA-munkaterületre történő küldésének V1-es sémája is egy eprúciós útvonalon van. Ez azt jelenti, hogy ha a V1 sémán alapuló egyéni lekérdezéseket vagy automatizálásokat írt, javasoljuk, hogy frissítse ezeket a lekérdezéseket a jelenleg támogatott V2-séma használatára.

## <a name="next-steps"></a>További lépések
[További információ a figyelésről és a jelentéskészítésről az Azure Backup segítségével](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)