---
title: Optimalizálhatja a System Center Operations Manager-környezetet az Azure Log Analyticsszel |} A Microsoft Docs
description: A System Center Operations Manager állapotának ellenőrzése megoldás segítségével felmérheti a kockázatait és állapotát, a környezetek rendszeres időközönkénti.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: magoedte
ms.openlocfilehash: 27b55af74a713c51655891df8c852ff44cd3744a
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621770"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimalizálhatja a környezetet a System Center Operations Manager állapotának ellenőrzése (előzetes verzió) megoldás

![A System Center Operations Manager állapotának ellenőrzése szimbólum](./media/scom-assessment/scom-assessment-symbol.png)

A System Center Operations Manager állapotának ellenőrzése megoldás segítségével felmérheti a kockázatokat és a System Center Operations Manager felügyeleti csoport állapotának rendszeres időközönkénti. Ez a cikk segítséget nyújt a telepítését, konfigurálását és a megoldás használatához, hogy a potenciális problémákat korrekciós műveleteket hajthatja végre.

Ez a megoldás a telepített kiszolgálói infrastruktúrát vonatkozó javaslatok rangsorolt listáját tartalmazza. Az ajánlások szerint vannak kategóriába sorolva négy különböző fókuszterületre vonatkozóan, amely segítségével gyorsan megismerheti a kockázatokat, és korrekciós műveletek.

Javaslatok útmutatóként tudással és ezer vevő a Microsoft mérnökei tapasztalatai alapulnak. Minden javaslat ismerteti, miért számít a probléma előfordulhat, hogy Önnek és a javasolt változások megvalósítása.

Kiválaszthatja, hogy a szervezet számára legfontosabb, és nyomon követheti az előrehaladást kockázati ingyenes és kifogástalan állapotú környezetét fókuszterületek.

Után, a megoldáshoz hozzáadott értékelés elvégezni, összefoglaló adatait fókuszterületek jelenik meg a **System Center Operations Manager állapotának ellenőrzése** irányítópult-infrastruktúrája számára. A következő szakaszok ismertetik az információk használata az a **System Center Operations Manager állapotának ellenőrzése** irányítópult, melyen megtekintheti, és megfelelő ajánlott műveletek az Operations Manager környezetben.

