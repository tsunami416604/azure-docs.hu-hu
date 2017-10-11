---
title: "Az Active Directory-környezet az Azure Naplóelemzés optimalizálása |} Microsoft Docs"
description: "Az Active Directory-értékelési megoldás segítségével rendszeres időközönkénti kockázat és a környezetek állapotának megállapítása."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97368f0b9e89ffd0cd982b6e8670d5a1f62ad42c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Az Active Directory-környezet, az Active Directory-értékelési megoldás a Naplóelemzési optimalizálása

![AD Assessment szimbólum](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Az Active Directory-értékelési megoldás segítségével rendszeres időközönkénti kockázat és a környezetek állapotának megállapítása. Ez a cikk segít telepíti, és a megoldás, hogy a potenciális problémákat korrekciós műveletek hajthatók végre.

Ez a megoldás a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza. A javaslatok között négy kategóriába sorolni fókuszterületre vonatkozóan, amely gyorsan ismertetése a kockázat, és hajtsa végre a műveletet.

A javaslatok a Tudásbázis és a Microsoft szakemberei ügyfél látogatások ezer tapasztalatai alapulnak. Minden ajánlást ismerteti, miért probléma előfordulhat, hogy lényeges, hogy Önnek, és előfordulhat, hogy a javasolt.

Kiválaszthatja a fókuszterületre vonatkozóan, amely a szervezet számára fontos, és a nyomon követni a kockázat szabad és megfelelő környezet futtató felé.

Után, a megoldás felvett értékelését fejezhető be, összefoglaló adatait fókuszterületek jelenik meg a **AD Assessment** irányítópult az infrastruktúrát a környezetben. A következő szakaszok ismertetik, hogyan használja az információk a **AD Assessment** irányítópultot, ahol megtekintheti, és ezután javasolt műveletek a Active Directory-kiszolgálói infrastruktúrában.

![SQL Assessment csempe képe](./media/log-analytics-ad-assessment/ad-tile.png)

![SQL Assessment irányítópult képe](./media/log-analytics-ad-assessment/ad-assessment.png)

## <a name="installing-and-configuring-the-solution"></a>Telepítése és a megoldás konfigurálása
Az alábbi információk segítségével telepítse és konfigurálja a megoldásokat.

* Ügynökök telepítenie kell a tartományvezérlőkön, amelyek tagjai a tartománynak kell kiértékelni.
* Az Active Directory-értékelési megoldás a .NET-keretrendszer 4 támogatott verzióját igényli (4.5.2 vagy újabb) valamennyi számítógépen, amelyen OMS-ügynököt.
* Az Active Directory-értékelési megoldás hozzáadása az OMS-munkaterület a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ADAssessmentOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).  Nincs szükség további konfigurációra.

  > [!NOTE]
  > A megoldás hozzáadása után a AdvisorAssessment.exe fájl kerül kiszolgálók ügynökkel. Konfigurációs adatok olvasása és küldi el feldolgozásra a felhőben az OMS szolgáltatáshoz a rendszer. A fogadott adatokhoz logika vonatkozik, és a felhőszolgáltatás-adatait rögzíti.
  >
  >

## <a name="active-directory-assessment-data-collection-details"></a>Active Directory Assessment az gyűjtemény adatait

Active Directory Assessment használatával az ügynököket, amelyeket úgy állította be az alábbi forrásokból gyűjti az adatokat:

- Beállításjegyzék gyűjtő használatát
- LDAP-gyűjtő
- .NET-keretrendszer
- Esemény naplógyűjtő
- Active Directory Service interfaces (ADSI)
- Windows PowerShell
- Fájl adatgyűjtők
- A Windows Management Instrumentation (WMI)
- A DCDIAG eszköz API
- Fájlreplikációs szolgáltatás (NTFRS) tartozó API
- Egyéni C# kód


Az alábbi táblázat adatgyűjtési módszerek ügynökök, az Operations Manager (SCOM) szükséges-e, és milyen gyakran adatgyűjtés ügynök által.

| Platform | Közvetlen ügynök | SCOM-ügynököt | Azure Storage | SCOM szükséges? | Felügyeleti csoport keresztül küldött SCOM ügynök adatok | Gyűjtemény gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |7 nap |

## <a name="understanding-how-recommendations-are-prioritized"></a>Hogyan kerülnek előrébb a javaslatok megértése
Minden javaslat végrehajtott, amely azonosítja a relatív fontosságát az ajánlás súlyozási értéket kap. A 10 legfontosabb javaslatok láthatók.

