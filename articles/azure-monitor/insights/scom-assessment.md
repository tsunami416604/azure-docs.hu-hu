---
title: A System Center Operations Manager értékelése az Azure Monitor segítségével
description: A System Center Operations Manager állapotfelmérési megoldássegítségével rendszeres időközönként felmérheti a környezetek kockázatát és állapotát.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 94251dfa2d9fa732912ed20d825e64f542d79188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055412"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>A környezet optimalizálása a System Center Operations Manager Health Check (előzetes verzió) megoldással

![System Center Operations Manager állapotellenőrzés szimbóluma](./media/scom-assessment/scom-assessment-symbol.png)

A System Center Operations Manager állapot-ellenőrzési megoldással rendszeres időközönként felmérheti a System Center Operations Manager felügyeleti csoportjának kockázatát és állapotát. Ez a cikk segít a megoldás telepítésében, konfigurálásaés használatában, így korrekciós műveleteket tehet le a lehetséges problémák ra.

Ez a megoldás a telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza. A javaslatok négy kiemelt területre vannak kategorizálva, amelyek segítenek a kockázat gyors megértésében és a korrekciós intézkedések meghozatalában.

A javaslatok a Microsoft mérnökei által több ezer ügyféllátogatás során szerzett tudáson és tapasztalaton alapulnak. Minden egyes javaslat útmutatást nyújt arról, hogy egy probléma miért lehet fontos az Ön számára, és hogyan valósíthatja meg a javasolt módosításokat.

Kiválaszthatja a szervezet számára legfontosabb fókuszterületeket, és nyomon követheti a kockázatmentes és egészséges környezet megmaradása felé tett előrehaladást.

Miután hozzáadta a megoldást, és elvégezte az értékelést, a fókuszterületek összefoglaló információi megjelennek az infrastruktúra **System Center Operations Manager Állapot-ellenőrzési** irányítópultján. Az alábbi szakaszok ismertetik, hogyan használhatja a **System Center Operations Manager állapotfelmérési** irányítópultján található információkat, ahol megtekintheti, majd az Operations Manager környezetben ajánlott műveleteket érhet el.

