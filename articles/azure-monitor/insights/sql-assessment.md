---
title: Sql Server környezet ének optimalizálása az Azure Monitor segítségével | Microsoft dokumentumok
description: Az Azure Monitor segítségével az SQL állapot-ellenőrzés megoldás segítségével rendszeres időközönként felmérheti a környezetek kockázatát és állapotát.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/28/2019
ms.openlocfilehash: ceaed0800df01bf2c44fee13d98b01b6e726200d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662484"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Az SQL-környezet optimalizálása az Azure Monitor SQL Server állapotellenőrző megoldásával

![SQL állapot-ellenőrzés szimbólum](./media/sql-assessment/sql-assessment-symbol.png)

Az SQL Állapot-ellenőrzés megoldás segítségével rendszeres időközönként felmérheti a kiszolgálói környezetek kockázatát és állapotát. Ez a cikk segít a megoldás telepítésében, hogy korrekciós intézkedéseket tehessen a lehetséges problémák esetén.

Ez a megoldás a telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza. Az ajánlások hat kiemelt területre vannak kategorizálva, amelyek segítenek a kockázat gyors megértésében és a korrekciós intézkedések meghozatalában.

A javaslatok a Microsoft mérnökei által több ezer ügyféllátogatás során szerzett tudáson és tapasztalaton alapulnak. Minden egyes javaslat útmutatást nyújt arról, hogy egy probléma miért lehet fontos az Ön számára, és hogyan valósíthatja meg a javasolt módosításokat.

Kiválaszthatja a szervezet számára legfontosabb fókuszterületeket, és nyomon követheti a kockázatmentes és egészséges környezet megmaradása felé tett előrehaladást.

Miután hozzáadta a megoldást, és egy értékelés befejeződött, a fókuszterületek összefoglaló információi megjelennek a környezetében lévő infrastruktúra **SQL állapot-ellenőrzési** irányítópultján. Az alábbi szakaszok ismertetik, hogyan használhatja az SQL **Állapot-ellenőrzés** irányítópulton található információkat, ahol megtekintheti, majd végrehajthatja az SQL Server-infrastruktúrához ajánlott műveleteket.

