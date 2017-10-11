---
title: "Az Azure Naplóelemzés a System Center Operations Manager-környezettel optimalizálása |} Microsoft Docs"
description: "A System Center Operations Manager értékelési megoldás segítségével rendszeres időközönkénti kockázat és a környezetek állapotának megállapítása."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4992d98397da409f7c1cfbdeb40fdb0cdd0d2f19
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-assessment-preview-solution"></a>A környezetben a System Center Operations Manager Assessment (előzetes verzió) megoldás optimalizálása

![A System Center Operations Manager Assessment szimbólum](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

A System Center Operations Manager értékelési megoldás segítségével rendszeres időközönkénti a kockázat és a System Center Operations Manager server-környezetek állapotának ellenőrzéséhez. Ez a cikk segítséget nyújt a telepítését, konfigurálását és használhatja a megoldást, hogy a potenciális problémákat korrekciós műveletek hajthatók végre.

Ez a megoldás a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza. A javaslatok között négy kategóriába sorolni fókuszterületre vonatkozóan, amely gyorsan ismertetése a kockázat, és hajtsa végre a javítási műveletet.

Az ajánlásokat tudással és a Microsoft szakemberei ügyfél látogatások ezer tapasztalatai alapulnak. Minden ajánlást ismerteti, miért probléma előfordulhat, hogy lényeges, hogy Önnek, és előfordulhat, hogy a javasolt.

Kiválaszthatja a fókuszterületre vonatkozóan, amely a szervezet számára fontos, és a nyomon követni a kockázat szabad és megfelelő környezet futtató felé.

Után, a megoldás felvett értékelését fejezhető be, összefoglaló adatait fókuszterületek jelenik meg a **System Center Operations Manager Assessment** irányítópult a infrastruktúra. A következő szakaszok ismertetik, hogyan használja az információk a **System Center Operations Manager Assessment** irányítópultot, ahol megtekintheti, és ezután javasolt műveletek a SCOM-infrastruktúrában.

![A System Center Operations Manager megoldás csempe](./media/log-analytics-scom-assessment/scom-tile.png)

![A System Center Operations Manager Assessment irányítópult](./media/log-analytics-scom-assessment/scom-dashboard01.png)

## <a name="installing-and-configuring-the-solution"></a>Telepítése és a megoldás konfigurálása

A Microsoft System Operations Manager 2012 R2 és a 2012 SP1 működik a megoldás.

Az alábbi információk segítségével telepítse és konfigurálja a megoldást.

 - Egy értékelési megoldás az OMS használata előtt rendelkeznie kell a telepített megoldás. A megoldás telepítése [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview) vagy utasításait követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).

 - Miután hozzáadta a megoldás a munkaterületet, a System Center Operations Manager Assessment csempe az irányítópulton a további konfigurációs szükséges üzenetet jeleníti meg. Kattintson a csempére, és kövesse a beállítási lépéseket az oldalon szerepel

 ![A System Center Operations Manager irányítópult-csempe](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

 A System Center Operations Manager konfigurálása az OMS-megoldás a konfiguráció oldalon leírt lépéseket követve végezhető el a parancsfájlt.

 Ehelyett az SCOM-konzollal assessment konfigurálásához kövesse a következő lépések ugyanabban a sorrendben
1. [A futtató fiók beállítása a System Center Operations Manager értékelése](#operations-manager-run-as-accounts-for-oms)  
2. [A System Center Operations Manager Assessment szabály konfigurálása](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>A System Center Operations Manager assessment az gyűjtemény adatait

A System Center Operations Manager assessment gyűjti a WMI-adatok, beállításjegyzék-adatok, az eseménynaplóban adatok, az Operations Manager adatainak a Windows PowerShell, az SQL-lekérdezések, a fájl információkat gyűjtő használja a kiszolgálót, amelyeken engedélyezve.

Az alábbi táblázat adatgyűjtési módszerek a System Center Operations Manager értékelést, és milyen gyakran egy ügynök által gyűjtött adatok.

| Platform | Közvetlen ügynök | SCOM-ügynököt | Azure Storage | SCOM szükséges? | Felügyeleti csoport keresztül küldött SCOM ügynök adatok | Gyűjtemény gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | | | | &#8226; | | hét napja |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Az Operations Manager futtató fiókot a következő OMS

Munkaterhelésekhez adja meg a felügyeleti csomagok épít OMS-hozzáadott értéket szolgáltatások. Egyes munkaterhelések jogokkal munkaterhelés-specifikus felügyeleti csomagok eltérő biztonsági környezetben, például egy olyan tartományi fiók futtatásához. Konfigurálja az Operations Manager futtató fiók hitelesítő adatok megadása.

Az alábbi információk segítségével a System Center Operations Manager Assessment beállítani az Operations Manager futtató fiókja.

### <a name="set-the-run-as-account"></a>A futtató fiók beállítása

1. Az Operations Manager konzolt, keresse meg a **felügyeleti** fülre.
2. Az a **futtató konfiguráció**, kattintson a **fiókok**.
3. Hozzon létre a futtató fiókot, a varázslóval, a Windows-fiók létrehozása a következő. A használandó fiókot, a egy azonosított, és hogy minden, az alábbi előfeltételek:

    >[!NOTE]
    A Futtatás mint fiók a következő követelményeknek kell megfelelnie:
    - Tartományi fiók tagja a helyi Rendszergazdák csoport összes kiszolgálójára a környezetben (az összes Operations Manager szerepkör - felügyeleti kiszolgáló, az OpsMgr-adatbázis, adatraktár, jelentéskészítési, Webkonzol, átjáró)
    - A felügyeleti csoport kiszabott művelet Manager rendszergazdai szerepköréhez
    - Hajtsa végre a [parancsfájl](#sql-script-to-grant-granular-permissions-to-the-run-as-account) a fiókot az Operations Manager által használt SQL-példány részletes engedélyt.
      Megjegyzés: Ha ez a fiók már rendelkezik rendszergazdai jogosultságokkal, majd hagyja ki a parancsfájl végrehajtása.

4. A **terjesztési biztonsági**, jelölje be **biztonságosabb**.
5. A felügyeleti kiszolgáló, a fiók terjesztési helyének megadása
3. Lépjen vissza a futtató konfiguráció, és kattintson a **profilok**.
4. Keresse meg a *SCOM Assessment profil*.
5. A profil nevét kell megadni: *Microsoft System Center Advisor SCOM Assessment futtató profil*.
6. Kattintson a jobb gombbal, és a tulajdonságok frissítése, és adja hozzá a legutóbb létrehozott futtató fiókot a 3. lépésben létrehozott.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-parancsfájl részletes engedélyeket a futtató fiók

Hajtsa végre a következő SQL-parancsfájlt a futtató fiók az Operations Manager által használt SQL-példányában szükséges engedélyt.

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


### <a name="configure-the-assessment-rule"></a>A felmérési szabály konfigurálása

A System Center Operations Manager Assessment megoldási felügyeleti csomag magában foglalja egy nevű szabályt *Microsoft System Center Advisor SCOM Assessment futtatása Assessment szabály*. Ez a szabály futtatása az értékelés felelős. A szabály engedélyezése, és adja meg a gyakorisággal, használja az alábbi eljárások.

Alapértelmezés szerint a Microsoft System Center Advisor SCOM Assessment futtatása Assessment szabály le van tiltva. Az értékelés futtatása, engedélyeznie kell a szabály a felügyeleti kiszolgálón. Kövesse az alábbi lépéseket.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>A szabály egy adott felügyeleti kiszolgáló engedélyezése

1. Az a **szerzői műveletek** munkaterületén az Operations Manager konzolt, keresse meg a szabály *Microsoft System Center Advisor SCOM Assessment futtatása Assessment szabály* a a **szabályok** ablaktáblán.
2. A keresési eredmények között, válassza ki azt, amelyik tartalmazza a szöveg *típusa: felügyeleti kiszolgáló*.
3. Kattintson a jobb gombbal a szabályt, és kattintson a **felülbírálások** > **a következő osztály egy adott objektumához: felügyeleti kiszolgáló**.
4.  A rendelkezésre álló felügyeleti kiszolgálók listában válassza ki a felügyeleti kiszolgálót, ahol fusson a szabály.
5.  Győződjön meg arról, hogy módosította a felülbírálás értékét **igaz** a a **engedélyezve** paraméter értékét.  
    ![bírálja felül a paraméter](./media/log-analytics-scom-assessment/rule.png)

Közben továbbra is ebben az ablakban konfigurálja a következő eljárással Futtatás gyakorisága.

#### <a name="configure-the-run-frequency"></a>A futtatási gyakorisága konfigurálása

Az értékelés minden 10 080 perc (vagy a hét napja), futtatásra van beállítva az alapértelmezett időköz. A minimális értékét 1440 perc (vagy egy nap) lehet felülbírálni. Az érték azt jelenti, hogy a minimális kihagyást közötti egymást követő assessment futtatása szükséges. Bírálja felül az időköz, használja az alábbi lépéseket.

1. Az a **szerzői műveletek** munkaterületén az Operations Manager konzolt, keresse meg a szabály *Microsoft System Center Advisor SCOM Assessment futtatása Assessment szabály* a a **szabályok** ablaktáblán.
2. A keresési eredmények között, válassza ki azt, amelyik tartalmazza a szöveg *típusa: felügyeleti kiszolgáló*.
3. Kattintson a jobb gombbal a szabályt, és kattintson a **szabály felülbírálása** > **a következő osztály összes objektumához: felügyeleti kiszolgáló**.
4. Módosítsa a **időköz** paraméter értéke a kívánt időközt értékre. Az alábbi példában a értéke 1440 perc (egy nap).  
    ![időköz paraméter](./media/log-analytics-scom-assessment/interval.png)  
    Ha a beállítás értéke kisebb, mint 1440 perc, majd a szabály futtatása egy nap időközönként. Ebben a példában a szabály figyelmen kívül hagyja az intervallum értéke, és egy nap gyakorisággal futtatja.


## <a name="understanding-how-recommendations-are-prioritized"></a>Hogyan kerülnek előrébb a javaslatok megértése

Minden javaslat végrehajtott, amely azonosítja a relatív fontosságát az ajánlás súlyozási értéket kap. A 10 legfontosabb javaslatok láthatók.

### <a name="how-weights-are-calculated"></a>Hogyan súlyok kiszámítása

Súlyozás alapján három kulcsfontosságú szerepet játszik az összesített értékek:

- A *valószínűség* , hogy azonosított problémát okoz problémát. Nagyobb a valószínűsége annak megfelel az ajánlás nagyobb általános pontszámot.
- A *hatás* , a problémát a szervezetben, ha azt okozzák a problémát. Egy magasabb hatása megfelel az ajánlás nagyobb általános pontszámot.
- A *elérhető* a javaslat végrehajtásához szükséges. Egy újabb elérhető megfelel az ajánlás kisebb általános pontszámot.

Minden ajánlást a súlyozási arányában a teljes pontszám minden fókusz területen érhető el. Például ha a rendelkezésre állás és üzleti folytonosság fókusz területen ajánlást 5 %-os pontszámot, amelyekre érvényes végrehajtási növeli a teljes rendelkezésre állás és üzleti folytonosság pontszám által 5 %.

### <a name="focus-areas"></a>Fókuszterületek

**Rendelkezésre állás és üzleti folytonosság** -fókusz itt megtekinthető a szolgáltatás rendelkezésre állása, a rugalmasság, az infrastruktúra és az üzleti védelmét javaslatok.

**Teljesítmény és méretezhetőség** -e fókuszba területen látható ajánlásokat a szervezet informatikai infrastruktúrájának nő, győződjön meg arról, hogy az informatikai környezet megfelel az aktuális teljesítménykövetelményeknek, és képes reagálni a infrastruktúrához.

**Frissítés, az áttelepítés és a központi telepítés** -e fókuszba területen látható ajánlásokat frissítéséhez, telepítse át, és az SQL Server telepítéséhez a meglévő infrastruktúra.

**Műveletek és -figyelő** -e fókuszba területen látható az IT-üzemeltetők egyszerűsítésére, megvalósítása megelőző karbantartási és teljesítmény maximalizálása érdekében ajánlott.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Kell megcéloznia 100 %-os pontozása minden fókusz területen?

Nem feltétlenül. Az ajánlások a Tudásbázis és a Microsoft szakemberei által ügyfél látogatások ezer keresztül szerzett tapasztalatok alapulnak. Azonban nincs két kiszolgáló infrastruktúrák megegyeznek, és előfordulhat, hogy több vagy kevesebb kapcsolódik, konkrét javaslatokért. Például bizonyos biztonsági javaslatok kevésbé fontos, ha a virtuális gépek nem érhetők el az Internet lehet. Egyes rendelkezésre állási javaslatok lehet kevésbé alacsony prioritást ad hoc adatgyűjtés és a reporting Services. Lehet, hogy egy összetett üzleti fontos problémák kevésbé fontos, hogy a kezdeti. Érdemes lehet azonosítani a fókusz a prioritások, és keresse meg, hogy a pontszámokat változnak az idők.

Minden javaslat arról, hogy miért fontos útmutatást tartalmazza. Ez az útmutató segítségével kiértékelheti, hogy a javaslat végrehajtási, az informatikai szolgáltatások természetét és a szervezete üzleti igényeinek adott-e.

## <a name="use-assessment-focus-area-recommendations"></a>Kiértékelés fókusz terület ajánlásai használata

Egy értékelési megoldás az OMS használata előtt rendelkeznie kell a telepített megoldás. További megoldások telepítéséről lásd: [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). Azt követően, a System Center Operations Manager Assessment csempe használatával az OMS-áttekintése oldalon megtekintéséhez javaslatok összegzését.

Az összesített megfelelőségi értékelése az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Az egy fókuszban terület javaslatok megtekintése és a szükséges javítási műveletek

1. Az a **áttekintése** lapján kattintson a **System Center Operations Manager Assessment** csempére.
2. Az a **System Center Operations Manager Assessment** lapon. Ellenőrizze az összefoglaló információkat a fókusz terület paneleken egyikében, majd kattintson egy adott fókusz területre javaslatok megtekintéséhez.
3. A fókusz terület lapok egyikén tekintheti meg a környezetnek a rangsorolt ajánlásokat. Kattintson az ajánlás **érintett objektumok** miért a javaslatokkal kapcsolatos részletek megtekintéséhez.  
    ![fókusz terület](./media/log-analytics-scom-assessment/focus-area.png)
4. Az ajánlott javítási műveletek hajthatók végre **javasolt műveletek**. A cikk intéztek, újabb értékelések fog jegyezze fel, az elvégzett műveletek, és a megfelelőségi pontszám növeli. Javított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Figyelmen kívül hagyja a javaslatok

Ha figyelmen kívül hagyása kívánt ajánlásokat, létrehozhat egy szövegfájlt, amely OMS használja az értékelési eredmények jelennek meg javaslatok megelőzése érdekében.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Javaslatok figyelmen kívül hagyása kívánt azonosításához

1. Jelentkezzen be a munkaterületet, és nyissa meg a keresési napló. A következő lekérdezés futtatásával lista ajánlásokat, amelyek nem tudták használni a környezetében.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Ez a naplófájl-keresési lekérdezés ábrázoló képernyőkép:  
    ![naplóbeli keresés](./media/log-analytics-scom-assessment/scom-log-search.png)

2. Válassza ki a javaslatok, amelyet szeretne figyelmen kívül hagyja. A RecommendationId az értékeket fogja használni a következő eljárásban.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozzon létre, és egy IgnoreRecommendations.txt szövegfájl használata

1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be, vagy adjon meg minden egyes RecommendationId a minden javaslat, amelyet az OMS figyelmen kívül hagyása külön sorban, és mentse és zárja be a fájlt.
3. Helyezze el a fájlt a következő mappában található minden olyan számítógépen OMS figyelmen kívül hagyja a javaslatok, ahová.
4. Az Operations Manager felügyeleti kiszolgálón - *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft.

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy javaslatokat figyelmen kívül hagyja a

1. Után a következő ütemezett értékelési fut, alapértelmezés szerint minden hét nap, a megadott javaslatok figyelmen kívül hagyott megjelölve, és nem jelenik meg az értékelés irányítópulton.
2. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listában.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

3. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott ajánlott megtekintéséhez, távolítsa el a IgnoreRecommendations.txt fájlokat, vagy RecommendationIDs eltávolíthatja őket.

## <a name="system-center-operations-manager-assessment-solution-faq"></a>A System Center Operations Manager értékelési megoldás – gyakori kérdések

*Az értékelési megoldás az OMS-munkaterület hozzáadott. Azonban nem szerepel a javaslatokat. miért ne?* Miután hozzáadta a megoldás, használja a következő lépéseket a javaslatok az OMS-irányítópulton.  

- [A futtató fiók beállítása a System Center Operations Manager értékelése](#operations-manager-run-as-accounts-for-oms)  
- [A System Center Operations Manager Assessment szabály konfigurálása](#configure-the-assessment-rule)


*Van mód konfigurálása, hogy milyen gyakran az értékelés fut?* Igen. Lásd: [állítsa be a futási gyakoriságát](#configure-the-run-frequency).

*Ha egy másik kiszolgáló fel van derítve, miután a System Center Operations Manager értékelési megoldás felvett, az értékelési?* Igen, felderítése, után úgy értékelik ettől majd a--alapértelmezés szerint hét naponta.

*Mi az a folyamat, amelyet az adatgyűjtést a neve?* AdvisorAssessment.exe

*Ha fut a AdvisorAssessment.exe folyamat?* AdvisorAssessment.exe fut az állapotfigyelő szolgáltatást a felügyeleti kiszolgáló a, amelyekben engedélyezve van-e az assessment szabály. A folyamat használja, a teljes környezet felderítési sorrendekben távoli adatgyűjtés.

*Mennyi időt vesz igénybe az adatok gyűjtését?* Adatgyűjtés a kiszolgálón körülbelül egy órát vesz igénybe. Azt is tovább tarthat olyan környezetekben, ahol sok Operations Manager-példányok vagy adatbázisok.

*Mi történik, ha állítható be az értékelési-ek intervallumának és legfeljebb 1440 perc?* Az értékelés előre megadott, naponta egyszer legfeljebb futtatásához. Ha az intervallum értéke értékre felülbírálása legfeljebb 1440 perc, majd az értékelés használ az intervallum értéke 1440 perc.

*Hogyan állapítható meg vannak-e az előfeltételként hibák?* Ha az értékelés futott, és az eredmények nem jelenik meg, akkor valószínű, hogy a vizsgálathoz szükséges előfeltételek némelyike nem sikerült. Ön is végrehajthatja a lekérdezések: `Type=Operation Solution=SCOMAssessment` és `Type=SCOMAssessmentRecommendation FocusArea=Prerequisites` napló keresés a sikertelen előfeltétel megjelenítéséhez.

*Van egy `Failed to connect to the SQL Instance (….).` üzenet előfeltételt sikertelen. Mi az a probléma?* AdvisorAssessment.exe, a folyamat által összegyűjtött adatok, az állapotfigyelő szolgáltatást a felügyeleti kiszolgáló alatt fut. Az értékelés részeként a folyamat megpróbál csatlakozni az SQL Server, ahol az Operations Manager adatbázis szerepel-e. Ez a hiba akkor fordulhat elő, amikor tűzfalszabályok, tiltsa le a kapcsolatot az SQL Server-példányhoz.

*Milyen típusú adatok gyűjtése?* A következő típusú adatok összegyűjtése: - WMI-adatok - beállításjegyzék - adatok eseménynapló - adatok az Operations Manager adatainak Windows PowerShell, az SQL-lekérdezések és a fájl információkat gyűjtő keresztül.

*Miért kell konfigurálni egy futtató fiókot?* Az Operations Manager-kiszolgáló esetén a különböző SQL-lekérdezések futnak. Ahhoz, hogy futtatni egy futtató fiókot kell használnia, szükséges engedélyekkel. Ezenkívül, helyi rendszergazdai jogosultságok szükségesek a WMI-lekérdezés.

*Miért meg csak az első 10 javaslatokat?* Helyett felkínálva feladatok teljes körű, túlságosan listáját, ajánlott összpontosítani a rangsorolt javaslatok először címzést. Miután foglalkozni velük, további javaslatokat is elérhető lesz. Ha szeretné a részletes listája, megtekintheti az összes naplófájl-keresési javaslatok.

*Van mód figyelmen kívül hagyja az ajánlás?* Igen, tekintse meg a [figyelmen kívül hagyja a javaslatok](#Ignore-recommendations).


## <a name="next-steps"></a>Következő lépések

- [Naplók keresése](log-analytics-log-searches.md) a System Center Operations Manager Assessment részletes adatai és a javaslatok.