![A System Center Operations Manager megoldáscsempéje](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager állapotfelmérési irányítópultja](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása

A megoldás együttműködik a Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager és a Microsoft System Központ Operations Manager 1807. A .NET Framework 4.6.2 támogatott verzióját minden felügyeleti kiszolgálóra telepíteni kell.

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

- Az állapot-ellenőrzés megoldás használata előtt a Log Analytics, a megoldás telepítve kell lennie. Telepítse a megoldást az [Azure piactérről.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview)

- Miután hozzáadja a megoldást a munkaterülethez, a **System Center Operations Manager állapotfelmérés** csempéje az irányítópulton egy további konfigurációra vonatkozó szükséges üzenetet jelenít meg. Kattintson a csempére, és kövesse az oldalon említett konfigurációs lépéseket

  ![A System Center Operations Manager irányítópultjának csempéje](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> A System Center Operations Manager konfigurálása parancsfájl használatával végezhető el a megoldás Log Analytics konfigurációs lapján említett lépések végrehajtásával.

 Az értékelés az Operations Manager operations konzolon keresztültörténő konfigurálásához hajtsa végre az alábbi lépéseket a következő sorrendben:
1. [A Futtatás másként fiók beállítása a System Center Operations Manager állapot-ellenőrzéséhez](#operations-manager-run-as-accounts-for-log-analytics)  
2. A System Center Operations Manager állapot-ellenőrzési szabályának konfigurálása

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>A System Center Operations Manager állapot-ellenőrzés által végzett adatgyűjtés részletei

A System Center Operations Manager állapot-ellenőrzési megoldás a következő forrásokból gyűjtadatokat:

* Registry
* Windows Management Instrumentation (WMI)
* Eseménynapló
* Fájladatok
* Közvetlenül az Operations Manager powershell- és SQL-lekérdezések használatával, a megadott felügyeleti kiszolgálóról.  

Az adatokat a felügyeleti kiszolgálón gyűjtjük, és hétnaponta továbbítjuk a Log Analytics szolgáltatásnak.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Futtató Operations Manager-fiókok a Log Analyticshez

A Log Analytics a számítási feladatok felügyeleti csomagjaira épül, hogy értéknövelő szolgáltatásokat nyújtson. Minden számítási feladat hoz munkaterhelés-specifikus jogosultságokat futtatni felügyeleti csomagok egy másik biztonsági környezetben, például egy tartományi felhasználói fiók. Konfigurálja az Operations Manager Futtatás mint fiók kiemelt hitelesítő adatokkal. További információt a [Futtatás másként fiók létrehozása](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) az Operations Manager dokumentációjában című témakörben talál.

Az alábbi információk segítségével állíthatja be az Operations Manager Futtatás másként fiókját a System Center Operations Manager állapotfelméréséhez.

### <a name="set-the-run-as-account"></a>A futtató fiók beállítása

A Futtatás másként fióknak a folytatás előtt meg kell felelnie a következő követelményeknek:

* Olyan tartományi felhasználói fiók, amely a helyi Rendszergazdák csoport tagja az Operations Manager szerepkört támogató kiszolgálókon – felügyeleti kiszolgáló, az operatív, adatraktárt és ACS-adatbázist üzemeltető SQL Server, Jelentéskészítés, Webkonzol és Átjárókiszolgáló.
* Az értékelés alatt álló felügyeleti csoport műveleti vezető rendszergazdai szerepköre
* Ha a fiók nem rendelkezik SQL sysadmin jogosultságokkal, akkor hajtsa végre a [parancsfájlt,](#sql-script-to-grant-granular-permissions-to-the-run-as-account) hogy részletes engedélyeket adjon a fióknak minden olyan SQL Server-példányon, amely az Operations Manager-adatbázisok egy vagy egészét tartalmazza.

1. Az Operations Manager konzolon válassza a **Felügyeleti** navigációs gombot.
2. A **Futtatás konfigurációként csoportban**kattintson a **Fiókok**elemre.
3. A **Futtatás fiókként létrehozása** varázsló **Bevezetés** lapján kattintson a **Tovább**gombra.
4. Az **Általános tulajdonságok** lapon válassza a **Windows** elemet a **Futtatás fióktípusként listában.**
5. Írjon be egy megjelenítendő nevet a **Megjelenítendő név** mezőbe, és tetszés szerint írjon be egy leírást a **Leírás** mezőbe, majd kattintson a **Tovább**gombra.
6. A **Terjesztésbiztonság** lapon válassza a **Biztonságosabb**lehetőséget.
7. Kattintson **a Létrehozás gombra.**  

Most, hogy a Futtatás másként fiók létrejött, meg kell céloznia a felügyeleti kiszolgálókat a felügyeleti csoportban, és egy előre definiált Futtatás mint profilhoz kell társítania, hogy a munkafolyamatok a hitelesítő adatokhasználatával fussanak.  

1. A **Futtatás konfigurációként**csoportban a **Fiókok**csoportban kattintson duplán a korábban létrehozott fiókra.
2. A **Distribution (Terjesztő)** lapon kattintson a **Hozzáadás** a **Kijelölt számítógépekhez** jelölőnégyzetre, és adja hozzá a fiók terjesztéséhez a felügyeleti kiszolgálót.  A módosítások mentéséhez kattintson kétszer az **OK** gombra.
3. A **Futtatás konfigurációként csoportban**kattintson a **Profilok gombra.**
4. Keresse meg az *SCOM értékelési profilját*.
5. A profil neve: *Microsoft System Center Operations Manager Állapot-ellenőrzés Futtatás profilként*.
6. Kattintson a jobb gombbal, és frissítse a tulajdonságait, és adja hozzá a korábban létrehozott Futtatás mint fiókhoz.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Az SQL-szkript, amely részletes engedélyeket biztosít a futtató fiók számára

Hajtsa végre a következő SQL-parancsfájlt, hogy megadja a szükséges engedélyeket a Futtatás másként fiókhoz az operations manager által használt, az operatív, adatraktárt és Az ACS-adatbázist üzemeltető SQL Server-példányon.

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

### <a name="configure-the-health-check-rule"></a>Az állapot-ellenőrzési szabály konfigurálása

A System Center Operations Manager állapotellenőrző megoldás felügyeleti csomagja tartalmaz egy *Microsoft System Center Operations Manager Run Health Check Rule*nevű szabályt. Ez a szabály felelős az állapot-ellenőrzés futtatásáért. A szabály engedélyezéséhez és a gyakoriság konfigurálásához használja az alábbi eljárásokat.

Alapértelmezés szerint a Microsoft System Center Operations Manager állapotellenőrzési szabálya le van tiltva. Az állapot-ellenőrzés futtatásához engedélyeznie kell a szabályt egy felügyeleti kiszolgálón. Ehhez a következő lépések szükségesek.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>A szabály engedélyezése egy adott felügyeleti kiszolgáló számára

1. Az Operations Manager műveleti konzol **Szerzői** munkaterületén keresse meg a *Microsoft System Center Operations Manager Állapotellenőrző szabály futtatására vonatkozó* szabályt a **Szabályok** ablaktáblán.
2. A keresési eredmények között válassza ki azt, amelyik a *Típus: Felügyeleti kiszolgáló*szöveget tartalmazza.
3. Kattintson a jobb gombbal a szabályra, majd kattintson **a Felülbírálások parancsra** > **az osztály egy adott objektumához: Management Server**.
4.  A rendelkezésre álló felügyeleti kiszolgálók listájában válassza ki azt a felügyeleti kiszolgálót, amelyen a szabálynak futnia kell.  Ennek ugyanaznak a felügyeleti kiszolgálónak kell lennie, amelyet korábban konfigurált a Futtatás másként fiók társítására.
5.  Győződjön meg arról, hogy a felülbírálási értéket **True** értékre **módosítja** az Engedélyezett paraméter értékéhez.<br><br> ![paraméter felülbírálása](./media/scom-assessment/rule.png)

    Amíg még ebben az ablakban van, konfigurálja a futtatási gyakoriságot a következő eljárással.

#### <a name="configure-the-run-frequency"></a>A futás gyakoriságának konfigurálása

Az értékelés alapértelmezés szerint 10 080 percenként (vagy hét naponta) fut. Az értéket legalább 1440 perces (vagy egy napos) értékre bírálhatja felül. Az érték az egymást követő értékelési futtatások között szükséges minimális időeltolódást jelenti. Az intervallum felülbírálásához kövesse az alábbi lépéseket.

1. Az Operations Manager konzol **Szerzői** munkaterületén keresse meg a *Microsoft System Center Operations Manager Állapotellenőrzési szabály futtatására vonatkozó* szabályt a **Szabályok** szakaszban.
2. A keresési eredmények között válassza ki azt, amelyik a *Típus: Felügyeleti kiszolgáló*szöveget tartalmazza.
3. Kattintson a jobb gombbal a szabályra, majd kattintson a >  **Szabály felülbírálása parancsra az**osztály összes**objektuma, a Felügyeleti kiszolgáló parancsra.**
4. Módosítsa az **Intervallum** paraméter értékét a kívánt intervallumértékre. Az alábbi példában az érték 1440 percre (egy napra) van állítva.<br><br> ![intervallum paraméter](./media/scom-assessment/interval.png)<br>  

    Ha az érték 1440 percnél rövidebbre van állítva, akkor a szabály egynapos időközönként fut. Ebben a példában a szabály figyelmen kívül hagyja az intervallum értékét, és egy napos gyakorisággal fut.


## <a name="understanding-how-recommendations-are-prioritized"></a>A javaslatok rangsorolásának ismertetése

Minden ajánlás súlyozási értéket kap, amely azonosítja az ajánlás relatív fontosságát. Csak a 10 legfontosabb ajánlások jelennek meg.

### <a name="how-weights-are-calculated"></a>A súlyozás kiszámítása

A súlyozás három fő tényezőn alapuló összesített érték:

- Annak *valószínűsége,* hogy az azonosított probléma problémákat okoz. Nagyobb valószínűséggel felel meg az ajánlás nagyobb összpontszámának.
- A probléma *hatása* a szervezetre, ha problémát okoz. A nagyobb hatás megegyezik a nagyobb általános pontszám az ajánlás.
- Az ajánlás végrehajtásához szükséges *erőfeszítés.* A nagyobb erőfeszítés megegyezik egy kisebb általános pontszám az ajánlás.

Az egyes ajánlások súlyozását az egyes kiemelt területre rendelkezésre álló összpontszám százalékában fejezik ki. Ha például a rendelkezésre állás és az üzletmenet-folytonosság i fókuszterület ének javaslata 5%-os pontszámmal rendelkezik, az ajánlás megvalósítása 5%-kal növeli az általános rendelkezésre állási és üzletmenet-folytonossági pontszámot.

### <a name="focus-areas"></a>Fókuszterületek

**Rendelkezésre állás és üzletmenet-folytonosság** – Ez a kiemelt terület a szolgáltatás rendelkezésre állására, az infrastruktúra rugalmasságára és az üzleti védelemre vonatkozó javaslatokat mutatja be.

**Teljesítmény és méretezhetőség** – Ez a kiemelt terület javaslatokat jelenít meg a szervezet informatikai infrastruktúrájának növekedéséhez, annak biztosításához, hogy az informatikai környezet megfeleljen az aktuális teljesítménykövetelményeknek, és képes legyen reagálni a változó infrastrukturális igényekre.

**Frissítés, áttelepítés és telepítés** – Ez a kiemelt terület javaslatokat tartalmaz az SQL Server frissítésének, áttelepítésének és meglévő infrastruktúrára való telepítésének elősegítésére.

**Műveletek és figyelés** – Ez a kiemelt terület javaslatokat tartalmaz az informatikai műveletek egyszerűsítésére, a megelőző karbantartás megvalósítására és a teljesítmény maximalizálására.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Érdemes 100%-ra törekedni minden fókuszterületen?

Nem feltétlenül. A javaslatok a Microsoft mérnökei által több ezer ügyféllátogatás során szerzett tudáson és tapasztalaton alapulnak. Azonban nincs két kiszolgálói infrastruktúra azonos, és a konkrét javaslatok többé-kevésbé relevánsak lehetnek az Ön számára. Egyes biztonsági javaslatok például kevésbé relevánsak lehetnek, ha a virtuális gépek nincsenek kitéve az internetnek. Egyes rendelkezésre állási javaslatok kevésbé relevánsak lehetnek az alacsony prioritású ad hoc adatgyűjtést és -jelentéskészítést biztosító szolgáltatások esetében. Az érett vállalkozás számára fontos kérdések kevésbé fontosak lehetnek az induló vállalkozások számára. Előfordulhat, hogy meg szeretné határozni, hogy mely fókuszterületek a prioritások, majd nézze meg, hogyan változnak a pontszámok az idő múlásával.

Minden ajánlás útmutatást tartalmaz arról, hogy miért fontos. Ez az útmutató segítségével kiértékelheti, hogy a javaslat megvalósítása megfelelő-e az Informatikai szolgáltatások jellegére és a szervezet üzleti igényeire tekintettel.

## <a name="use-health-check-focus-area-recommendations"></a>Az állapotfelmérés fókuszterületének ajánlásainak használata

Az állapot-ellenőrzési megoldás használata előtt a Log Analytics, a megoldás telepítve kell lennie. A megoldások telepítéséről a [Felügyeleti megoldás telepítése című](../../azure-monitor/insights/solutions.md)témakörben olvashat bővebben. A telepítés után megtekintheti a javaslatok összegzését a System Center Operations Manager állapotfelmérés csempéjével az Azure Portalon a munkaterület **áttekintése lapján.**

Tekintse meg az infrastruktúra összesített megfelelőségi értékeléseit, majd részletezze a javaslatokat.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>A kiemelt területre vonatkozó ajánlások megtekintése és korrekciós intézkedések meghozása
1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a rendszerbe.
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Log Analytics-előfizetések ablaktáblán jelöljön ki egy munkaterületet, majd kattintson a **Munkaterület összesítő** menüpontjára.  
4. Az **Áttekintés** lapon kattintson a **System Center Operations Manager Állapot-ellenőrzési** csempére.
5. A **System Center Operations Manager állapotfelméréslapján** tekintse át az összefoglaló információkat az egyik fókuszterület-panelen, majd kattintson az egyikre az adott fókuszterületre vonatkozó javaslatok megtekintéséhez.
6. A fókuszterület bármelyik oldalán megtekintheti a környezetéhez készített rangsorolt javaslatokat. Kattintson az **Érintett objektumok** csoportban egy javaslatra a javaslat meghirdetésének részleteinek megtekintéséhez.<br><br> ![fókuszterület](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. A **Javasolt műveletek**részben javasolt korrekciós intézkedéseket végrehajthatja. Az elem kezelése után a későbbi értékelések rögzítik, hogy az ajánlott műveleteket végrehajtották, és a megfelelőségi pontszám növekedni fog. A javított elemek **átadott objektumokként**jelennek meg.

## <a name="ignore-recommendations"></a>Javaslatok figyelmen kívül hagyása

Ha olyan javaslatokkal rendelkezik, amelyeket figyelmen kívül szeretne hagyni, létrehozhat egy szövegfájlt, amelyet a Log Analytics használ, hogy megakadályozza a javaslatok megjelenését az értékelési eredményekben.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>A figyelmen kívül hagyni kívánt javaslatok azonosítása
1. Az Azure Portalon a Log Analytics munkaterület lap a kiválasztott munkaterület, kattintson a **Log Search** menüelem.
2. Az alábbi lekérdezéssel felsorolhatja azokat a javaslatokat, amelyek a környezetben lévő számítógépek esetében sikertelenek voltak.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Ha a munkaterület et az [új Log Analytics lekérdezési nyelvre](../../azure-monitor/log-query/log-query-overview.md)frissítették, akkor a fenti lekérdezés a következőre változik.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Az alábbiakban egy képernyőképet mutat a Naplókeresés lekérdezésről:<br><br> ![naplóbeli keresés](./media/scom-assessment/scom-log-search.png)<br>

3. Válassza ki a figyelmen kívül hagyni kívánt javaslatokat. A következő eljárásban az Ajánlásazonosító értékeit fogja használni.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt szövegfájl létrehozása és használata

1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be vagy írja be az egyes javaslatazonosítókat minden olyan javaslathoz, amelyet a Log Analytics figyelmen kívül hagy egy külön sorban, majd mentse és zárja be a fájlt.
3. Helyezze a fájlt a következő mappába minden olyan számítógépen, ahol azt szeretné, hogy a Log Analytics figyelmen kívül hagyja a javaslatokat.
4. Az Operations Manager felügyeleti kiszolgálón - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>A javaslatok figyelmen kívül hagyása

1. A következő ütemezett értékelés futtatása után alapértelmezés szerint hétnaponta a megadott javaslatok figyelmen kívül hagyása, és nem jelennek meg az állapot-ellenőrzés irányítópultján.
2. A következő naplókeresési lekérdezések segítségével listázhatja az összes figyelmen kívül hagyott javaslatot.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Ha a munkaterület et az [új Log Analytics lekérdezési nyelvre](../../azure-monitor/log-query/log-query-overview.md)frissítették, akkor a fenti lekérdezés a következőre változik.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Ha később úgy dönt, hogy figyelmen kívül hagyott javaslatokat szeretne látni, távolítsa el az IgnoreRecommendations.txt fájlokat, vagy távolítsa el belőlük az ajánlási azonosítókat.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager Health Check megoldás – gyakori kérdések

*Hozzáadtam az Állapotfelmérés megoldást a Log Analytics-munkaterülethez. De nem látom az ajánlásokat. miért ne?* A megoldás hozzáadása után a következő lépésekkel tekintse meg a javaslatokat a Log Analytics irányítópulton.  

- [A Futtatás másként fiók beállítása a System Center Operations Manager állapot-ellenőrzéséhez](#operations-manager-run-as-accounts-for-log-analytics)  
- [A System Center Operations Manager állapot-ellenőrzési szabályának konfigurálása](#configure-the-health-check-rule)


*Van mód annak konfigurálására, hogy milyen gyakran fut az ellenőrzés?* Igen. Lásd: [A futtatási gyakoriság konfigurálása](#configure-the-run-frequency).

*Ha a rendszer egy másik kiszolgálót fedez fel a System Center Operations Manager állapot-ellenőrzési megoldás hozzáadása után, akkor a rendszer ellenőrzi?* Igen, a felfedezés után a rendszer ettől kezdve, alapértelmezés szerint hétnaponta ellenőrzi.

*Mi a neve annak a folyamatnak, amely az adatgyűjtést végzi?* TanácsadóÉrtékelése.exe

*Hol fut az AdvisorAssessment.exe folyamat?* Az AdvisorAssessment.exe a felügyeleti kiszolgáló HealthService folyamata alatt fut, ahol az állapotellenőrzési szabály engedélyezve van. Ezzel a folyamattal a teljes környezet felderítése távoli adatgyűjtéssel érhető el.

*Mennyi ideig tart az adatgyűjtés?* Az adatgyűjtés a kiszolgálón körülbelül egy órát vesz igénybe. Hosszabb időt vehet igénybe olyan környezetekben, amelyek számos Operations Manager-példányt vagy adatbázist rendelkeznek.

*Mi a teendő, ha az értékelés időköze 1440 percnél rövidebb revansot tesz ki?* Az értékelés előre konfigurálva van, hogy naponta legfeljebb egyszer fusson. Ha 1440 percnél kisebb értékre bírálja felül az intervallumértéket, akkor az értékelés 1440 percet használ intervallumértékként.

*Hogyan lehet tudni, hogy vannak-e előfeltételek?* Ha az állapot-ellenőrzés futott, és nem látja az eredményeket, akkor valószínű, hogy az ellenőrzés néhány előfeltétele sikertelen volt. A lekérdezéseket: `Operation Solution=SCOMAssessment` `SCOMAssessmentRecommendation FocusArea=Prerequisites` és a Naplókeresés a sikertelen előfeltételek megtekintéséhez.

*Van egy `Failed to connect to the SQL Instance (….).` üzenet az előfeltételhibákban. Mi a probléma?* Az AdvisorAssessment.exe, az adatokat gyűjtő folyamat a HealthService folyamat alatt fut a felügyeleti kiszolgálón. Az állapot-ellenőrzés részeként a folyamat megpróbál csatlakozni ahhoz az SQL-kiszolgálóhoz, amelyen az Operations Manager adatbázis található. Ez a hiba akkor fordulhat elő, ha a tűzfalszabályok blokkolják a kapcsolatot az SQL Server-példánysal.

*Milyen típusú adatokat gyűjtenek?* A rendszer a következő típusú adatokat gyűjti: - WMI-adatok - Rendszerleíró adatbázis - EventLog-adatok – Operations Manager-adatok a Windows PowerShellen, az SQL-lekérdezéseken és a Fájladat-gyűjtőn keresztül.

*Miért kell konfigurálnom a Futtatás másként fiókot?* Az Operations Manager segítségével különböző SQL-lekérdezések futnak. Ahhoz, hogy futtathassák őket, a futtatási fióknak rendelkeznie kell a szükséges engedélyekkel. Ezenkívül a WMI lekérdezéséhez helyi rendszergazdai hitelesítő adatokra van szükség.

*Miért csak a 10 legfontosabb javaslat jelenjen meg?* Ahelyett, hogy a feladatok teljes körű, elsöprő listáját adná meg, javasoljuk, hogy először a rangsorolt ajánlások kezelésére összpontosítson. Miután megszólította őket, további javaslatok válnak elérhetővé. Ha szeretné látni a részletes listát, megtekintheti az összes javaslatot a Naplókeresés használatával.

*Van rá mód, hogy figyelmen kívül hagyja az ajánlást?* Igen, tekintse meg a [Figyelmen kívül hagyása javaslatokat](#ignore-recommendations).


## <a name="next-steps"></a>További lépések

- [A naplókban megtudhatja,](../../azure-monitor/log-query/log-query-overview.md) hogyan elemezheti a System Center Operations Manager állapotfelmérési adatait és javaslatait.
