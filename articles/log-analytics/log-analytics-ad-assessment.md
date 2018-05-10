---
title: Az Active Directory-környezet az Azure Naplóelemzés optimalizálása |} Microsoft Docs
description: Az Active Directory állapotát ellenőrző megoldás segítségével rendszeres időközönkénti kockázat és a környezetek állapotának megállapítása.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 016b5bafdf6543ad43e36a875e6aa68572b35f97
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Az Active Directory-környezet, az Active Directory állapotát ellenőrző megoldással a Naplóelemzési optimalizálása

![AD a szimbólum állapotának ellenőrzése](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Az Active Directory állapotát ellenőrző megoldás segítségével rendszeres időközönkénti kockázat és a környezetek állapotának megállapítása. Ez a cikk segít telepíti, és a megoldás, hogy a potenciális problémákat korrekciós műveletek hajthatók végre.

Ez a megoldás a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza. A javaslatok között négy kategóriába sorolni fókuszterületre vonatkozóan, amely gyorsan ismertetése a kockázat, és hajtsa végre a műveletet.

A javaslatok a Tudásbázis és a Microsoft szakemberei ügyfél látogatások ezer tapasztalatai alapulnak. Minden ajánlást ismerteti, miért probléma előfordulhat, hogy lényeges, hogy Önnek, és előfordulhat, hogy a javasolt.

Kiválaszthatja a fókuszterületre vonatkozóan, amely a szervezet számára fontos, és a nyomon követni a kockázat szabad és megfelelő környezet futtató felé.

Után, a megoldás felvett ellenőrzése kész, összefoglaló adatait fókuszterületek jelenik meg a **AD állapotának ellenőrzése** irányítópult az infrastruktúrát a környezetben. A következő szakaszok ismertetik, hogyan használja az információk a **AD állapotának ellenőrzése** irányítópultot, ahol megtekintheti, és ezután javasolt műveletek a Active Directory-kiszolgálói infrastruktúrában.  

![kép csempe AD állapotának ellenőrzése](./media/log-analytics-ad-assessment/ad-healthcheck-summary-tile.png)

![Az irányítópult AD állapotának ellenőrzése képe](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Előfeltételek

* Az Active Directory állapotának ellenőrzése megoldás a .NET-keretrendszer 4.5.2-es verzióját egy támogatott verziója szükséges, vagy fent telepítve a Microsoft Monitoring Agent (MMA) telepítése minden egyes számítógépen.  Az MMA ügynök System Center 2016 - Operations Manager és az Operations Manager 2012 R2 és a Naplóelemzés szolgáltatás használja.
* A megoldás a Windows Server 2008 és 2008 R2, Windows Server 2012 és 2012 R2 és Windows Server 2016 futtató tartományvezérlőket támogatja.
* A Naplóelemzési munkaterület hozzáadása az Active Directory állapotát ellenőrző megoldás az Azure-portálon az Azure piactérről.  Nincs szükség további konfigurációra.

  > [!NOTE]
  > A megoldás hozzáadása után a AdvisorAssessment.exe fájl kerül kiszolgálók ügynökkel. Konfigurációs adatok olvasása és elküldheti a Naplóelemzés szolgáltatás feldolgozásra a felhőben. A fogadott adatokhoz logika vonatkozik, és a felhőszolgáltatás-adatait rögzíti.
  >
  >

A tartományvezérlők, amelyek a tartomány tagjai kiértékelendő állapot-ellenőrzés végrehajtásához szükséges egy ügynök és a kapcsolat a következő támogatott módszerek egyikének használatával szolgáltatáshoz:

1. Telepítse a [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) Ha a tartományvezérlő nem már figyel a System Center 2016 - Operations Manager, illetve az Operations Manager 2012 R2.
2. Ha a számítógép megfigyelés alatt áll a System Center 2016 - Operations Manager, illetve az Operations Manager 2012 R2 és a felügyeleti csoport nem integrálva van a Naplóelemzési szolgáltatás, a tartományvezérlő is lehet, többhelyű a Log Analyticshez az adatok gyűjtéséhez és továbbítja a szolgáltatást, és továbbra is az Operations Manager által figyelt.  
3. Ellenkező esetben, ha az Operations Manager felügyeleti csoportjának integrálva van a szolgáltatás, kell hozzáadnia a tartományvezérlők, az adatok gyűjtésével a következő lépéseket a szolgáltatás által [ügynök által felügyelt számítógépek hozzáadása](log-analytics-om-agents.md#connecting-operations-manager-to-log-analytics) engedélyezése után a megoldás a munkaterületen.  

Az ügynök jelentéseket az Operations Manager felügyeleti csoport gyűjti az adatokat, a tartományvezérlő a hozzárendelt felügyeleti kiszolgálójának továbbítja, és a Log Analytics szolgáltatás közvetlenül a felügyeleti kiszolgálótól érkező majd érkezik.  Az adatok nem szerepel az Operations Manager-adatbázishoz.  

## <a name="active-directory-health-check-data-collection-details"></a>Active Directory állapotának ellenőrzése az gyűjtemény adatait

Active Directory állapotát ellenőrző gyűjti az adatokat, hogy engedélyezte-ügynök használatával az alábbi forrásokból:

- Beállításjegyzék
- LDAP
- .NET-keretrendszer
- Eseménynapló
- Active Directory Service interfaces (ADSI)
- Windows PowerShell
- Fájladatok
- A Windows Management Instrumentation (WMI)
- A DCDIAG eszköz API
- Fájlreplikációs szolgáltatás (NTFRS) tartozó API
- Egyéni C# kód

Adatok gyűjtése a tartományvezérlőn, és hét naponta Naplóelemzési továbbítja.  

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

**Teljesítmény és méretezhetőség** -e fókuszba területen látható ajánlásokat a szervezet informatikai infrastruktúrájának nő, győződjön meg arról, hogy az informatikai környezettől aktuális teljesítménykövetelményeknek megfelel-e, és képes válaszolni infrastruktúra módosítása szüksége van.

**Áttelepítés és a telepítés frissítéséhez** -e fókuszba területen látható ajánlásokat frissítéséhez, telepítse át, és Active Directory telepítése a meglévő infrastruktúra.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Kell megcéloznia 100 %-os pontozása minden fókusz területen?
Nem feltétlenül. Az ajánlások a Tudásbázis és a Microsoft szakemberei által ügyfél látogatások ezer keresztül szerzett tapasztalatok alapulnak. Azonban nincs két kiszolgáló infrastruktúrák megegyeznek, és előfordulhat, hogy több vagy kevesebb kapcsolódik, konkrét javaslatokért. Például bizonyos biztonsági javaslatok kevésbé fontos, ha a virtuális gépek nem érhetők el az Internet lehet. Egyes rendelkezésre állási javaslatok lehet kevésbé alacsony prioritást ad hoc adatgyűjtés és a reporting Services. Lehet, hogy egy összetett üzleti fontos problémák kevésbé fontos, hogy a kezdeti. Érdemes lehet azonosítani a fókusz a prioritások, és keresse meg, hogy a pontszámokat változnak az idők.

Minden javaslat arról, hogy miért fontos útmutatást tartalmazza. Ezt az útmutatást kell használnia annak kiértékelésére, hogy a javaslat végrehajtási, az informatikai szolgáltatások természetét és a szervezete üzleti igényeinek.

## <a name="use-health-check-focus-area-recommendations"></a>Használjon állapotának ellenőrzése a fókusz terület javaslatok
Azt követően, megtekintheti a javaslatok összegzése a állapotának ellenőrzése csempe az Azure portálon, a megoldás oldalon használatával.

Az összesített megfelelőségi értékelése az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Az egy fókuszban terület javaslatok megtekintése és a szükséges javítási műveletek
3. Kattintson a **áttekintése** a Naplóelemzési munkaterület az Azure portálon csempéjén.
4. A a **áttekintése** lapján kattintson a **Active Directory állapotát ellenőrző** csempére.
5. Az a **állapotának ellenőrzése** lapon. Ellenőrizze az összefoglaló információkat a fókusz terület paneleken egyikében, majd kattintson egy adott fókusz területre javaslatok megtekintéséhez.
6. A fókusz terület lapok egyikén tekintheti meg a környezetnek a rangsorolt ajánlásokat. Kattintson az ajánlás **érintett objektumok** miért a javaslatokkal kapcsolatos részletek megtekintéséhez.<br><br> ![kép ajánlások állapotának ellenőrzése](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-02.png)
7. Az ajánlott javítási műveletek hajthatók végre **javasolt műveletek**. A cikk intéztek, ha újabb értékelések azt jelzi, hogy a javasolt műveletek vették, és a megfelelőségi pontszám növeli. Javított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Figyelmen kívül hagyja a javaslatok
Ha figyelmen kívül hagyása kívánt ajánlásokat, létrehozhat egy szövegfájlt, amely Naplóelemzési fogja használni az értékelési eredmények jelennek meg javaslatok megelőzése érdekében.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Javaslatok, figyelmen kívül hagyja majd azonosításához
1. Az Azure-portálon a kijelölt munkaterülethez tartozó Naplóelemzési munkaterület lapon kattintson a **naplófájl-keresési** csempére.
2. A következő lekérdezés futtatásával lista ajánlásokat, amelyek nem tudták használni a környezetében.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Ez a naplófájl-keresési lekérdezés ábrázoló képernyőkép:<br><br> ![nem sikerült javaslatok](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

3. Válassza ki a javaslatok, amelyet szeretne figyelmen kívül hagyja. A RecommendationId az értékeket fogja használni a következő eljárásban.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozzon létre, és egy IgnoreRecommendations.txt szövegfájl használata
1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be, vagy adjon meg minden egyes javaslat, amelyet az Naplóelemzési figyelmen kívül hagyása külön sorban, és mentse és zárja be a fájlt a RecommendationId.
3. Helyezze el a fájlt a következő mappában található minden olyan számítógépen Naplóelemzési figyelmen kívül hagyja a javaslatok, ahová.
   * A Microsoft Monitoring-ügynökkel rendelkező számítógépek (közvetlenül vagy az Operations Manager keresztül csatlakozik) - *SystemDrive*: \Program Files\Microsoft figyelés Agent\Agent
   * Az Operations Manager 2012 R2 felügyeleti kiszolgálón - *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft
   * Az Operations Manager 2016 felügyeleti kiszolgálón - *SystemDrive*: System Center 2016\Operations Manager\Server \Program Files\Microsoft

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy javaslatokat figyelmen kívül hagyja a
A megadott javaslatok megjelölve után a következő ütemezett állapotának ellenőrzése futtatása, alapértelmezés szerint minden hét nap, *figyelmen kívül hagyott* és nem jelennek meg az irányítópulton.

1. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listában.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott ajánlott megtekintéséhez, távolítsa el a IgnoreRecommendations.txt fájlokat, vagy RecommendationIDs eltávolíthatja őket.

## <a name="ad-health-check-solutions-faq"></a>Az AD Health ellenőrzése megoldások – gyakori kérdések
*Milyen gyakran fut a rendszerállapot-ellenőrzés?*

* Az ellenőrzés hét naponta fut.

*Van mód konfigurálása, hogy milyen gyakran az az állapot-ellenőrzéssel fut?*

* Jelenleg nem.

*Ha egy másik kiszolgálóra az ügyfélállapot-ellenőrzés megoldást, felvett merül fel lesz az ellenőrzött*

* Igen, ha kiderül, hogy be van jelölve, majd a hét naponta.

*Ha egy kiszolgáló le van szerelve, ha azt eltávolítja a szolgáltatásból az állapot-ellenőrzés?*

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

## <a name="next-steps"></a>További lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-searches.md) megtudhatja, hogyan elemezhet részletes adatok AD állapotának ellenőrzése és javaslatokat.
