---
title: Az Azure Naplóelemzés a System Center Operations Manager-környezettel optimalizálása |} Microsoft Docs
description: A System Center Operations Manager állapotának ellenőrzése megoldás segítségével rendszeres időközönkénti kockázat és a környezetek állapotának megállapítása.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 493fb0295c1d760bb7de13ae1b703b3636750612
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018714"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>A környezetben a System Center Operations Manager állapotának ellenőrzése (előzetes verzió) megoldás optimalizálása

![A System Center Operations Manager állapotának ellenőrzése szimbólum](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

A System Center Operations Manager állapotának ellenőrzése megoldás segítségével rendszeres időközönkénti a kockázat és a System Center Operations Manager felügyeleti csoport állapotának ellenőrzéséhez. Ez a cikk segítséget nyújt a telepítését, konfigurálását és használhatja a megoldást, hogy a potenciális problémákat korrekciós műveletek hajthatók végre.

Ez a megoldás a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza. A javaslatok között négy kategóriába sorolni fókuszterületre vonatkozóan, amely gyorsan ismertetése a kockázat, és hajtsa végre a javítási műveletet.

Az ajánlásokat tudással és a Microsoft szakemberei ügyfél látogatások ezer tapasztalatai alapulnak. Minden ajánlást ismerteti, miért probléma előfordulhat, hogy lényeges, hogy Önnek, és előfordulhat, hogy a javasolt.

Kiválaszthatja a fókuszterületre vonatkozóan, amely a szervezet számára fontos, és a nyomon követni a kockázat szabad és megfelelő környezet futtató felé.

Után, a megoldás felvett értékelését elvégezni, összefoglaló adatait fókuszterületek jelenik meg a **System Center Operations Manager állapotának ellenőrzése** irányítópult a infrastruktúra. A következő szakaszok ismertetik, hogyan használja az információk a **System Center Operations Manager állapotának ellenőrzése** irányítópult, ahol megtekintheti, és ezután javasolt műveletek az Operations Manager-környezetben.

![A System Center Operations Manager megoldás csempe](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-tile.png)

![A System Center Operations Manager állapotának ellenőrzése irányítópult](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Telepítése és a megoldás konfigurálása

A megoldás a Microsoft System Operations Manager 2012 Service Pack (SP) 1 és 2012 R2 működik.

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

 - A állapotának ellenőrzése megoldás a Naplóelemzési használata előtt rendelkeznie kell a telepített megoldás. A megoldás telepítése [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - A megoldás a munkaterülethez való hozzáadását követően a **System Center Operations Manager állapotának ellenőrzése** csempe az irányítópulton szükséges további konfigurációs jelenik meg. Kattintson a csempére, és kövesse a beállítási lépéseket az oldalon szerepel

 ![A System Center Operations Manager irányítópult-csempe](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> A System Center Operations Manager konfigurációs végezhető parancsprogrammal szerepel a lap a Naplóelemzési megoldás lépéseit követve.

 Az Operations Manager operatív konzolon keresztül assessment megadásához a következő sorrendben hajtsa végre az alábbi lépéseket:
1. [A futtató fiók beállítása a System Center Operations Manager állapotának ellenőrzése](#operations-manager-run-as-accounts-for-log-analytics)  
2. [A System Center Operations Manager állapotának ellenőrzése szabály konfigurálása](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>A System Center Operations Manager assessment az gyűjtemény adatait

A System Center Operations Manager assessment gyűjti az adatokat az alábbi forrásokból:

* Beállításjegyzék
* A Windows Management Instrumentation (WMI)
* Eseménynapló
* Fájladatok
* Közvetlenül az Operations Manager használatával a PowerShell és az SQL-lekérdezések megadott felügyeleti kiszolgálóról.  

Adatok gyűjtése a felügyeleti kiszolgálón, és hét naponta Naplóelemzési továbbítja.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Az Operations Manager futtató fiókot a következő Naplóelemzési

Napló Analytics épít munkaterhelésekhez adja meg a felügyeleti csomagok által hozzáadott értéket szolgáltatások. Egyes munkaterhelések munkaterhelés-specifikus jogosultság felügyeleti csomagok eltérő biztonsági környezetben, például a tartományi felhasználói fiókot igényel. Az Operations Manager futtató fiók konfigurálása jogosultsági szintű hitelesítő adatokkal. További információkért lásd: [futtató fiók létrehozása](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) az Operations Manager-dokumentációban.

Az alábbi információk segítségével a System Center Operations Manager állapotának ellenőrzése az Operations Manager futtató fiók beállítása.

### <a name="set-the-run-as-account"></a>A futtató fiók beállítása

A Futtatás mint fiók meg kell felelnie a következő követelményeket a folytatás előtt:

* Egy tartományi felhasználói fiókot, amely tagja a helyi Rendszergazdák csoport összes kiszolgálójára támogató bármilyen Operations Manager szerepkör - felügyeleti kiszolgáló, az operatív helyt adó SQL Server, az adatraktár és ACS-adatbázis, webalkalmazás-konzolhoz, és átjárókiszolgáló Reporting,.
* A felügyeleti csoport kiszabott művelet Manager rendszergazdai szerepköréhez
* Ha a fiók nem rendelkezik SQL-rendszergazdai jogosultságokkal, majd futtatja a [parancsfájl](#sql-script-to-grant-granular-permissions-to-the-run-as-account) a fiókot az egyes vagy az Operations Manager-adatbázisokat üzemeltető SQL Server-példány részletes engedélyt.

1. Az Operations Manager konzolján válassza ki a **felügyeleti** navigációs gombra.
2. A **futtató konfiguráció**, kattintson a **fiókok**.
3. Az a **futtató fiók létrehozása** varázsló, a **bemutatása** kattintson **következő**.
4. Az a **általános tulajdonságok** lapon jelölje be **Windows** a a **futtató fiók típusa:** listája.
5. Adjon meg egy megjelenítési nevet a a **megjelenítendő név** szöveg mezőben, és opcionálisan írja be egy leírást a **leírás** mezőbe, majd kattintson a **tovább**.
6. Az a **terjesztési biztonsági** lapon jelölje be **biztonságosabb**.
7. Kattintson a **Create** (Létrehozás) gombra.  

Most, hogy a futtató fiók jön létre, a felügyeleti csoport felügyeleti kiszolgálókkal kell, és társított egy előre megadott futtató profilt, így a munkafolyamatok futnak-e a hitelesítő adatok használatával.  

1. A **futtató konfiguráció**, **fiókok**, az eredmények ablaktáblán kattintson duplán a korábban létrehozott fiók.
2. Az a **terjesztési** lapra, majd **Hozzáadás** a a **kijelölt számítógépek** mezőbe, majd adja hozzá a felügyeleti kiszolgáló és a fiók terjesztését.  Kattintson a **OK** kétszer gombra a módosítások mentéséhez.
3. A **futtató konfiguráció**, kattintson a **profilok**.
4. Keresse meg a *SCOM Assessment profil*.
5. A profil nevét kell megadni: *Microsoft System Center Advisor SCOM Assessment futtató profil*.
6. Kattintson a jobb gombbal, és a tulajdonságok frissítése, és adja hozzá a legutóbb létrehozott futtató fiókot, amelyet előzőleg létrehozott.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-parancsfájl részletes engedélyeket a futtató fiók

Hajtsa végre a következő SQL-parancsfájlt a futtató fiók az Operations Manager üzemeltetési a működési, adatraktár és az ACS-adatbázis az SQL Server-példány szükséges engedélyt.

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

### <a name="configure-the-health-check-rule"></a>A health ellenőrzése szabály konfigurálása

A System Center Operations Manager állapotának ellenőrzése megoldási felügyeleti csomag magában foglalja egy nevű szabályt *Microsoft System Center Advisor SCOM Assessment futtatása Assessment szabály*. Ez a szabály a rendszerállapot-ellenőrzés futtatásával felelős. A szabály engedélyezése, és adja meg a gyakorisággal, használja az alábbi eljárások.

Alapértelmezés szerint a Microsoft System Center Advisor SCOM Assessment futtatása Assessment szabály le van tiltva. A rendszerállapot-ellenőrzés futtatása, engedélyeznie kell a szabály a felügyeleti kiszolgálón. Kövesse az alábbi lépéseket.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>A szabály egy adott felügyeleti kiszolgáló engedélyezése

1. Az a **szerzői műveletek** munkaterületen keresse meg a szabály az Operations Manager operatív konzol *Microsoft System Center Advisor SCOM Assessment futtatása Assessment szabály* a a **szabályok** ablaktáblán.
2. A keresési eredmények között, válassza ki azt, amelyik tartalmazza a szöveg *típusa: felügyeleti kiszolgáló*.
3. Kattintson a jobb gombbal a szabályt, és kattintson a **felülbírálások** > **a következő osztály egy adott objektumához: felügyeleti kiszolgáló**.
4.  A rendelkezésre álló felügyeleti kiszolgálók listában válassza ki a felügyeleti kiszolgálót, ahol fusson a szabály.  Ez legyen a korábban megadott értékektől rendelkező futtató fiókhoz rendelni az azonos felügyeleti kiszolgálóhoz.
5.  Győződjön meg arról, hogy módosította a felülbírálás értékét **igaz** a a **engedélyezve** paraméter értékét.<br><br> ![bírálja felül a paraméter](./media/log-analytics-scom-assessment/rule.png)

    Ebben az ablakban a továbbra is a futási gyakoriságát, a következő eljárás segítségével konfigurálja.

#### <a name="configure-the-run-frequency"></a>A futtatási gyakorisága konfigurálása

Az értékelés alapértelmezés szerint minden 10 080 perc (vagy a hét napja) futtatásra van konfigurálva. A minimális értékét 1440 perc (vagy egy nap) lehet felülbírálni. Az érték azt jelenti, hogy a minimális kihagyást közötti egymást követő assessment futtatása szükséges. Bírálja felül az időköz, használja az alábbi lépéseket.

1. Az a **szerzői műveletek** munkaterületén az Operations Manager konzolt, keresse meg a szabály *Microsoft System Center Advisor SCOM Assessment futtatása Assessment szabály* a a **szabályok** a szakasz.
2. A keresési eredmények között, válassza ki azt, amelyik tartalmazza a szöveg *típusa: felügyeleti kiszolgáló*.
3. Kattintson a jobb gombbal a szabályt, és kattintson a **szabály felülbírálása** > **a következő osztály összes objektumához: felügyeleti kiszolgáló**.
4. Módosítsa a **időköz** paraméter értéke a kívánt időközt értékre. Az alábbi példában a értéke 1440 perc (egy nap).<br><br> ![időköz paraméter](./media/log-analytics-scom-assessment/interval.png)<br>  

    Ha a beállítás értéke kisebb, mint 1440 perc, majd a szabály futtatása egy nap intervallumban. Ebben a példában a szabály figyelmen kívül hagyja az intervallum értéke, és egy nap gyakorisággal futtatja.


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

**Teljesítmény és méretezhetőség** -e fókuszba területen látható ajánlásokat a szervezet informatikai infrastruktúrájának nő, győződjön meg arról, hogy az informatikai környezettől aktuális teljesítménykövetelményeknek megfelel-e, és képes válaszolni infrastruktúra módosítása szüksége van.

**Frissítés, az áttelepítés és a központi telepítés** -e fókuszba területen látható ajánlásokat frissítéséhez, telepítse át, és az SQL Server telepítéséhez a meglévő infrastruktúra.

**Műveletek és -figyelő** -e fókuszba területen látható az IT-üzemeltetők egyszerűsítésére, megvalósítása megelőző karbantartási és teljesítmény maximalizálása érdekében ajánlott.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Kell megcéloznia 100 %-os pontozása minden fókusz területen?

Nem feltétlenül. Az ajánlások a Tudásbázis és a Microsoft szakemberei által ügyfél látogatások ezer keresztül szerzett tapasztalatok alapulnak. Azonban nincs két kiszolgáló infrastruktúrák megegyeznek, és előfordulhat, hogy több vagy kevesebb kapcsolódik, konkrét javaslatokért. Például bizonyos biztonsági javaslatok kevésbé fontos, ha a virtuális gépek nem érhetők el az Internet lehet. Egyes rendelkezésre állási javaslatok lehet kevésbé alacsony prioritást ad hoc adatgyűjtés és a reporting Services. Lehet, hogy egy összetett üzleti fontos problémák kevésbé fontos, hogy a kezdeti. Érdemes lehet azonosítani a fókusz a prioritások, és keresse meg, hogy a pontszámokat változnak az idők.

Minden javaslat arról, hogy miért fontos útmutatást tartalmazza. Ez az útmutató segítségével kiértékelheti, hogy a javaslat végrehajtási, az informatikai szolgáltatások természetét és a szervezete üzleti igényeinek adott-e.

## <a name="use-health-check-focus-area-recommendations"></a>Használjon állapotának ellenőrzése a fókusz terület javaslatok

Ügyfélállapot-ellenőrzés megoldást a Naplóelemzési használata előtt rendelkeznie kell a telepített megoldás. További megoldások telepítéséről lásd: [felügyeleti megoldás telepítése](log-analytics-add-solutions.md). Azt követően, megtekintheti a System Center Operations Manager állapotának ellenőrzése csempe használatával javaslatok összegzése a **áttekintése** lap munkaterület az Azure portálon.

Az összesített megfelelőségi értékelése az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Az egy fókuszban terület javaslatok megtekintése és a szükséges javítási műveletek
1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Naplóelemzési előfizetések ablaktáblán jelölje ki a munkaterület, és kattintson a **munkaterület összegzés** menüpont.  
4. Az a **áttekintése** lapján kattintson a **System Center Operations Manager állapotának ellenőrzése** csempére.
5. Az a **System Center Operations Manager állapotának ellenőrzése** lapon. Ellenőrizze az összefoglaló információkat a fókusz terület paneleken egyikében, majd kattintson egy adott fókusz területre javaslatok megtekintéséhez.
6. A fókusz terület lapok egyikén tekintheti meg a környezetnek a rangsorolt ajánlásokat. Kattintson az ajánlás **érintett objektumok** miért a javaslatokkal kapcsolatos részletek megtekintéséhez.<br><br> ![fókusz terület](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Az ajánlott javítási műveletek hajthatók végre **javasolt műveletek**. A cikk intéztek, újabb értékelések fog jegyezze fel, az elvégzett műveletek, és a megfelelőségi pontszám növeli. Javított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Figyelmen kívül hagyja a javaslatok

Ha figyelmen kívül hagyása kívánt ajánlásokat, létrehozhat egy szövegfájlt, amely Naplóelemzési használja az értékelési eredmények jelennek meg javaslatok megelőzése érdekében.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Javaslatok figyelmen kívül hagyása kívánt azonosításához
1. Az Azure-portálon a kijelölt munkaterülethez tartozó Naplóelemzési munkaterület lapon kattintson a **naplófájl-keresési** menüpont.
2. A következő lekérdezés futtatásával lista ajánlásokat, amelyek nem tudták használni a környezetében.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a fenti lekérdezés megváltozna a következők.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Ez a naplófájl-keresési lekérdezés ábrázoló képernyőkép:<br><br> ![naplóbeli keresés](./media/log-analytics-scom-assessment/scom-log-search.png)<br>

3. Válassza ki a javaslatok, amelyet szeretne figyelmen kívül hagyja. A RecommendationId az értékeket fogja használni a következő eljárásban.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozzon létre, és egy IgnoreRecommendations.txt szövegfájl használata

1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be, vagy adjon meg minden egyes javaslat, amelyet az Naplóelemzési figyelmen kívül hagyása külön sorban, és mentse és zárja be a fájlt a RecommendationId.
3. Helyezze el a fájlt a következő mappában található minden olyan számítógépen Naplóelemzési figyelmen kívül hagyja a javaslatok, ahová.
4. Az Operations Manager felügyeleti kiszolgálón - *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft.

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy javaslatokat figyelmen kívül hagyja a

1. A következő ütemezett értékelési fut, alapértelmezés szerint minden hét nap, miután az adott ajánlásokat figyelmen kívül hagyott megjelölve, és nem jelenik meg a jelölőnégyzet irányítópult.
2. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listában.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a fenti lekérdezés megváltozna a következők.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott ajánlott megtekintéséhez, távolítsa el a IgnoreRecommendations.txt fájlokat, vagy RecommendationIDs eltávolíthatja őket.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>A System Center Operations Manager állapotának ellenőrzése megoldás – gyakori kérdések

*A állapotának ellenőrzése megoldás hozzáadni a Naplóelemzési munkaterület Azonban nem szerepel a javaslatokat. miért ne?* Miután hozzáadta a megoldás, használja a következő lépéseket a javaslatok a Log Analyticshez irányítópulton.  

- [A futtató fiók beállítása a System Center Operations Manager állapotának ellenőrzése](#operations-manager-run-as-accounts-for-log-analytics)  
- [A System Center Operations Manager állapotának ellenőrzése szabály konfigurálása](#configure-the-health-check-rule)


*Van mód konfigurálása, hogy milyen gyakran a-ellenőrzést futtat?* Igen. Lásd: [állítsa be a futási gyakoriságát](#configure-the-run-frequency).

*Ha egy másik kiszolgáló fel van derítve, miután a System Center Operations Manager értékelési megoldás felvett, az ellenőrzött?* Igen, azt be van jelölve ettől hét naponta alapértelmezés szerint felderítése után.

*Mi az a folyamat, amelyet az adatgyűjtést a neve?* AdvisorAssessment.exe

*Ha fut a AdvisorAssessment.exe folyamat?* AdvisorAssessment.exe a felügyeleti kiszolgáló Állapotfigyelő szolgáltatás folyamatban fut, ahol a állapotának ellenőrzése szabály van engedélyezve. A folyamat használja, a teljes környezet felderítési sorrendekben távoli adatgyűjtés.

*Mennyi időt vesz igénybe az adatok gyűjtését?* Adatgyűjtés a kiszolgálón körülbelül egy órát vesz igénybe. Azt is tovább tarthat olyan környezetekben, ahol sok Operations Manager-példányok vagy adatbázisok.

*Mi történik, ha állítható be az értékelési-ek intervallumának és legfeljebb 1440 perc?* Az értékelés előre megadott, naponta egyszer legfeljebb futtatásához. Ha az intervallum értéke értékre felülbírálása legfeljebb 1440 perc, majd az értékelés használ az intervallum értéke 1440 perc.

*Honnan lehet tudni, hogy van-e előfeltétel-ellenőrzési hibák?* Ha az állapot-ellenőrzés futtatása, és az eredmények nem jelenik meg, akkor valószínű, hogy néhány előfeltétel-ellenőrzés sikertelen volt. Ön is végrehajthatja a lekérdezések: `Operation Solution=SCOMAssessment` és `SCOMAssessmentRecommendation FocusArea=Prerequisites` a napló keresési megtekintéséhez az előfeltételek nem teljesülnek.

*Van egy `Failed to connect to the SQL Instance (….).` üzenet előfeltétel sikertelen. Mi az a probléma?* Az állapotfigyelő szolgáltatás folyamatban fut AdvisorAssessment.exe, a folyamat által összegyűjtött adatok, a felügyeleti kiszolgálón. Az állapot-ellenőrzéssel részeként a folyamat megpróbál csatlakozni az SQL Server, ahol az Operations Manager adatbázis szerepel-e. Ez a hiba akkor fordulhat elő, amikor tűzfalszabályok, tiltsa le a kapcsolatot az SQL Server-példányhoz.

*Milyen típusú adatok gyűjtése?* A következő típusú adatok összegyűjtése: - WMI-adatok - beállításjegyzék - adatok eseménynapló - adatok az Operations Manager adatainak Windows PowerShell, az SQL-lekérdezések és a fájl információkat gyűjtő keresztül.

*Miért kell konfigurálni egy futtató fiókot?* Az Operations Manager az SQL-lekérdezések futnak. Ahhoz, hogy futtatni egy futtató fiókot kell használnia, szükséges engedélyekkel. Ezenkívül, helyi rendszergazdai jogosultságok szükségesek a WMI-lekérdezés.

*Miért meg csak az első 10 javaslatokat?* Helyett felkínálva feladatok teljes körű, túlságosan listáját, ajánlott összpontosítani a rangsorolt javaslatok először címzést. Miután foglalkozni velük, további javaslatokat is elérhető lesz. Ha szeretné a részletes listája, megtekintheti az összes naplófájl-keresési javaslatok.

*Van mód figyelmen kívül hagyja az ajánlás?* Igen, tekintse meg a [figyelmen kívül hagyja a javaslatok](#Ignore-recommendations).


## <a name="next-steps"></a>További lépések

- [Naplók keresése](log-analytics-log-searches.md) megtudhatja, hogyan elemezhet a System Center Operations Manager állapotának ellenőrzése a részletes adatok és javaslatokat.
