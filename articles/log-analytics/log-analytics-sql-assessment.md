---
title: "Az SQL Server-környezet az Azure Naplóelemzés optimalizálása |} Microsoft Docs"
description: "Az Azure Naplóelemzés az SQL-értékelési megoldás segítségével rendszeres időközönkénti a kockázat és az SQL server-környezetek állapotának ellenőrzéséhez."
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
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2aed3315fe60ace46dfb4176dc13aa417257b0c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-sql-server-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Az SQL Server-környezet a Naplóelemzési SQL értékelési megoldás optimalizálása

![SQL Assessment szimbólum](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Az SQL-értékelési megoldás segítségével rendszeres időközönkénti kockázat és a környezetek állapotának megállapítása. Ez a cikk segít a megoldás telepítéséhez, úgy, hogy a potenciális problémákat korrekciós műveletek hajthatók végre.

Ez a megoldás a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza. A javaslatok szerint vannak kategóriába között hat fókuszterületre vonatkozóan, amely gyorsan ismertetése a kockázat, és hajtsa végre a javítási műveletet.

Az ajánlásokat tudással és a Microsoft szakemberei ügyfél látogatások ezer tapasztalatai alapulnak. Minden ajánlást ismerteti, miért probléma előfordulhat, hogy lényeges, hogy Önnek, és előfordulhat, hogy a javasolt.

Kiválaszthatja a fókuszterületre vonatkozóan, amely a szervezet számára fontos, és a nyomon követni a kockázat szabad és megfelelő környezet futtató felé.

Után, a megoldás felvett értékelését fejezhető be, összefoglaló adatait fókuszterületek jelenik meg a **SQL Assessment** irányítópult az infrastruktúrát a környezetben. A következő szakaszok ismertetik, hogyan használja az információk a **SQL Assessment** irányítópultot, ahol megtekintheti, és ezután javasolt műveletek a SQL server-infrastruktúrában.

![SQL Assessment csempe képe](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![SQL Assessment irányítópult képe](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>Telepítése és a megoldás konfigurálása
A Standard, Developer és Enterprise kiadás SQL Server jelenleg támogatott verziói SQL Assessment működik.

Az alábbi információk segítségével telepítse és konfigurálja a megoldást.

* Ügynökök telepítenie kell a kiszolgálókat, az SQL Server telepítve van.
* Az SQL-értékelési megoldás minden számítógépen, amelyen OMS-ügynököt telepített .NET-keretrendszer 4 támogatott verziója szükséges.
* A megoldás telepítéséhez a felhasználónak kell rendszergazdaként vagy közreműködői az Azure-előfizetések az Azure portál használata. Emellett a felhasználó az OMS-munkaterület közreműködői csoportjának tagja vagy az OMS-portál rendszergazdája kell, hogy legyen.
* Ha az Operations Manager-ügynök használ SQL Assessment, szüksége lesz az Operations Manager Run-As fiók használatára. Lásd: [Operations Manager futtató fiókok az OMS](#operations-manager-run-as-accounts-for-oms) alább olvashat.

  > [!NOTE]
  > Az MMA ügynök nem támogatja az Operations Manager Run-As fiókokat.
  >
  >
* Az SQL-értékelési megoldás hozzáadása az OMS-munkaterület ismertetett eljárással [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). Nincs szükség további konfigurációra.

> [!NOTE]
> A megoldás hozzáadása után a AdvisorAssessment.exe fájl kerül kiszolgálók ügynökkel. Konfigurációs adatok olvasása és küldi el feldolgozásra a felhőben az OMS szolgáltatáshoz a rendszer. A fogadott adatokhoz logika vonatkozik, és a felhőszolgáltatás-adatait rögzíti.

## <a name="sql-assessment-data-collection-details"></a>SQL Assessment az gyűjtemény adatait
SQL Assessment gyűjti a WMI-adatok, a beállításjegyzék-adatok, a teljesítményadatokat és az SQL Server dinamikus felügyeleti nézetben eredményeket az ügynökök, amelyeken engedélyezve.

Az alábbi táblázat adatgyűjtési módszerek ügynökök, az Operations Manager (SCOM) szükséges-e, és milyen gyakran adatgyűjtés ügynök által.

| Platform | Közvetlen ügynök | SCOM-ügynököt | Azure Storage | SCOM szükséges? | Felügyeleti csoport keresztül küldött SCOM ügynök adatok | Gyűjtemény gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  | &#8226; |7 nap |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Az Operations Manager futtató fiókot a következő OMS
Az OMS szolgáltatáshoz használja az Operations Manager ügynök és a felügyeleti csoport gyűjtése és adatok elküldése az OMS szolgáltatáshoz. OMS buildek esetén munkaterhelésekhez adja meg a felügyeleti csomagok érték-szolgáltatások hozzáadása. Egyes munkaterhelések jogokkal munkaterhelés-specifikus felügyeleti csomagok eltérő biztonsági környezetben, például egy olyan tartományi fiók futtatásához. Meg kell adnia a hitelesítő adatokat egy Operations Manager futtató fiókja beállításával.

Az alábbi információk segítségével állítsa be az Operations Manager futtató fiókja SQL értékeléséhez.

### <a name="set-the-run-as-account-for-sql-assessment"></a>A futtató fiókhoz tartozó SQL-értékelés beállítása
 Ha az SQL Server felügyeleti csomag már használ, a futtató fiókot kell használnia.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Az SQL futtató fiók konfigurálása az operatív konzolon
> [!NOTE]
> Ha a közvetlen OMS-ügynököt, nem pedig az SCOM-ügynököt használ, a felügyeleti csomag mindig a helyi rendszer fiók biztonsági környezetében fut. Hagyja ki a lépéseket 1-5 az alábbi, és futtassa a T-SQL vagy a Powershell-példa, adja meg az NT AUTHORITY\SYSTEM felhasználónevet.
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
6. Módosíthatja, és majd hajtható végre a következő T-SQL-minta SQL értékelése futtató fiók szükséges minimális engedélyeket adni minden SQL Server-példányon. Azonban nem kell tennie, ha a futtató fiók már tartozik a sysadmin (rendszergazda) kiszolgálói szerepkört az SQL Server-példányok.

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

## <a name="use-assessment-focus-area-recommendations"></a>Kiértékelés fókusz terület ajánlásai használata
Egy értékelési megoldás az OMS használata előtt rendelkeznie kell a telepített megoldás. További megoldások telepítéséről lásd: [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). Azt követően, megtekintheti az összegzés ajánlások SQL Assessment csempére az OMS-áttekintése oldalon használatával.

Az összesített megfelelőségi értékelése az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Az egy fókuszban terület javaslatok megtekintése és a szükséges javítási műveletek
1. Az a **áttekintése** lapján kattintson a **SQL Assessment** csempére.
2. Az a **SQL Assessment** lapon. Ellenőrizze az összefoglaló információkat a fókusz terület paneleken egyikében, majd kattintson egy adott fókusz területre javaslatok megtekintéséhez.
3. A fókusz terület lapok egyikén tekintheti meg a környezetnek a rangsorolt ajánlásokat. Kattintson az ajánlás **érintett objektumok** miért a javaslatokkal kapcsolatos részletek megtekintéséhez.  
    ![SQL-kiértékelés ajánlásai képe](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. Az ajánlott javítási műveletek hajthatók végre **javasolt műveletek**. A cikk intéztek, újabb értékelések fog jegyezze fel, az elvégzett műveletek, és a megfelelőségi pontszám növeli. Javított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Figyelmen kívül hagyja a javaslatok
Ha figyelmen kívül hagyása kívánt ajánlásokat, létrehozhat egy szövegfájlt, amely OMS fogja használni az értékelési eredmények jelennek meg javaslatok megelőzése érdekében.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Javaslatok, figyelmen kívül hagyja majd azonosításához
1. Jelentkezzen be a munkaterületet, és nyissa meg a keresési napló. A következő lekérdezés futtatásával lista ajánlásokat, amelyek nem tudták használni a környezetében.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```

   Ez a naplófájl-keresési lekérdezés ábrázoló képernyőkép: ![nem lehetett ajánlásait](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)
2. Válassza ki a javaslatok, amelyet szeretne figyelmen kívül hagyja. A RecommendationId az értékeket fogja használni a következő eljárásban.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozzon létre, és egy IgnoreRecommendations.txt szövegfájl használata
1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be, vagy adjon meg minden egyes RecommendationId a minden javaslat, amelyet az OMS figyelmen kívül hagyása külön sorban, és mentse és zárja be a fájlt.
3. Helyezze el a fájlt a következő mappában található minden olyan számítógépen OMS figyelmen kívül hagyja a javaslatok, ahová.
   * A Microsoft Monitoring-ügynökkel rendelkező számítógépek (közvetlenül vagy az Operations Manager keresztül csatlakozik) - *SystemDrive*: \Program Files\Microsoft figyelés Agent\Agent
   * Az Operations Manager felügyeleti kiszolgálón - *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy javaslatokat figyelmen kívül hagyja a
1. Után a következő ütemezett értékelési fut, alapértelmezésben 7 naponta, a megadott javaslatok figyelmen kívül hagyott megjelölve, és nem jelenik meg az értékelés irányítópulton.
2. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listában.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
3. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott ajánlott megtekintéséhez, távolítsa el a IgnoreRecommendations.txt fájlokat, vagy RecommendationIDs eltávolíthatja őket.

## <a name="sql-assessment-solution-faq"></a>SQL-értékelési megoldás – gyakori kérdések
*Milyen gyakran fut a felmérés elvégzéséhez?*

* Az értékelés 7 naponta fut.

*Van mód konfigurálása, hogy milyen gyakran az értékelés fut?*

* Jelenleg nem.

*Ha egy másik kiszolgáló fel van derítve, miután az SQL-értékelési megoldás felvett, az értékelési?*

* Igen, ha kiderül, hogy megfelelőségét ellenőrizni kell, majd a gyakoriság 7 nap.

*Ha egy kiszolgáló le van szerelve, ha azt eltávolítja a szolgáltatásból az értékelés?*

* Ha a kiszolgáló nem időközönként adatokat küldenek a 3 hét, a rendszer eltávolítja.

*Mi az a folyamat, amelyet az adatgyűjtést a neve?*

* AdvisorAssessment.exe

*Mennyi időt vesz igénybe a gyűjtött adatok?*

* A tényleges adatok gyűjtése a kiszolgálón a körülbelül 1 órát vesz igénybe. Azt is tovább tarthat, amely az SQL Server-példányok vagy adatbázisok sok kiszolgálókon.

*Milyen típusú adatok gyűjtése?*

* A következő típusú adatok gyűjtése történt:
  * WMI
  * Beállításkulcs
  * Teljesítményszámlálók
  * Az SQL-dinamikus felügyeleti nézetek (DMV).

*Van mód konfigurálása az adatgyűjtés?*

* Jelenleg nem.

*Miért kell konfigurálni egy futtató fiókot?*

* Az SQL Server az SQL-lekérdezések kis számú futnak. Ahhoz, hogy futtatni egy futtató fiókot az SQL VIEW SERVER STATE engedélyt kell használni.  Emellett ahhoz, hogy a lekérdezése a WMI, helyi rendszergazdai jogosultságok szükségesek.

*Miért meg csak az első 10 javaslatokat?*

* Helyett felkínálva feladatok túlságosan teljesnek, javasoljuk, összpontosítani a rangsorolt javaslatok először címzést. Miután foglalkozni velük, további javaslatokat is elérhető lesz. Ha szeretné a részletes listát talál, az OMS-napló keresési ajánlások tekintheti meg.

*Van mód figyelmen kívül hagyja az ajánlás?*

* Igen, tekintse meg [figyelmen kívül hagyja a javaslatok](#ignore-recommendations) fenti szakaszban.

## <a name="next-steps"></a>Következő lépések
* [Naplók keresése](log-analytics-log-searches.md) részletes SQL megfelelőségvizsgálati adatai és a javaslatok megtekintéséhez.
