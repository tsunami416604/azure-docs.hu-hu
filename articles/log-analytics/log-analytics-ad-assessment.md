---
title: Az Active Directory-környezetet az Azure Log Analytics optimalizálása |} A Microsoft Docs
description: Az Active Directory állapotának ellenőrzése megoldás segítségével felmérheti a kockázatait és állapotát, a környezetek rendszeres időközönkénti.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c56a0239209f5c71130c9dd8173eed48f60a3b0a
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042070"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Optimalizálhatja a Active Directory környezetet az Active Directory állapotának ellenőrzése megoldás a Log Analyticsben

![AD állapotának ellenőrzése szimbólum](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Az Active Directory állapotának ellenőrzése megoldás segítségével felmérheti a kockázatokat és a kiszolgáló-környezetek állapotát rendszeres időközönkénti. Ez a cikk segítségével telepítheti és használhatja a megoldás, hogy a potenciális problémákat korrekciós műveleteket hajthatja végre.

Ez a megoldás a telepített kiszolgálói infrastruktúrát vonatkozó javaslatok rangsorolt listáját tartalmazza. Az ajánlások szerint vannak kategóriába sorolva négy különböző fókuszterületre vonatkozóan, amely segítségével gyorsan megismerheti a kockázatokat, és a szükséges műveletek.

A javaslatok tudással és ezer vevő a Microsoft mérnökei tapasztalatai alapulnak. Minden javaslat ismerteti, miért számít a probléma előfordulhat, hogy Önnek és a javasolt változások megvalósítása.

Kiválaszthatja, hogy a szervezet számára legfontosabb, és nyomon követheti az előrehaladást kockázati ingyenes és kifogástalan állapotú környezetét fókuszterületek.

Miután hozzáadta a megoldást, és egy ellenőrzés befejeződött, összefoglaló adatait fókuszterületek jelenik meg a **AD állapotának ellenőrzése** a környezetében az infrastruktúra-irányítópulton. A következő szakaszok ismertetik az információk használata az a **AD állapotának ellenőrzése** irányítópult, amelyen megtekintheti és megfelelő ajánlott műveletek az Active Directory-kiszolgáló infrastruktúra.  

![AD állapotának ellenőrzése csempét ábrázoló kép](./media/log-analytics-ad-assessment/ad-healthcheck-summary-tile.png)

![AD állapotának ellenőrzése irányítópult képe](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Előfeltételek

* Az Active Directory állapotának ellenőrzése megoldás támogatott .NET-keretrendszer 4.5.2-es verziója szükséges, vagy a fent telepített minden olyan számítógépen, a Microsoft Monitoring Agent (MMA) telepítve van.  Az MMA-ügynök System Center 2016 – Operations Manager és az Operations Manager 2012 R2 és a Log Analytics szolgáltatás használatára szolgál.
* A megoldás támogatja a Windows Server 2008 és 2008 R2, Windows Server 2012 és 2012 R2 és Windows Server 2016 rendszert futtató tartományvezérlő.
* Az Active Directory állapotának ellenőrzése megoldás hozzáadása az Azure Portalon az Azure marketplace-ről a Log Analytics-munkaterületet.  Nincs szükség további konfigurációra.

  > [!NOTE]
  > A megoldás hozzáadását követően a AdvisorAssessment.exe fájlt adnak hozzá, ügynökökkel kiszolgálókra. Konfigurációs adatok olvasása és feldolgozásához a felhőben a Log Analytics szolgáltatásnak küldi. A fogadott adatokat logikát alkalmaz, és a felhőszolgáltatás-adatait rögzíti.
  >
  >

Hajtsa végre az állapot-ellenőrzés a tartományvezérlők, amelyek ki kell értékelni a tartomány tagjai, szükségük van egy ügynök és a következő támogatott módszerek egyikének használatával a Log Analyticshez való kapcsolatot:

1. Telepítse a [a Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) , ha a tartományvezérlő már nem áll a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2.
2. Ha figyelhető a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2 és a felügyeleti csoport nincs integrálva a Log Analytics szolgáltatással, a tartományvezérlő is lehet, a Log Analytics adatokat gyűjtse és továbbítsa a többhelyű a szolgáltatást, és továbbra is az Operations Manager által figyelendő.  
3. Ellenkező esetben az Operations Manager felügyeleti csoport integrálva van a szolgáltatást, ha hozzá kell a tartományvezérlők, az adatgyűjtés a szolgáltatás a következő szakasz lépéseit [adja hozzá az ügynök által felügyelt számítógépek](log-analytics-om-agents.md#connecting-operations-manager-to-log-analytics) engedélyezése után a megoldás a munkaterületén.  

Az ügynököt a tartományvezérlőn, az Operations Manager felügyeleti csoport jelentéseket gyűjti az adatokat, továbbítja a hozzárendelt felügyeleti kiszolgálónak, és majd a felügyeleti kiszolgálóról közvetlenül a Log Analytics szolgáltatásnak továbbítja.  Az adatok nem szerepel, az Operations Manager-adatbázisokról.  

## <a name="active-directory-health-check-data-collection-details"></a>Az Active Directory állapotának ellenőrzése adatok gyűjtése részletei

Az Active Directory állapotának ellenőrzése gyűjti az adatokat, hogy engedélyezte-ügynök használatával a következő forrásokból:

- Beállításjegyzék
- LDAP
- .NET-keretrendszer
- Eseménynapló
- Az Active Directory Service interfaces (ADSI)
- Windows PowerShell
- Fájladatok
- Windows Management Instrumentation (WMI)
- A DCDIAG API-hívások
- Fájlreplikációs szolgáltatás (NTFRS) tartozó API
- Egyéni C#-kód

Adatokat gyűjti a tartományvezérlőn, és továbbítja a Log Analytics hét naponta.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Hogyan kerülnek előrébb a javaslatok megértése
Minden végrehajtott javaslat, amely azonosítja a relatív fontosságát az ajánlás súlyozási értéket kap. A 10 legfontosabb javaslatok láthatók.

### <a name="how-weights-are-calculated"></a>Hogyan számítják ki súlyok
Súlyozás három fő tényezők alapján az összesített értékek:

* A *valószínűségi* , hogy az azonosított probléma problémákat okoz. Nagyobb a valószínűsége annak használatára, az ajánlás nagyobb általános pontszámot.
* A *hatás* a szervezet azt problémát okozni, ha a probléma. Egy újabb hatás-adatbázisnak felel meg az ajánlás nagyobb általános pontszámot.
* A *erőfeszítés* történő megvalósításához szükséges. A javaslat egy kisebb összesített pontszám magasabb erőfeszítéssel állapotnak felel meg.

Az egyes javaslatok a súlyozás van a teljes elérhető egyes fókuszterület pontszám százalékban kifejezve. Például ha az adott javaslat a biztonság és megfelelőség fókuszterület 5 %-os pontszámot, a javaslat megvalósítása növeli az átfogó biztonsági és megfelelőségi pontszám szerint 5 %.

### <a name="focus-areas"></a>Fókuszterületek
**Biztonsági és megfelelőségi** -e fókuszterület esetleges biztonsági fenyegetések és a problémák, a vállalati szabályzatok és a technikai, jogszabályi és szabályozásoknak való megfelelőség követelményeinek való javaslatait mutatja be.

**Rendelkezésre állás és üzleti folytonosság** -e fókuszterület javaslatait a szolgáltatás rendelkezésre állása, az infrastruktúra és a vállalati védelme rugalmasságát mutatja be.

**Teljesítmény és méretezhetőség** -e fókuszterület látható ajánlásokat a szervezet IT-infrastruktúráját a növeléssel, ellenőrizze, hogy informatikai környezete megfelel az aktuális teljesítménykövetelményeknek, és tud válaszolni az infrastruktúra módosítása szüksége van.

**Frissítés, Migrálás és üzembe helyezés** -e fókuszterület segítséget frissítése, migrálása és üzembe helyezése az Active Directory a meglévő infrastruktúra javaslatait mutatja be.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Tárhelyeken minden fókuszterület a 100 %-os pontozása?
Nem feltétlenül. A javaslatok tudással és több ezer ügyfél által a Microsoft mérnökei szerzett tapasztalatok alapulnak. Azonban nem két kiszolgáló infrastruktúrák azonosak, és lehet, hogy több vagy kevesebb releváns konkrét javaslatokért. Például bizonyos biztonsági javaslatok kevésbé fontos, ha a virtuális gépek nem jelennek meg az interneten a lehet. Lehet, hogy egyes rendelkezésre állási javaslatok kevesebb releváns alacsony prioritású alkalmi adatok összegyűjtésére és a reporting services esetén. Érett fontos problémák a kevésbé fontos, hogy egy indítási is lehet. Előfordulhat, hogy szeretné azonosítani, melyik fókuszterületek a munkájukhoz, és keresse meg, hogyan a pontszámokat az idő előrehaladtával változik.

Minden javaslat, hogy miért fontos vonatkozó útmutatást tartalmaz. Ez az útmutató használjon kiértékelését, hogy a javaslat megvalósítása alkalmas, az informatikai szolgáltatások jellegét, és a szervezet igényeit.

## <a name="use-health-check-focus-area-recommendations"></a>Használat állapotának ellenőrzése fókusz területre vonatkozó javaslatok
A telepítés után megtekintheti a javaslatok összegzését a állapotának ellenőrzése csempe a megoldás oldalon az Azure Portal használatával.

Az összesített megfelelőségi értékeléseket az infrastruktúrát, és a-feltárás javaslatok megtekintése.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Egy fókuszterület javaslatok megtekintése, és korrekciós műveletek
3. Kattintson a **áttekintése** csempére a Log Analytics-munkaterület az Azure Portalon.
4. Az a **áttekintése** lap, kattintson a **Active Directory állapotának ellenőrzése** csempére.
5. Az a **állapotának ellenőrzése** lapon. tekintse át az összefoglaló adatokat az egyik a fókusz terület paneleket, majd kattintson egy adott fókuszterület javaslatok megtekintése.
6. A bármelyik, fókusz terület megtekintheti a rangsorolt javaslatok arról, hogy a környezetben. Alatt egy javaslatra kattint **érintett objektumok** Miért jön létre a javaslat részleteinek megtekintéséhez.<br><br> ![kép az ajánlások állapotának ellenőrzése](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-02.png)
7. Az ajánlott javítási műveleteket hajthatja végre **javasolt műveletek**. Intéztek a cikket, ha újabb értékelések rekordokat, amelyek a javasolt műveleteket származnak, és a megfelelőségi pontszám növeli. Kijavított elemek jelennek meg **átadott objektumok**.

## <a name="ignore-recommendations"></a>Hagyja figyelmen kívül a javaslatok
Ha javaslatoknál, amelyeket figyelmen kívül kívánja, létrehozhat egy szövegfájlt, amelynek használatával a Log Analytics megakadályozása javaslatokat az értékelés eredményeinek parancsot.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Javaslatok, amelyek figyelmen kívül hagyja majd azonosításához
1. Az Azure Portalon a kijelölt munkaterület a Log Analytics munkaterület oldalon, kattintson a **naplóbeli keresés** csempére.
2. A következő lista ajánlásokat, amelyek nem tudták lekérdezés használata a környezetében.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    A következő bejelentkezéshez a Naplókeresési lekérdezésen:<br><br> ![nem sikerült javaslatokat](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

3. Válassza ki a javaslatok, amelyek figyelmen kívül kívánja. Szeretné használni az értékeket a RecommendationId az alábbi eljárás írja le.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Hozhat létre és használhat egy IgnoreRecommendations.txt szövegfájl
1. Hozzon létre egy fájlt IgnoreRecommendations.txt.
2. Illessze be, vagy írjon be minden egyes RecommendationId minden javaslat, amelyet a Log Analyticsben, hogy figyelmen kívül, külön sorban, majd mentse és zárja be a fájlt.
3. Helyezze a fájlt a következő mappában minden olyan számítógépen a Log Analytics figyelmen kívül hagyja a javaslatok, ahová.
   * A Microsoft Monitoring Agent (közvetlenül vagy Operations Manager keresztül csatlakozik) – a számítógépeken *SystemDrive*: Monitoring Agent\Agent \Program Files\Microsoft
   * Az Operations Manager 2012 R2 felügyeleti kiszolgálón – *SystemDrive*: System Center 2012 R2\Operations Manager\Server \Program Files\Microsoft
   * Az Operations Manager 2016 felügyeleti kiszolgálón – *SystemDrive*: System Center 2016\Operations Manager\Server \Program Files\Microsoft

### <a name="to-verify-that-recommendations-are-ignored"></a>Győződjön meg arról, hogy a javaslatok figyelmen kívül hagyja a
A következő ütemezett egészségügyi ellenőrzés futtatásakor a szolgáltatás alapértelmezés szerint minden hét nap, miután a megadott javaslatok vannak megjelölve az *figyelmen kívül hagyva* és nem jelenik meg az irányítópulton.

1. A következő naplófájl-keresési lekérdezések segítségével a figyelmen kívül hagyott javaslatok listája.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Ha később úgy dönt, hogy szeretné-e figyelmen kívül hagyott javaslatok, IgnoreRecommendations.txt fájlok eltávolítása, vagy távolíthatja el RecommendationIDs őket.

## <a name="ad-health-check-solutions-faq"></a>AD állapotának ellenőrzése megoldások – gyakori kérdések
*Milyen gyakran fut a állapot-ellenőrzése?*

* Az ellenőrzés hét naponta fut.

*Van mód konfigurálása, hogy milyen gyakran fut a az állapot-ellenőrzés?*

* Jelenleg nem.

*Ha egy másik kiszolgáló fel van derítve, miután hozzá van adva egy health ellenőrzési megoldás az lesz, ellenőrizni kell*

* Igen, ez be van jelölve, majd a hét naponta felderítése után.

*Ha egy kiszolgáló le van szerelve, amikor azt eltávolítjuk az állapot-ellenőrzés?*

* Ha a kiszolgáló nem küldenek adatokat a 3 hét, a rendszer eltávolítja.

*Mi a neve, a folyamat, amely az adatgyűjtés?*

* AdvisorAssessment.exe

*Mennyi ideig tart a gyűjtendő adatokat?*

* A tényleges adatok gyűjtése a kiszolgálón körülbelül 1 órát vesz igénybe. Ez hosszabb időt vehet igénybe a kiszolgálókon, amelyek az Active Directory-kiszolgálók nagy száma.

*Van mód a konfigurálását, amikor az adatgyűjtés történik?*

* Jelenleg nem.

*Miért jelennek meg a csak az első 10-javaslatok?*

* Helyett így elsöprő teljesnek feladatot, azt javasoljuk, hogy arra összpontosítunk, először azoknak a rangsorolt javaslatok. Oldja meg őket, miután további javaslatokat is elérhetőek lesznek. Ha inkább a részletes listát, összes ajánlás naplóbeli keresés használatával tekintheti meg.

*Van mód figyelmen kívül hagyja a javaslatot?*

* Igen, tekintse meg [figyelmen kívül hagyja a javaslatok](#ignore-recommendations) című fenti szakaszban.

## <a name="next-steps"></a>További lépések
* Használat [Log Analytics naplóbeli kereséseivel](log-analytics-log-searches.md) megtudhatja, hogyan elemezheti a részletes adatokat AD állapotának ellenőrzése és javaslatok.