### <a name="how-weights-are-calculated"></a>Hogyan súlyok kiszámítása
Súlyozás alapján három kulcsfontosságú szerepet játszik az összesített értékek:

* A *valószínűség* hibát azonosított problémákat okoz. Nagyobb a valószínűsége annak megfelel az ajánlás nagyobb általános pontszámot.
* A *hatás* , a problémát a szervezetben, ha azt okozzák a problémát. Egy magasabb hatása megfelel az ajánlás nagyobb általános pontszámot.
* A *elérhető* a javaslat végrehajtásához szükséges. Egy újabb elérhető megfelel az ajánlás kisebb általános pontszámot.

Minden ajánlást a súlyozási arányában a teljes pontszám minden fókusz területen érhető el. Például ha a biztonsági és megfelelőségi fókusz területen ajánlást 5 %-os pontszámot, amelyekre érvényes végrehajtási növeli a teljes biztonsági és megfelelőségi pontszám által 5 %.

### <a name="focus-areas"></a>Fókuszterületek
**Biztonsági és megfelelőségi** -e fókuszba területen látható esetleges biztonsági fenyegetéseket jelezhetnek és a behatolás, a vállalati házirendeket és a technikai, jogszabályi és szabályozásoknak megfelelőségi előírásokra vonatkozó javaslatok.

**Rendelkezésre állás és üzleti folytonosság** -fókusz itt megtekinthető a szolgáltatás rendelkezésre állása, a rugalmasság, az infrastruktúra és az üzleti védelmét javaslatok.

**Teljesítmény és méretezhetőség** -e fókuszba területen látható ajánlásokat a szervezet informatikai infrastruktúrájának nő, győződjön meg arról, hogy az informatikai környezet megfelel az aktuális teljesítménykövetelményeknek, és képes reagálni a infrastruktúrához.

**Áttelepítés és a telepítés frissítéséhez** -e fókuszba területen látható ajánlásokat frissítéséhez, telepítse át, és Active Directory telepítése a meglévő infrastruktúra.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Kell megcéloznia 100 %-os pontozása minden fókusz területen?
Nem feltétlenül. Az ajánlások a Tudásbázis és a Microsoft szakemberei által ügyfél látogatások ezer keresztül szerzett tapasztalatok alapulnak. Azonban nincs két kiszolgáló infrastruktúrák megegyeznek, és előfordulhat, hogy több vagy kevesebb kapcsolódik, konkrét javaslatokért. Például bizonyos biztonsági javaslatok kevésbé fontos, ha a virtuális gépek nem érhetők el az Internet lehet. Egyes rendelkezésre állási javaslatok lehet kevésbé alacsony prioritást ad hoc adatgyűjtés és a reporting Services. Lehet, hogy egy összetett üzleti fontos problémák kevésbé fontos, hogy a kezdeti. Érdemes lehet azonosítani a fókusz a prioritások, és keresse meg, hogy a pontszámokat változnak az idők.

Minden javaslat arról, hogy miért fontos útmutatást tartalmazza. Ezt az útmutatást kell használnia annak kiértékelésére, hogy a javaslat végrehajtási, az informatikai szolgáltatások természetét és a szervezete üzleti igényeinek.

## <a name="use-assessment-focus-area-recommendations"></a>Kiértékelés fókusz terület ajánlásai használata
Egy értékelési megoldás az OMS használata előtt rendelkeznie kell a telepített megoldás. További megoldások telepítéséről lásd: [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). A telepítés után javaslatok összefoglalása használata az értékelési csempe az OMS-áttekintése oldalon is megtekintheti.

Az összesített megfelelőségi értékelése az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Az egy fókuszban terület javaslatok megtekintése és a szükséges javítási műveletek
1. Az a **áttekintése** lapján kattintson a **Assessment** a kiszolgálói infrastruktúra csempéjén.
2. Az a **Assessment** lapon. Ellenőrizze az összefoglaló információkat a fókusz terület paneleken egyikében, majd kattintson egy adott fókusz területre javaslatok megtekintéséhez.
3. A fókusz terület lapok egyikén tekintheti meg a környezetnek a rangsorolt ajánlásokat. Kattintson az ajánlás **érintett objektumok** miért a javaslatokkal kapcsolatos részletek megtekintéséhez.  
    ![Kiértékelés ajánlásai képe](./media/log-analytics-ad-assessment/ad-focus.png)
