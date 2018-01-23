---
title: "Az SQL Server-környezet az Azure Naplóelemzés optimalizálása |} Microsoft Docs"
description: "Az Azure Naplóelemzés használhatja az SQL állapotának ellenőrzése megoldás rendszeres időközönkénti a kockázat és a környezetek állapotának felmérésére."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: magoedte;banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09fed11830bbbce23f7098050568d68a3b3bebec
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>A Naplóelemzési az SQL Server állapotának ellenőrzése megoldás SQL környezetében optimalizálása

![A szimbólum SQL állapotának ellenőrzése](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Az SQL állapotának ellenőrzése megoldás segítségével rendszeres időközönkénti kockázat és a környezetek állapotának megállapítása. Ez a cikk segít a megoldás telepítéséhez, úgy, hogy a potenciális problémákat korrekciós műveletek hajthatók végre.

Ez a megoldás a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza. A javaslatok szerint vannak kategóriába között hat fókuszterületre vonatkozóan, amely gyorsan ismertetése a kockázat, és hajtsa végre a javítási műveletet.

Az ajánlásokat tudással és a Microsoft szakemberei ügyfél látogatások ezer tapasztalatai alapulnak. Minden ajánlást ismerteti, miért probléma előfordulhat, hogy lényeges, hogy Önnek, és előfordulhat, hogy a javasolt.

Kiválaszthatja a fókuszterületre vonatkozóan, amely a szervezet számára fontos, és a nyomon követni a kockázat szabad és megfelelő környezet futtató felé.

Után, a megoldás felvett értékelését fejezhető be, összefoglaló adatait fókuszterületek jelenik meg a **SQL állapotának ellenőrzése** irányítópult az infrastruktúrát a környezetben. A következő szakaszok ismertetik, hogyan használja az információk a **SQL állapotának ellenőrzése** irányítópultot, ahol megtekintheti, és ezután javasolt műveletek a SQL Server-infrastruktúrában.

![SQL állapotának ellenőrzése csempe képe](./media/log-analytics-sql-assessment/sql-healthcheck-summary-tile.png)

![SQL állapotának ellenőrzése irányítópult képe](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Előfeltételek

* Az SQL állapotának ellenőrzése megoldás, amely rendelkezik a Microsoft Monitoring Agent (MMA) telepítése minden egyes számítógépen telepített .NET-keretrendszer 4 támogatott verziója szükséges.  Az MMA ügynök System Center 2016 - Operations Manager és az Operations Manager 2012 R2 és a Naplóelemzés szolgáltatás használja.  
* A megoldás az SQL Server 2016, 2012 vagy 2014 verziót támogatja.
* A Naplóelemzési munkaterület az SQL állapotának ellenőrzése megoldás felvétele az Azure-portálon az Azure piactérről.  A megoldás telepítéséhez rendszergazdaként vagy az Azure-előfizetésben közreműködői kell lennie. 

  > [!NOTE]
  > A megoldás hozzáadása után a AdvisorAssessment.exe fájl kerül kiszolgálók ügynökkel. Konfigurációs adatok olvasása és elküldheti a Naplóelemzés szolgáltatás feldolgozásra a felhőben. A fogadott adatokhoz logika vonatkozik, és a felhőszolgáltatás-adatait rögzíti.
  >
  >

Hajtsa végre a rendszerállapot-ellenőrzést futtat az SQL Server-kiszolgálók, szükségük van egy ügynök és a kapcsolat szolgáltatáshoz a következő támogatott módszerek egyikének használatával:

1. Telepítse a [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) Ha a kiszolgáló nem már figyel a System Center 2016 - Operations Manager, illetve az Operations Manager 2012 R2.
2. Ha a számítógép megfigyelés alatt áll a System Center 2016 - Operations Manager, illetve az Operations Manager 2012 R2 és a felügyeleti csoport nem integrálva van a Naplóelemzési szolgáltatás, a kiszolgáló többhelyű a Log Analyticshez az adatok gyűjtéséhez és a szolgáltatás továbbítja, és továbbra is lehet az Operations Manager általi megfigyelés alatt.  
3. Ellenkező esetben, ha az Operations Manager felügyeleti csoportjának integrálva van a szolgáltatás, kell hozzáadnia a tartományvezérlők, az adatok gyűjtésével a következő lépéseket a szolgáltatás által [ügynök által felügyelt számítógépek hozzáadása](log-analytics-om-agents.md#connecting-operations-manager-to-oms) engedélyezése után a megoldás a munkaterületen.  

Az ügynököt az SQL Server, az Operations Manager felügyeleti csoport jelentéseket gyűjti az adatokat, továbbítja a hozzárendelt felügyeleti kiszolgálóhoz, és a Log Analytics szolgáltatás közvetlenül a felügyeleti kiszolgálótól érkező majd érkezik.  Az adatok nem szerepel az Operations Manager-adatbázishoz.  

Ha az SQL Server az Operations Manager által figyelt, akkor konfigurálása egy Operations Manager futtató fiókja. Lásd: [Operations Manager futtató fiókok a Naplóelemzési](#operations-manager-run-as-accounts-for-log-analytics) alább olvashat.

## <a name="sql-health-check-data-collection-details"></a>Az gyűjtemény adatait SQL állapotának ellenőrzése
SQL állapotának ellenőrzése gyűjti az adatokat, hogy engedélyezte-ügynök használatával az alábbi forrásokból: 

* A Windows Management Instrumentation (WMI) 
* Beállításjegyzék 
* Teljesítményszámlálók
* SQL Server dinamikus felügyeleti eredmények megtekintése 

Adatok gyűjtése az SQL-kiszolgálón, és hét naponta Naplóelemzési továbbítja.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Az Operations Manager futtató fiókot a következő Naplóelemzési
A Naplóelemzési gyűjtése és küldhetnek adatokat a Naplóelemzés szolgáltatás az Operations Manager ügynök és a felügyeleti csoport használatával. Napló Analytics buildek esetén munkaterhelésekhez adja meg a felügyeleti csomagok érték-szolgáltatások hozzáadása. Egyes munkaterhelések munkaterhelés-specifikus jogosultság felügyeleti csomagok eltérő biztonsági környezetben, például a tartományi felhasználói fiókot igényel. Meg kell adnia a hitelesítő adatokat egy Operations Manager futtató fiókja beállításával.

A következő adatokat használja az SQL állapotának ellenőrzése az Operations Manager futtató fiók beállítását.

### <a name="set-the-run-as-account-for-sql-health-check"></a>A futtató fiók beállítása SQL állapotának ellenőrzése
 Ha az SQL Server felügyeleti csomag már használ, ez a futtató konfiguráció kell használnia.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Az SQL futtató fiók konfigurálása az operatív konzolon
> [!NOTE]
> Alapértelmezés szerint a felügyeleti csomag olyan munkafolyamatot a helyi rendszer fiók biztonsági környezetében fut. Ha használja a Microsoft Monitoring Agent közvetlenül kapcsolódik a szolgáltatáshoz helyett jelentéskészítési közvetlenül egy Operations Manager felügyeleti csoporthoz, hagyja ki az 1-5. lépéseket, és futtassa a T-SQL vagy a PowerShell-példa, adja meg az NT AUTHORITY\SYSTEM, mint a a felhasználónév.
>
>

1. Az Operations Manager programban nyissa meg az operatív konzolt, és kattintson **felügyeleti**.
2. A **futtató konfiguráció**, kattintson a **profilok**, és nyissa meg a **OMS SQL Assessment futtató profil**.
3. Az a **futtató fiókok** kattintson **Hozzáadás**.
4. Válassza ki a Windows futtató fiókhoz, amely tartalmazza az SQL Server szükséges hitelesítő adatokat, vagy kattintson a **új** kattintva létrehozhat egyet.

   > [!NOTE]
   > A futtató fiók típusú Windows kell lennie. A futtató fiók a helyi Rendszergazdák csoport összes SQL Server-példányokat futtató Windows-kiszolgálók részét is kell.
   >
   >
5. Kattintson a **Save** (Mentés) gombra.
6. Módosíthatja, és majd hajtható végre a következő T-SQL-minta minden SQL Server-példányon a futtató fiók az állapot-ellenőrzés végrehajtásához szükséges minimális engedélyeket adni. Azonban nem szükséges ezt megtenni, ha a futtató fiók már tartozik a sysadmin (rendszergazda) kiszolgálói szerepkört az SQL Server-példányokat.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>A Windows PowerShell használatával az SQL futtató fiók konfigurálása
Nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsfájlt, miután frissítette a információkkal:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Hogyan kerülnek előrébb a javaslatok megértése
Minden javaslat végrehajtott, amely azonosítja a relatív fontosságát az ajánlás súlyozási értéket kap. Csak a tíz legfontosabb javaslatok láthatók.

### <a name="how-weights-are-calculated"></a>Hogyan súlyok kiszámítása
Súlyozás alapján három kulcsfontosságú szerepet játszik az összesített értékek:

* A *valószínűség* , hogy azonosított problémát okoz problémát. Nagyobb a valószínűsége annak megfelel az ajánlás nagyobb általános pontszámot.
* A *hatás* , a problémát a szervezetben, ha azt okozzák a problémát. Egy magasabb hatása megfelel az ajánlás nagyobb általános pontszámot.
* A *elérhető* a javaslat végrehajtásához szükséges. Egy újabb elérhető megfelel az ajánlás kisebb általános pontszámot.

Minden ajánlást a súlyozási arányában a teljes pontszám minden fókusz területen érhető el. Például ha a biztonsági és megfelelőségi fókusz területen ajánlást 5 %-os pontszámot, amelyekre érvényes végrehajtási növeli a teljes biztonsági és megfelelőségi pontszám által 5 %.

### <a name="focus-areas"></a>Fókuszterületek
**Biztonsági és megfelelőségi** -e fókuszba területen látható esetleges biztonsági fenyegetéseket jelezhetnek és a behatolás, a vállalati házirendeket és a technikai, jogszabályi és szabályozásoknak megfelelőségi előírásokra vonatkozó javaslatok.

**Rendelkezésre állás és üzleti folytonosság** -fókusz itt megtekinthető a szolgáltatás rendelkezésre állása, a rugalmasság, az infrastruktúra és az üzleti védelmét javaslatok.

**Teljesítmény és méretezhetőség** -e fókuszba területen látható ajánlásokat a szervezet informatikai infrastruktúrájának nő, győződjön meg arról, hogy az informatikai környezet megfelel az aktuális teljesítménykövetelményeknek, és képes reagálni a infrastruktúrához.

**Áttelepítés és a telepítés frissítéséhez** -e fókuszba területen látható ajánlásokat frissítéséhez, telepítse át, és az SQL Server telepítéséhez a meglévő infrastruktúra.

**Műveletek és -figyelő** -e fókuszba területen látható az IT-üzemeltetők egyszerűsítésére, megvalósítása megelőző karbantartási és teljesítmény maximalizálása érdekében ajánlott.

**Változás- és konfigurációkezelés** -e fókuszba területen látható ajánlások a napi tevékenységek védje, biztosítására, hogy módosításokat nem negatív hatással vannak az infrastruktúra Változáskezelő eljárások, létrehozásához, és nyomon követése és naplózása rendszerkonfigurációk.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Kell megcéloznia 100 %-os pontozása minden fókusz területen?
Nem feltétlenül. Az ajánlások a Tudásbázis és a Microsoft szakemberei által ügyfél látogatások ezer keresztül szerzett tapasztalatok alapulnak. Azonban nincs két kiszolgáló infrastruktúrák megegyeznek, és előfordulhat, hogy több vagy kevesebb kapcsolódik, konkrét javaslatokért. Például bizonyos biztonsági javaslatok kevésbé fontos, ha a virtuális gépek nem érhetők el az Internet lehet. Egyes rendelkezésre állási javaslatok lehet kevésbé alacsony prioritást ad hoc adatgyűjtés és a reporting Services. Lehet, hogy egy összetett üzleti fontos problémák kevésbé fontos, hogy a kezdeti. Érdemes lehet azonosítani a fókusz a prioritások, és keresse meg, hogy a pontszámokat változnak az idők.

Minden javaslat arról, hogy miért fontos útmutatást tartalmazza. Ezt az útmutatást kell használnia annak kiértékelésére, hogy a javaslat végrehajtási, az informatikai szolgáltatások természetét és a szervezete üzleti igényeinek.

## <a name="use-health-check-focus-area-recommendations"></a>Használja a fókusz terület javaslatok állapotának ellenőrzése
Egy értékelési megoldás a Naplóelemzési használata előtt rendelkeznie kell a telepített megoldás.  Azt követően, megtekintheti a javaslatok összegzése az SQL állapotának ellenőrzése csempe az Azure portálon, a megoldás oldalon használatával.

Az összesített megfelelőségi értékelése az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Az egy fókuszban terület javaslatok megtekintése és a szükséges javítási műveletek
1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen. 
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Naplóelemzési előfizetések ablaktáblán jelölje ki a munkaterület, és kattintson a **áttekintése** csempére.  
4. Az a **áttekintése** lapján kattintson a **SQL állapotának ellenőrzése** csempére. 
5. Az a **állapotának ellenőrzése** lapon. Ellenőrizze az összefoglaló információkat a fókusz terület paneleken egyikében, majd kattintson egy adott fókusz területre javaslatok megtekintéséhez.
6. A fókusz terület lapok egyikén tekintheti meg a környezetnek a rangsorolt ajánlásokat. Kattintson az ajánlás **érintett objektumok** miért a javaslatokkal kapcsolatos részletek megtekintéséhez.<br><br> ![kép ajánlások SQL állapotának ellenőrzése](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Az ajánlott javítási műveletek hajthatók végre **javasolt műveletek**. A cikk intéztek, újabb értékelések fog jegyezze fel, az elvégzett műveletek, és a megfelelőségi pontszám növeli. Javított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Figyelmen kívül hagyja a javaslatok
Ha figyelmen kívül hagyása kívánt ajánlásokat, létrehozhat egy szövegfájlt, amely Naplóelemzési fogja használni az értékelési eredmények jelennek meg javaslatok megelőzése érdekében.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Javaslatok, figyelmen kívül hagyja majd azonosításához
1. Az Azure-portálon a kijelölt munkaterülethez tartozó Naplóelemzési munkaterület lapon kattintson a **naplófájl-keresési** csempére.
2. A következő lekérdezés futtatásával lista ajánlásokat, amelyek nem tudták használni a környezetében.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a fenti lekérdezés megváltozna a következők.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Ez a naplófájl-keresési lekérdezés ábrázoló képernyőkép:<br><br> ![nem sikerült javaslatok](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Válassza ki a javaslatok, amelyet szeretne figyelmen kívül hagyja. A RecommendationId az értékeket fogja használni a következő eljárásban.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozzon létre, és egy IgnoreRecommendations.txt szövegfájl használata
1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be, vagy adjon meg minden egyes javaslat, amelyet az Naplóelemzési figyelmen kívül hagyása külön sorban, és mentse és zárja be a fájlt a RecommendationId.
3. Helyezze el a fájlt a következő mappában található minden olyan számítógépen Naplóelemzési figyelmen kívül hagyja a javaslatok, ahová.
   * A Microsoft Monitoring-ügynökkel rendelkező számítógépek (közvetlenül vagy az Operations Manager keresztül csatlakozik) - *SystemDrive*: \Program Files\Microsoft figyelés Agent\Agent
   * Az Operations Manager felügyeleti kiszolgálón - *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft
   * Az Operations Manager 2016 felügyeleti kiszolgálón - *SystemDrive*: System Center 2016\Operations Manager\Server \Program Files\Microsoft

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy javaslatokat figyelmen kívül hagyja a
1. Után a következő ütemezett értékelési fut, alapértelmezésben 7 naponta, a megadott javaslatok figyelmen kívül hagyott megjelölve, és nem jelenik meg az értékelés irányítópulton.
2. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listában.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a fenti lekérdezés megváltozna a következők.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott ajánlott megtekintéséhez, távolítsa el a IgnoreRecommendations.txt fájlokat, vagy RecommendationIDs eltávolíthatja őket.

## <a name="sql-health-check-solution-faq"></a>Gyakran ismételt kérdések megoldás SQL állapotának ellenőrzése
*Milyen gyakran fut a rendszerállapot-ellenőrzés?*

* Az ellenőrzés hét naponta fut.

*Van mód konfigurálása, hogy milyen gyakran a-ellenőrzést futtat?*

* Jelenleg nem.

*Ha egy másik kiszolgáló fel van derítve, miután az SQL állapotának ellenőrzése megoldás felvett, az ellenőrzött?*

* Igen, ha kiderül, hogy be van jelölve, majd a hét naponta.

*Ha egy kiszolgáló le van szerelve, ha azt eltávolítja a szolgáltatásból az állapot-ellenőrzés?*

* Ha a kiszolgáló nem időközönként adatokat küldenek a 3 hét, a rendszer eltávolítja.

*Mi az a folyamat, amelyet az adatgyűjtést a neve?*

* AdvisorAssessment.exe

*Mennyi időt vesz igénybe a gyűjtött adatok?*

* A tényleges adatok gyűjtése a kiszolgálón a körülbelül 1 órát vesz igénybe. Azt is tovább tarthat, amely az SQL Server-példányok vagy adatbázisok sok kiszolgálókon.

*Milyen típusú adatok gyűjtése?*

* A következő típusú adatok gyűjtése történt:
  * WMI
  * Beállításjegyzék
  * Teljesítményszámlálók
  * Az SQL-dinamikus felügyeleti nézetek (DMV).

*Van mód konfigurálása az adatgyűjtés?*

* Jelenleg nem.

*Miért kell konfigurálni egy futtató fiókot?*

* Az SQL Server az SQL-lekérdezések kis számú futnak. Ahhoz, hogy futtatni egy futtató fiókot az SQL VIEW SERVER STATE engedélyt kell használni.  Emellett ahhoz, hogy a lekérdezése a WMI, helyi rendszergazdai jogosultságok szükségesek.

*Miért meg csak az első 10 javaslatokat?*

* Helyett felkínálva feladatok túlságosan teljesnek, javasoljuk, összpontosítani a rangsorolt javaslatok először címzést. Miután foglalkozni velük, további javaslatokat is elérhető lesz. Ha szeretné a részletes listája, a Naplóelemzési naplófájl-keresési ajánlások tekintheti meg.

*Van mód figyelmen kívül hagyja az ajánlás?*

* Igen, tekintse meg [figyelmen kívül hagyja a javaslatok](#ignore-recommendations) fenti szakaszban.

## <a name="next-steps"></a>További lépések
* [Naplók keresése](log-analytics-log-searches.md) megtudhatja, hogyan elemezhet részletes SQL állapotának ellenőrzése adatok és javaslatokat.