![A System Center Operations Manager megoldás csempe](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager Health Check dashboard](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása

A megoldás együttműködik a Microsoft System Center 2012 Operations Manager Service Pack 1, a Microsoft System Center 2012 R2 Operations Manager, a Microsoft System Center 2016 Operations Manager, a Microsoft System Center 2016 Operations Manager és a Microsoft System Az Operations Managerben 1807

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

- A Health Check megoldás a Log Analytics használata előtt rendelkeznie kell a telepített megoldás. Telepítse a megoldás a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- A munkaterületet, a megoldás hozzáadását követően a **System Center Operations Manager állapotának ellenőrzése** az irányítópulton lévő csempe további konfiguráció szükséges üzenetet jeleníti meg. Kattintson a csempére, és kövesse a lapon szereplő konfigurációs lépéseket

  ![A System Center Operations Manager irányítópult-csempe](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> A System Center Operations Manager konfigurációs teheti meg a megoldás a Log Analytics konfiguráció lapján szereplő lépéseket követve parancsfájl használatával.

 Az Operations Manager operatív konzolján keresztül értékelés konfigurálásához a következő sorrendben hajtsa végre az alábbi lépéseket:
1. [A futtató fiók beállítása a System Center Operations Manager állapotának ellenőrzése](#operations-manager-run-as-accounts-for-log-analytics)  
2. A System Center Operations Manager állapotának ellenőrzése szabály konfigurálása

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>A System Center Operations Manager állapotának ellenőrzése adatok gyűjtése részletei

A System Center Operations Manager állapotának ellenőrzése megoldás a következő forrásokból gyűjt adatokat:

* Beállításjegyzék
* Windows Management Instrumentation (WMI)
* Eseménynapló
* Fájladatok
* Közvetlenül az Operations Manager használatával a PowerShell és az SQL-lekérdezéseket, Ön által megadott felügyeleti kiszolgálóról.  

Adatok a felügyeleti kiszolgálón gyűjti, és továbbítja a Log Analytics hét naponta.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Az Operations Manager futtató fiókokat a Log Analytics

Felügyeleti csomagok számítási feladatokhoz biztosít a log Analytics épít értéknövelt szolgáltatásokat. Minden számítási feladathoz munkaterhelés-specifikus jogosultságokkal eltérő biztonsági környezetben, például a tartományi felhasználói fiók felügyeleti csomagjainak futtatásához szükséges. Konfigurálja az Operations Manager futtató fiókja jogosultságokat biztosító hitelesítő adatokhoz. További információkért lásd: [egy futtató fiók létrehozása](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) az Operations Manager dokumentációjában.

Az alábbi információk segítségével állítsa be az Operations Manager futtató fiókot a System Center Operations Manager állapotának ellenőrzése.

### <a name="set-the-run-as-account"></a>A futtató fiók beállítása

A futtató fiókot a következő követelményeket a folytatás előtt kell megfelelnie:

* Egy tartományi felhasználói fiók, amely tagja a helyi Rendszergazdák csoport összes kiszolgálójára támogató bármilyen Operations Manager-szerepkör - felügyeleti kiszolgáló, SQL Server kiszolgálót, az operatív, adatraktár és ACS-adatbázis, webalkalmazás-konzolhoz, és átjárókiszolgáló Reporting,.
* A felügyeleti csoport kiszabott művelet Manager rendszergazda szerepkör
* Ha a fiók nem rendelkezik SQL-rendszergazdai jogosultságokkal, majd hajtsa végre a [parancsfájl](#sql-script-to-grant-granular-permissions-to-the-run-as-account) részletes engedélyezés minden egyes vagy az Operations Manager adatbázisait üzemeltető SQL Server-példány a fiókjához.

1. Az Operations Manager konzolon válassza a **felügyeleti** navigációs gombra.
2. A **futtató konfiguráció**, kattintson a **fiókok**.
3. Az a **futtató fiók létrehozása** varázsló, a a **bemutatása** lapon kattintson **tovább**.
4. Az a **általános tulajdonságok** lapon jelölje be **Windows** a a **futtató fiók típusa:** listája.
5. Írja be a megjelenítendő nevet a a **megjelenítendő név** szöveg mezőbe, majd igény szerint adjon meg egy leírást a a **leírás** mezőbe, majd kattintson a **tovább**.
6. Az a **terjesztési biztonsági** lapon jelölje be **biztonságosabb**.
7. Kattintson a **Create** (Létrehozás) gombra.  

Most, hogy a futtató fiók jön létre, a cél felügyeleti kiszolgálókat a felügyeleti csoportban kell, és egy előre meghatározott futtató profilhoz társított, így a munkafolyamatok futnak-e a hitelesítő adatok használatával.  

1. A **futtató konfiguráció**, **fiókok**, az eredmények ablaktáblán kattintson duplán a korábban létrehozott fiókot.
2. Az a **terjesztési** lapra, majd **Hozzáadás** számára a **kijelölt számítógépek** mezőbe, majd adja hozzá a felügyeleti kiszolgáló és a fiók terjesztését.  Kattintson a **OK** gombra kétszer a módosítások mentéséhez.
3. A **futtató konfiguráció**, kattintson a **profilok**.
4. Keresse meg a *SCOM értékelés profil*.
5. A profil nevének kell lennie: *A Microsoft System Center Operations Manager Health Check futtató profil*.
6. Kattintson a jobb gombbal, és a tulajdonságait frissíteni, és adja hozzá a legutóbb létrehozott futtató fiókot a korábban létrehozott.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Részletes engedélyezés a futtató fiókot az SQL-szkript

Hajtsa végre a következő SQL-szkriptet a szükséges engedélyeket a futtató fiókot az Operations Manager üzemeltetési az operatív, adatraktár és az ACS-adatbázis által használt SQL Server-példányon.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Az állapot ellenőrzése szabály konfigurálása

A System Center Operations Manager állapotának ellenőrzése megoldási felügyeleti csomag tartalmaz egy szabályt, nevű *Microsoft Center Operations Manager futtatása állapotának ellenőrzése Rendszerszabály*. Ez a szabály feladata az állapot-ellenőrzés futtatása. A szabály engedélyezése és a gyakoriság beállítása, kövesse az alábbi eljárásokat.

A Microsoft Center Operations Manager futtatása állapotának ellenőrzése Rendszerszabály alapértelmezés szerint le van tiltva. Az állapot-ellenőrzés futtatása, engedélyeznie kell a szabály a felügyeleti kiszolgálón. Kövesse az alábbi lépéseket.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>A szabály egy adott felügyeleti kiszolgáló engedélyezése

1. Az a **szerzői műveletek** munkaterületen keresse meg a szabály az Operations Manager operatív konzol *Microsoft Center Operations Manager futtatása állapotának ellenőrzése Rendszerszabály* a a **szabályok** ablaktáblán.
2. A keresési eredmények között, válassza ki a szöveget tartalmazó *típusa: Felügyeleti kiszolgáló*.
3. Kattintson a jobb gombbal a szabályt, és kattintson a **felülbírálások** > **osztály egy adott objektumához: Felügyeleti kiszolgáló**.
4.  Az elérhető felügyeleti kiszolgálók listában válassza ki a felügyeleti kiszolgáló, ahol a szabályt kell futnia.  Ez lehet társítani a futtató fiókot a korábban beállított azonos felügyeleti kiszolgálóhoz.
5.  Győződjön meg arról, hogy a felülbírálás értékét módosítja **igaz** számára a **engedélyezve** paraméter értéke.<br><br> ![bírálja felül a paraméter](./media/scom-assessment/rule.png)

    Miközben továbbra is ezt az ablakot, konfigurálja a következő eljárással futtatási gyakorisága.

#### <a name="configure-the-run-frequency"></a>A futtatási gyakorisága konfigurálása

Alapértelmezés szerint az értékelés futtatása minden 10 080 perc (vagy a hét napja) van konfigurálva. A minimális értékét 1440 perc (vagy egy nap) felül lehet bírálni. Az érték az egymást követő assessment futtatásai között szükséges minimális kihagyást jelöli. Bírálja felül az időközt, használja az alábbi lépéseket.

1. Az a **szerzői műveletek** munkaterületen az Operations Manager-konzolon keresse meg a szabály *Microsoft Center Operations Manager futtatása állapotának ellenőrzése Rendszerszabály* a a **szabályok** a szakasz.
2. A keresési eredmények között, válassza ki a szöveget tartalmazó *típusa: Felügyeleti kiszolgáló*.
3. Kattintson a jobb gombbal a szabályt, és kattintson a **szabály felülbírálása** > **a következő osztály összes objektumához: Felügyeleti kiszolgáló**.
4. Módosítsa a **időköz** paraméterérték a kívánt értéket. Az alábbi példában a értéke 1440 perc (egy nap).<br><br> ![intervallum paraméter](./media/scom-assessment/interval.png)<br>  

    Ha az értéke kisebb, mint 1440 perc, a szabály az adott egy nap alatt futtat. Ebben a példában a szabály figyelmen kívül hagyja az intervallum értéke, és a egy nap gyakorisággal futtatja.


## <a name="understanding-how-recommendations-are-prioritized"></a>Hogyan kerülnek előrébb a javaslatok megértése

Minden végrehajtott javaslat, amely azonosítja a relatív fontosságát az ajánlás súlyozási értéket kap. A 10 legfontosabb javaslatok láthatók.

### <a name="how-weights-are-calculated"></a>Hogyan számítják ki súlyok

Súlyozás három fő tényezők alapján az összesített értékek:

- A *valószínűségi* , hogy az azonosított probléma problémákat okozhat. Nagyobb a valószínűsége annak használatára, az ajánlás nagyobb általános pontszámot.
- A *hatás* a szervezet azt problémát okozni, ha a probléma. Egy újabb hatás-adatbázisnak felel meg az ajánlás nagyobb általános pontszámot.
- A *erőfeszítés* történő megvalósításához szükséges. A javaslat egy kisebb összesített pontszám magasabb erőfeszítéssel állapotnak felel meg.

Az egyes javaslatok a súlyozás van a teljes elérhető egyes fókuszterület pontszám százalékban kifejezve. Például ha az adott javaslat a rendelkezésre állás és üzleti folytonosság fókuszterület 5 %-os pontszámot, a javaslat megvalósítása növeli az általános rendelkezésre állás és üzleti folytonosság pontszám szerint 5 %.

### <a name="focus-areas"></a>Fókuszterületek

**Rendelkezésre állás és üzleti folytonosság** -e fókuszterület javaslatait a szolgáltatás rendelkezésre állása, az infrastruktúra és a vállalati védelme rugalmasságát mutatja be.

**Teljesítmény és méretezhetőség** -e fókuszterület látható ajánlásokat a szervezet IT-infrastruktúráját a növeléssel, ellenőrizze, hogy informatikai környezete megfelel az aktuális teljesítménykövetelményeknek, és tud válaszolni az infrastruktúra módosítása szüksége van.

**Frissítés, Migrálás és üzembe helyezési** -e fókuszterület segítséget frissítése, migrálása és üzembe helyezése az SQL Server a meglévő infrastruktúra javaslatait mutatja be.

**Műveletek és a figyelés** -e fókuszterület leegyszerűsítheti az informatikai műveletek, és megelőző jellegű karbantartás végrehajtása és a teljesítmény maximalizálása érdekében javaslatait mutatja be.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Tárhelyeken minden fókuszterület a 100 %-os pontozása?

Nem feltétlenül. A javaslatok tudással és több ezer ügyfél által a Microsoft mérnökei szerzett tapasztalatok alapulnak. Azonban nem két kiszolgáló infrastruktúrák azonosak, és lehet, hogy több vagy kevesebb releváns konkrét javaslatokért. Például bizonyos biztonsági javaslatok kevésbé fontos, ha a virtuális gépek nem jelennek meg az interneten a lehet. Lehet, hogy egyes rendelkezésre állási javaslatok kevesebb releváns alacsony prioritású alkalmi adatok összegyűjtésére és a reporting services esetén. Érett fontos problémák a kevésbé fontos, hogy egy indítási is lehet. Előfordulhat, hogy szeretné azonosítani, melyik fókuszterületek a munkájukhoz, és keresse meg, hogyan a pontszámokat az idő előrehaladtával változik.

Minden javaslat, hogy miért fontos vonatkozó útmutatást tartalmaz. Ez az útmutató segítségével kiértékelheti, hogy-e a javaslat megvalósítása megfelelő, az informatikai szolgáltatások jellegét, és a szervezet igényeit.

## <a name="use-health-check-focus-area-recommendations"></a>Használat állapotának ellenőrzése fókusz területre vonatkozó javaslatok

A health ellenőrzési megoldás a Log Analytics használata előtt rendelkeznie kell a telepített megoldás. További megoldások telepítéséről lásd: [management megoldás telepítése](../../azure-monitor/insights/solutions.md). Azt követően, javaslatok összegzését a System Center Operations Manager állapotának ellenőrzése csempe használatával megtekintheti a **áttekintése** oldalán a munkaterület az Azure Portalon.

Az összesített megfelelőségi értékeléseket az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Egy fókuszterület javaslatok megtekintése, és korrekciós műveletek
1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Log Analytics-előfizetések ablaktábláján válasszon egy munkaterületet, majd kattintson a **munkaterület összefoglalás** menüpontot.  
4. Az a **áttekintése** lap, kattintson a **System Center Operations Manager állapotának ellenőrzése** csempére.
5. Az a **System Center Operations Manager állapotának ellenőrzése** lapon. tekintse át az összefoglaló adatokat az egyik a fókusz terület paneleket, majd kattintson egy adott fókuszterület javaslatok megtekintése.
6. A bármelyik, fókusz terület megtekintheti a rangsorolt javaslatok arról, hogy a környezetben. Alatt egy javaslatra kattint **érintett objektumok** Miért jön létre a javaslat részleteinek megtekintéséhez.<br><br> ![fókuszterület](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Az ajánlott javítási műveleteket hajthatja végre **javasolt műveletek**. Az elem történnek, ha újabb értékelések, amely ajánlott műveletek származnak, és növeli a megfelelőségi pontszám lesz felvéve. Kijavított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Hagyja figyelmen kívül a javaslatok

Ha javaslatoknál, amelyeket figyelmen kívül szeretne, létrehozhat egy szöveges fájl, amelyet a Log Analytics alkalmaz, hogy elkerülje a javaslatokat az értékelés eredményeinek parancsot.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Figyelmen kívül hagyása kívánt javaslatok azonosításához
1. Az Azure Portalon a kijelölt munkaterület a Log Analytics munkaterület oldalon, kattintson a **naplóbeli keresés** menüpontot.
2. A következő lista ajánlásokat, amelyek nem tudták lekérdezés használata a környezetében.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Ha a munkaterülete frissítve lett a [Log Analytics új lekérdezési nyelvre](../../azure-monitor/log-query/log-query-overview.md), akkor a fenti lekérdezés módosulnak az alábbiak.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Íme egy Képernyőkép a Naplókeresési lekérdezésen:<br><br> ![naplóbeli keresés](./media/scom-assessment/scom-log-search.png)<br>

3. Válassza ki a javaslatok, amelyek figyelmen kívül kívánja. Szeretné használni az értékeket a RecommendationId az alábbi eljárás írja le.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozhat létre és használhat egy IgnoreRecommendations.txt szövegfájl

1. Hozzon létre egy fájlt IgnoreRecommendations.txt.
2. Illessze be, vagy írjon be minden egyes RecommendationId minden javaslat, amelyet a Log Analyticsben, hogy figyelmen kívül, külön sorban, majd mentse és zárja be a fájlt.
3. Helyezze a fájlt a következő mappában minden olyan számítógépen a Log Analytics figyelmen kívül hagyja a javaslatok, ahová.
4. Az Operations Manager felügyeleti kiszolgálón – *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft.

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy a javaslatok figyelmen kívül hagyja a

1. Után a következő ütemezett értékelési fut le, alapértelmezés szerint minden hét nap, a megadott ajánlásokat vannak megjelölve az figyelmen kívül hagyva, és nem jelennek meg az állapot-ellenőrzés irányítópultjának.
2. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listája.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Ha a munkaterülete frissítve lett a [Log Analytics új lekérdezési nyelvre](../../azure-monitor/log-query/log-query-overview.md), akkor a fenti lekérdezés módosulnak az alábbiak.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott javaslatok, IgnoreRecommendations.txt fájlok eltávolítása, vagy távolíthatja el RecommendationIDs őket.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager állapotának ellenőrzése megoldás – gyakori kérdések

*A Health Check megoldás hozzáadni a Log Analytics-munkaterület De a javaslatok nem látható. miért ne?* A megoldás hozzáadása után a következő lépések nézet a javaslatok használata a Log Analytics-irányítópulton.  

- [A futtató fiók beállítása a System Center Operations Manager állapotának ellenőrzése](#operations-manager-run-as-accounts-for-log-analytics)  
- [A System Center Operations Manager állapotának ellenőrzése szabály konfigurálása](#configure-the-health-check-rule)


*Van mód konfigurálása, hogy milyen gyakran az ellenőrzés fut?* Igen. Lásd: [futtatási gyakoriságát](#configure-the-run-frequency).

*Ha egy másik kiszolgáló fel van derítve, miután hozzá van adva a System Center Operations Manager állapotának ellenőrzése megoldást, azt ellenőrzött?* Igen, ez be van jelölve ettől kezdve az alapértelmezés szerint minden hét nap felderítése után.

*Mi a neve, a folyamat, amely az adatgyűjtés?* AdvisorAssessment.exe

*Ha fut a AdvisorAssessment.exe folyamat?* AdvisorAssessment.exe a felügyeleti kiszolgáló Állapotfigyelő szolgáltatás folyamatban fut, ahol az egészségügyi ellenőrzési szabály engedélyezve van. A folyamat használ, a teljes környezet felderítését a gazdafájlon keresztül távoli adatgyűjtés.

*Mennyi ideig tart az adatgyűjtés?* A kiszolgálón az adatgyűjtés körülbelül egy órát vesz igénybe. Ez hosszabb időt vehet igénybe, amelyek több Operations Manager-példányok vagy adatbázisok környezetekben.

*Mi történik, ha szeretnék beállítani az időközt az értékelés kevesebb mint 1440 perc alatt?* Az értékelés előre konfigurálva a futtatását, hogy legfeljebb naponta egyszer. Ha kevesebb mint 1440 perc felülírja az intervallum értéke értékre, majd az értékelés használja az intervallum értéke 1440 perc.

*Hogyan lehet tudni, hogy vannak-e előfeltétel-ellenőrzési hibák?* Ha az állapot-ellenőrzés futtatása, és nem látja az eredményeket, majd valószínű, hogy néhány előfeltétel-ellenőrzés sikertelen volt. Lekérdezéseket hajthat végre: `Operation Solution=SCOMAssessment` és `SCOMAssessmentRecommendation FocusArea=Prerequisites` a Naplókeresésben megtekintéséhez az előfeltételek nem teljesülnek.

*Van egy `Failed to connect to the SQL Instance (….).` üzenet az előfeltétel-ellenőrzési hibák. Mi a probléma?* AdvisorAssessment.exe, a folyamat, amely gyűjti az adatokat, az állapotfigyelő szolgáltatás folyamatban fut, a felügyeleti kiszolgálón. A folyamat részeként az állapot-ellenőrzés a próbál meg csatlakozni az SQL Server, amelyen megtalálható az Operations Manager-adatbázis. Ez a hiba akkor fordulhat elő, ha tűzfalszabályok, tiltsa le a kapcsolatot az SQL Server-példányra.

*Milyen típusú adatokat gyűjt?* A következő típusú adatokat gyűjtjük: – WMI-adatok – beállításjegyzék - eseménynapló-adatok – adatok az Operations Manager adatainak Windows PowerShell, az SQL-lekérdezések és a fájl információkat gyűjtő keresztül.

*Miért van a futtató fiók konfigurálása?* Az Operations Managerrel az SQL-lekérdezések futnak. Ahhoz, hogy futtatni egy futtató fiókot kell használnia, szükséges engedélyekkel. Emellett helyi rendszergazdai hitelesítő adatok szükségesek a WMI-lekérdezés.

*Miért jelennek meg a csak az első 10-javaslatok?* Helyett, így egy teljes körű, elsöprő feladatok listáját, azt javasoljuk, hogy arra összpontosítunk, először azoknak a rangsorolt javaslatok. Oldja meg őket, miután további javaslatokat is elérhetőek lesznek. Ha inkább a részletes listát, összes ajánlás naplóbeli keresés használatával tekintheti meg.

*Van mód figyelmen kívül hagyja a javaslatot?* Igen, tekintse meg a [figyelmen kívül hagyja a javaslatok](#ignore-recommendations).


## <a name="next-steps"></a>További lépések

- [Naplók keresése](../../azure-monitor/log-query/log-query-overview.md) megtudhatja, hogyan elemezheti a System Center Operations Manager állapotának ellenőrzése a részletes adatok és javaslatok.