4. Az ajánlott javítási műveletek hajthatók végre **javasolt műveletek**. A cikk intéztek, ha újabb értékelések azt jelzi, hogy a javasolt műveletek vették, és a megfelelőségi pontszám növeli. Javított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Figyelmen kívül hagyja a javaslatok
Ha figyelmen kívül hagyása kívánt ajánlásokat, létrehozhat egy szövegfájlt, amely OMS fogja használni az értékelési eredmények jelennek meg javaslatok megelőzése érdekében.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Javaslatok, figyelmen kívül hagyja majd azonosításához
1. Jelentkezzen be a munkaterületet, és nyissa meg a keresési napló. A következő lekérdezés futtatásával lista ajánlásokat, amelyek nem tudták használni a környezetében.

   ```
   Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a fenti lekérdezés megváltozna a következők.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

   Ez a naplófájl-keresési lekérdezés ábrázoló képernyőkép: ![nem lehetett ajánlásait](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)
2. Válassza ki a javaslatok, amelyet szeretne figyelmen kívül hagyja. A RecommendationId az értékeket fogja használni a következő eljárásban.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozzon létre, és egy IgnoreRecommendations.txt szövegfájl használata
1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be, vagy adjon meg minden egyes javaslat, amelyet az Naplóelemzési figyelmen kívül hagyása külön sorban, és mentse és zárja be a fájlt a RecommendationId.
3. Helyezze el a fájlt a következő mappában található minden olyan számítógépen OMS figyelmen kívül hagyja a javaslatok, ahová.
   * A Microsoft Monitoring-ügynökkel rendelkező számítógépek (közvetlenül vagy az Operations Manager keresztül csatlakozik) - *SystemDrive*: \Program Files\Microsoft figyelés Agent\Agent
   * Az Operations Manager felügyeleti kiszolgálón - *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy javaslatokat figyelmen kívül hagyja a
A megadott javaslatok megjelölve után a következő ütemezett értékelési fut, alapértelmezésben 7 naponta, *figyelmen kívül hagyott* és nem jelenik meg a assessment irányítópult.

1. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listában.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a fenti lekérdezés megváltozna a következők.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott ajánlott megtekintéséhez, távolítsa el a IgnoreRecommendations.txt fájlokat, vagy RecommendationIDs eltávolíthatja őket.

## <a name="ad-assessment-solutions-faq"></a>AD Assessment megoldások – gyakori kérdések
*Milyen gyakran fut a felmérés elvégzéséhez?*

* Az értékelés 7 naponta fut.

*Van mód konfigurálása, hogy milyen gyakran az értékelés fut?*

* Jelenleg nem.

*Ha a másik kiszolgáló fel van derítve, miután felvett egy értékelési megoldás, az értékelési?*

* Igen, ha kiderül, hogy megfelelőségét ellenőrizni kell, majd a gyakoriság 7 nap.

*Ha egy kiszolgáló le van szerelve, ha azt eltávolítja a szolgáltatásból az értékelés?*

* Ha a kiszolgáló nem időközönként adatokat küldenek a 3 hét, a rendszer eltávolítja.

*Mi az a folyamat, amelyet az adatgyűjtést a neve?*

* AdvisorAssessment.exe

*Mennyi időt vesz igénybe a gyűjtött adatok?*

* A tényleges adatok gyűjtése a kiszolgálón a körülbelül 1 órát vesz igénybe. Azt is tovább tarthat a kiszolgálókon, amelyek nagyszámú Active Directory-kiszolgálók.

*Van mód konfigurálása az adatgyűjtés?*

* Jelenleg nem.

*Miért meg csak az első 10 javaslatokat?*

* Helyett felkínálva feladatok túlságosan teljesnek, javasoljuk, összpontosítani a rangsorolt javaslatok először címzést. Miután foglalkozni velük, további javaslatokat is elérhető lesz. Ha szeretné a részletes listája, megtekintheti az összes naplófájl-keresési javaslatok.

*Van mód figyelmen kívül hagyja az ajánlás?*

* Igen, tekintse meg [figyelmen kívül hagyja a javaslatok](#ignore-recommendations) fenti szakaszban.

## <a name="next-steps"></a>Következő lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-searches.md) részletes AD megfelelőségvizsgálati adatai és a javaslatok megtekintéséhez.