![Az SQL Állapot-ellenőrzés csempe képe](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![Az SQL Állapot-ellenőrzés irányítópultképe](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Előfeltételek

* Az SQL Állapot-ellenőrzési megoldáshoz a .NET Framework 4.6.2 támogatott verziója szükséges minden olyan számítógépen, amelyen telepítve van a Microsoft Monitoring Agent (MMA).  Az MMA-ügynököt a System Center 2016 – Operations Manager and Operations Manager 2012 R2 és az Azure Monitor használja.  
* A megoldás támogatja az SQL Server 2012-es, 2014-es és 2016-os verzióját.
* A Log Analytics-munkaterület az SQL állapot-ellenőrzés megoldás hozzáadása az Azure-piactérről az Azure Portalon.  A megoldás telepítéséhez rendszergazdai vagy közreműködőinek kell lennie az Azure-előfizetésben.

  > [!NOTE]
  > A megoldás hozzáadása után az AdvisorAssessment.exe fájl hozzáadódik az ügyintézőkkel rendelkező kiszolgálókhoz. A rendszer beolvassa a konfigurációs adatokat, majd elküldi az Azure Monitornak feldolgozásra. A felhőszolgáltatás egy logikát alkalmaz a kapott adatokon, és rögzíti azokat.
  >
  >

Az SQL Server-kiszolgálók állapot-ellenőrzésének elvégzéséhez ügynökre és kapcsolatra van szükségük az Azure Monitorhoz az alábbi támogatott módszerek egyikével:

1. Telepítse a [Microsoft Monitoring Agent (MMA) alkalmazást,](../../azure-monitor/platform/agent-windows.md) ha a kiszolgálót még nem figyeli a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2.
2. Ha a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2 szolgáltatással figyeli, és a felügyeleti csoport nincs integrálva az Azure Monitorszolgáltatással, a kiszolgáló több-homed a Log Analytics adatok gyűjtésére és a szolgáltatáshoz való továbbításhoz, és továbbra is az Operations Manager felügyeli.  
3. Ellenkező esetben, ha az Operations Manager felügyeleti csoport integrálva van a szolgáltatással, hozzá kell adnia a tartományvezérlőket a szolgáltatás általi adatgyűjtéshez az [ügynök által felügyelt számítógépek hozzáadása](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) című részben leírt lépéseket követve, miután engedélyezte a megoldást a munkaterületen.  

Az SQL Server azon ügynöke, amely jelentést tesz egy Operations Manager felügyeleti csoportnak, adatokat gyűjt, továbbítja a hozzárendelt felügyeleti kiszolgálóra, majd közvetlenül a felügyeleti kiszolgálóról küldi el az Azure Monitornak.  Az adatok nem lesznek beírva az Operations Manager-adatbázisokba.  

Ha az Operations Manager figyeli az SQL Server kiszolgálót, konfigurálnia kell egy Operations Manager Futtatás másként fiókot. További információért lásd alább [az Operations Manager futtatási fiókjait az Azure Monitorhoz.](#operations-manager-run-as-accounts-for-log-analytics)

## <a name="sql-health-check-data-collection-details"></a>Az SQL Health Check által végzett adatgyűjtés részletei
Az SQL Állapotfelmérés a következő forrásokból gyűjtadatokat az engedélyezett ügynök használatával:

* Windows Management Instrumentation (WMI)
* Registry
* Teljesítményszámlálók
* SQL Server dinamikus felügyeleti nézeteredményei

Az adatokat az SQL Server en gyűjtik, és hétnaponta továbbítják a Log Analytics szolgáltatásnak.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Futtató Operations Manager-fiókok a Log Analyticshez
A Log Analytics az Operations Manager-ügynök és felügyeleti csoport segítségével gyűjti és küldi el az adatokat a Log Analytics szolgáltatásnak. A Log Analytics a számítási feladatok felügyeleti csomagjaira épül, hogy értéknövelő szolgáltatásokat nyújtson. Minden számítási feladat hoz munkaterhelés-specifikus jogosultságokat futtatni felügyeleti csomagok egy másik biztonsági környezetben, például egy tartományi felhasználói fiók. Az Operations Manager Futtatás másként fiók konfigurálásával meg kell adnia a hitelesítő adatokat.

Az alábbi információk segítségével állítsa be az Operations Manager Futtatás másként fiókját az SQL Állapot-ellenőrzéshez.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Az SQL Health Check futtató fiókjának beállítása
 Ha már használja az SQL Server felügyeleti csomagot, akkor ezt a Futtatás másként konfigurációt kell használnia.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Az SQL Run As fiók konfigurálása az operatív konzolon
> [!NOTE]
> A felügyeleti csomag ban alapértelmezés szerint a munkafolyamatok a Helyi rendszer fiók biztonsági környezetében futnak. Ha a Microsoft Monitoring Agent közvetlenül a szolgáltatáshoz csatlakozik, és nem közvetlenül az Operations Manager felügyeleti csoportjának jelent, hagyja ki az alábbi 1-5. felhasználónevét.
>
>

1. Az Operations Manager ben nyissa meg az operatív konzolt, majd kattintson a **Felügyelet gombra.**
2. A **Futtatás konfigurációként csoportban**kattintson a **Profilok**elemre, és nyissa meg az SQL Assessment Run As **Profile (SQL Assessment Run As Profile ) (Sql Assessment Run As Profile ) (Futtatás profilként) csoportban.**
3. A **Futtató fiókok** lapon kattintson **Hozzáadás** lehetőségre.
4. Jelöljön ki egy Windows Futtatás másként fiókot, amely tartalmazza az SQL Server hez szükséges hitelesítő adatokat, vagy kattintson az **Új** gombra a létrehozáshoz.

   > [!NOTE]
   > A Futtatás másként fióktípusnak Windows típusúnak kell lennie. A Futtatás másként fióknak az SQL Server-példányokat tároló összes Windows-kiszolgáló helyi rendszergazdák csoportjának is részét kell lennie.
   >
   >
5. Kattintson a **Mentés** gombra.
6. Módosítsa, majd hajtsa végre a következő T-SQL-mintát minden SQL Server-példányon, hogy megadja a Futtatás fiókként az állapot-ellenőrzés végrehajtásához szükséges minimális engedélyeket. Ezt azonban nem kell megtennie, ha a Futtatás másként fiók már része az SQL Server-példányok rendszergazdai kiszolgálói szerepkörének.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Az SQL Futtatás másként fiók konfigurálása a Windows PowerShell használatával
Nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsfájlt, miután frissítette az adatokkal:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>A javaslatok rangsorolásának ismertetése
Minden ajánlás súlyozási értéket kap, amely azonosítja az ajánlás relatív fontosságát. Csak a tíz legfontosabb ajánlás jelenik meg.

### <a name="how-weights-are-calculated"></a>A súlyozás kiszámítása
A súlyozás három fő tényezőn alapuló összesített érték:

* Annak *valószínűsége,* hogy az azonosított probléma problémákat okoz. Nagyobb valószínűséggel felel meg az ajánlás nagyobb összpontszámának.
* A probléma *hatása* a szervezetre, ha problémát okoz. A nagyobb hatás megegyezik a nagyobb általános pontszám az ajánlás.
* Az ajánlás végrehajtásához szükséges *erőfeszítés.* A nagyobb erőfeszítés megegyezik egy kisebb általános pontszám az ajánlás.

Az egyes ajánlások súlyozását az egyes kiemelt területre rendelkezésre álló összpontszám százalékában fejezik ki. Ha például a Biztonság és megfelelőség kiemelt területen egy javaslat 5%-os pontszámmal rendelkezik, az ajánlás megvalósítása 5%-kal növeli a teljes biztonsági és megfelelőségi pontszámot.

### <a name="focus-areas"></a>Fókuszterületek
**Biztonság és megfelelőség** – Ez a kiemelt terület a lehetséges biztonsági fenyegetésekre és szabálysértésekre, a vállalati házirendekre, valamint a műszaki, jogi és jogszabályi megfelelőségi követelményekre vonatkozó ajánlásokat mutatja be.

**Rendelkezésre állás és üzletmenet-folytonosság** – Ez a kiemelt terület a szolgáltatás rendelkezésre állására, az infrastruktúra rugalmasságára és az üzleti védelemre vonatkozó javaslatokat mutatja be.

**Teljesítmény és méretezhetőség** – Ez a kiemelt terület javaslatokat jelenít meg a szervezet informatikai infrastruktúrájának növekedéséhez, annak biztosításához, hogy az informatikai környezet megfeleljen az aktuális teljesítménykövetelményeknek, és képes legyen reagálni a változó infrastrukturális igényekre.

**Frissítés, áttelepítés és telepítés** – Ez a kiemelt terület javaslatokat tartalmaz az SQL Server frissítésének, áttelepítésének és meglévő infrastruktúrára való telepítésének elősegítésére.

**Műveletek és figyelés** – Ez a kiemelt terület javaslatokat tartalmaz az informatikai műveletek egyszerűsítésére, a megelőző karbantartás megvalósítására és a teljesítmény maximalizálására.

**Módosítás- és konfigurációkezelés** – Ez a kiemelt terület javaslatokat jelenít meg a napi műveletek védelmére, annak biztosítására, hogy a módosítások ne befolyásolják hátrányosan az infrastruktúrát, változáskezelési eljárásokat hozzon létre, valamint nyomon kövesse és naplózza a rendszerkonfigurációkat.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Érdemes 100%-ra törekedni minden fókuszterületen?
Nem feltétlenül. A javaslatok a Microsoft mérnökei által több ezer ügyféllátogatás során szerzett tudáson és tapasztalaton alapulnak. Azonban nincs két kiszolgálói infrastruktúra azonos, és a konkrét javaslatok többé-kevésbé relevánsak lehetnek az Ön számára. Egyes biztonsági javaslatok például kevésbé relevánsak lehetnek, ha a virtuális gépek nincsenek kitéve az internetnek. Egyes rendelkezésre állási javaslatok kevésbé relevánsak lehetnek az alacsony prioritású ad hoc adatgyűjtést és -jelentéskészítést biztosító szolgáltatások esetében. Az érett vállalkozás számára fontos kérdések kevésbé fontosak lehetnek az induló vállalkozások számára. Előfordulhat, hogy meg szeretné határozni, hogy mely fókuszterületek a prioritások, majd nézze meg, hogyan változnak a pontszámok az idő múlásával.

Minden ajánlás útmutatást tartalmaz arról, hogy miért fontos. Ezzel az útmutatóval kiértékelheti, hogy a javaslat megvalósítása megfelelő-e az Informatikai szolgáltatások jellegétől és a szervezet üzleti igényeitől.

## <a name="use-health-check-focus-area-recommendations"></a>Az állapot-ellenőrzés fókuszterület-javaslatainak használata
Mielőtt egy értékelési megoldás az Azure Monitorban, a megoldás telepítve kell lennie.  A telepítés után megtekintheti a javaslatok összegzését az Azure-portálon az Azure Monitor **áttekintése lapján** található SQL Állapot-ellenőrzés csempével.

Tekintse meg az infrastruktúra összesített megfelelőségi értékeléseit, majd részletezze a javaslatokat.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>A kiemelt területre vonatkozó ajánlások megtekintése és korrekciós intézkedések meghozása
1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Monitorozás** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Monitorozás** lehetőséget.
3. A menü **Elemzések** szakaszában válassza az **Egyebek**lehetőséget.  
4. Az **Áttekintés** lapon kattintson az **SQL Állapot-ellenőrzés** csempére.
5. Az **Állapotfelmérés** lapon tekintse át az összefoglaló információkat a fókuszterület egyik paneljén, majd kattintson az egyikre az adott fókuszterületre vonatkozó javaslatok megtekintéséhez.
6. A fókuszterület bármelyik oldalán megtekintheti a környezetéhez készített rangsorolt javaslatokat. Kattintson az **Érintett objektumok** csoportban egy javaslatra a javaslat meghirdetésének részleteinek megtekintéséhez.<br><br> ![Az SQL Állapotfelmérés rekedési javaslatainak képe](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. A **Javasolt műveletek**részben javasolt korrekciós intézkedéseket végrehajthatja. Az elem kezelése után a későbbi értékelések rögzítik, hogy az ajánlott műveleteket végrehajtották, és a megfelelőségi pontszám növekedni fog. A javított elemek **átadott objektumokként**jelennek meg.

## <a name="ignore-recommendations"></a>Javaslatok figyelmen kívül hagyása
Ha olyan javaslatokat szeretne figyelmen kívül hagyni, hogy hozzon létre egy szöveges fájlt, amely az Azure Monitor fogja használni, hogy megakadályozza a javaslatok jelennek meg az értékelési eredmények.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>A figyelmen kívül hagyott ajánlások azonosítása
1. Az Azure Monitor menüben kattintson a **Naplók parancsra.**
2. Az alábbi lekérdezéssel felsorolhatja azokat a javaslatokat, amelyek a környezetben lévő számítógépek esetében sikertelenek voltak.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Itt egy képernyőkép, amely a naplólekérdezést mutatja:<br><br> ![sikertelen ajánlások](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Válassza ki a figyelmen kívül hagyni kívánt javaslatokat. A következő eljárásban az Ajánlásazonosító értékeit fogja használni.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt szövegfájl létrehozása és használata
1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be vagy írja be az egyes javaslatokkal kapcsolatos javaslatokat, amelyeket az Azure Monitor figyelmen kívül szeretne hagyni egy külön sorban, majd mentse és zárja be a fájlt.
3. Helyezze a fájlt a következő mappába minden olyan számítógépen, ahol azt szeretné, hogy az Azure Monitor figyelmen kívül hagyja a javaslatokat.
   * A Microsoft Monitoring Agent (közvetlenül vagy az Operations Manager en keresztül) rendelkező számítógépeken - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Az Operations Manager felügyeleti kiszolgálón - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Az Operations Manager 2016 felügyeleti kiszolgálón - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>A javaslatok figyelmen kívül hagyása
1. A következő ütemezett értékelés futtatása után alapértelmezés szerint minden 7 nap, a megadott javaslatok vannak jelölve figyelmen kívül hagyva, és nem jelenik meg az értékelési irányítópulton.
2. A következő naplókeresési lekérdezések segítségével listázhatja az összes figyelmen kívül hagyott javaslatot.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Ha később úgy dönt, hogy figyelmen kívül hagyott javaslatokat szeretne látni, távolítsa el az IgnoreRecommendations.txt fájlokat, vagy távolítsa el belőlük az ajánlási azonosítókat.

## <a name="sql-health-check-solution-faq"></a>Az SQL Health Check megoldás – gyakori kérdések

*Milyen ellenőrzéseket végez az SQL Assessment megoldás?*

* A következő lekérdezés az összes jelenleg végrehajtott ellenőrzés leírását jeleníti meg:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Az eredmények ezután exportálhatók az Excelbe további ellenőrzés céljából.


*Milyen gyakran fut az állapotfelmérés?*

* A csekk hétnaponta működik.

*Van mód annak konfigurálására, hogy milyen gyakran fut az ellenőrzés?*

* Jelenleg nem.

*Ha egy másik kiszolgálót fedez fel az SQL Állapot-ellenőrzési megoldás hozzáadása után, akkor a rendszer ellenőrzi?*

* Igen, amint felfedezik, akkor antól, hétnaponta ellenőrzik.

*Ha egy kiszolgálót leszerelnek, mikor távolítják el az állapotfelmérésből?*

* Ha a kiszolgáló 3 hétig nem küld adatokat, a rendszer eltávolítja azt.

*Mi a neve annak a folyamatnak, amely az adatgyűjtést végzi?*

* TanácsadóÉrtékelése.exe

*Mennyi ideig tart az adatok gyűjtése?*

* A tényleges adatgyűjtés a kiszolgálón körülbelül 1 órát vesz igénybe. A nagyszámú SQL-példányt vagy adatbázist tartalmazó kiszolgálókon hosszabb időt vehet igénybe.

*Milyen típusú adatokat gyűjtenek?*

* A következő típusú adatokat gyűjtjük:
  * WMI
  * Registry
  * Teljesítményszámlálók
  * SQL dinamikus felügyeleti nézetek (DMV).

*Van mód az adatgyűjtés időpontjára?*

* Jelenleg nem.

*Miért kell konfigurálnom a Futtatás másként fiókot?*

* Az SQL Server esetében kis számú SQL-lekérdezés fut. Ahhoz, hogy futtathatók legyenek, a VIEW SERVER STATE engedélyekkel rendelkező Futtatás fiókkal kell használni az SQL-hez.  A WMI lekérdezéséhez helyi rendszergazdai hitelesítő adatokra is szükség van.

*Miért csak a 10 legfontosabb javaslat jelenjen meg?*

* Ahelyett, hogy a feladatok teljes körű, elsöprő listáját adná meg, javasoljuk, hogy először a rangsorolt ajánlások kezelésére összpontosítson. Miután megszólította őket, további javaslatok válnak elérhetővé. Ha szeretné látni a részletes listát, megtekintheti az összes javaslatot a Log Analytics naplókeresés használatával.

*Van rá mód, hogy figyelmen kívül hagyja az ajánlást?*

* Igen, [lásd: A javaslatok figyelmen kívül hagyása](#ignore-recommendations) című szakaszban.

## <a name="next-steps"></a>További lépések
* [Naplózhatja a lekérdezéseket,](../log-query/log-query-overview.md) hogy megtudja, hogyan elemezheti a részletes SQL-állapot-ellenőrzési adatokat és javaslatokat.
