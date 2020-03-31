---
title: Az Active Directory-környezet optimalizálása az Azure Monitor segítségével | Microsoft dokumentumok
description: Az Active Directory állapot-ellenőrzési megoldás segítségével rendszeres időközönként felmérheti a környezetek kockázatát és állapotát.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: 57c474c8391168702154b71e0c454253ab921dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667227"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Az Active Directory környezet-optimalizálása az Azure Monitor Active Directory Health Check megoldásával

![AD állapotfelmérés szimbóluma](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Az Active Directory állapot-ellenőrzési megoldással rendszeres időközönként felmérheti a kiszolgálói környezetek kockázatát és állapotát. Ez a cikk segít a megoldás telepítésében és használatában, így korrekciós műveleteket tehet a lehetséges problémákra.

Ez a megoldás a telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza. A javaslatok négy kiemelt területre vannak kategorizálva, amelyek segítenek a kockázat gyors megértésében és a cselekvésben.

A javaslatok a Microsoft mérnökei által több ezer ügyféllátogatás során szerzett tudáson és tapasztalaton alapulnak. Minden egyes javaslat útmutatást nyújt arról, hogy egy probléma miért lehet fontos az Ön számára, és hogyan valósíthatja meg a javasolt módosításokat.

Kiválaszthatja a szervezet számára legfontosabb fókuszterületeket, és nyomon követheti a kockázatmentes és egészséges környezet megmaradása felé tett előrehaladást.

Miután hozzáadta a megoldást, és az ellenőrzés befejeződött, a fókuszterületek összefoglaló információi megjelennek a környezetében lévő infrastruktúra **AD állapot-ellenőrzés** irányítópultján. Az alábbi szakaszok ismertetik, hogyan használható az **AD állapot-ellenőrzés** irányítópulton található információk, ahol megtekintheti, majd végrehajthatja az Active Directory-kiszolgáló infrastruktúrájának ajánlott lépéseit.  

![Az AD állapot-ellenőrzés csempéjének képe](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![Az AD állapotfelmérés irányítópultjának képe](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Előfeltételek

* Az Active Directory állapot-ellenőrzési megoldáshoz a .NET Framework 4.6.2 vagy újabb verzió támogatott verziója van telepítve minden olyan számítógépen, amelyen telepítve van a Windows Log Analytics ügynöke (más néven Microsoft Monitoring Agent (MMA)).  Az ügynököt a System Center 2016 – Operations Manager, Operations Manager 2012 R2 és az Azure Monitor használja.
* A megoldás támogatja a Windows Server 2008 és 2008 R2, Windows Server 2012 és 2012 R2 és Windows Server 2016 rendszert futtató tartományvezérlőket.
* A Log Analytics munkaterületet, hogy adja hozzá az Active Directory állapot-ellenőrzési megoldás az Azure-piactérről az Azure Portalon. Nincs szükség további konfigurációra.

  > [!NOTE]
  > A megoldás hozzáadása után az AdvisorAssessment.exe fájl hozzáadódik az ügyintézőkkel rendelkező kiszolgálókhoz. A rendszer beolvassa a konfigurációs adatokat, majd elküldi az Azure Monitornak feldolgozásra. A felhőszolgáltatás egy logikát alkalmaz a kapott adatokon, és rögzíti azokat.
  >
  >

Az állapot-ellenőrzést a kiértékelendő tartományban lévő tartományvezérlőkön való állapotfelmérés végrehajtásához az adott tartomány minden tartományvezérlőjének ügynökre és kapcsolatra van szüksége az Azure Monitorhoz az alábbi támogatott módszerek egyikével:

1. Telepítse a [Windows Log Analytics-ügynökét,](../../azure-monitor/platform/agent-windows.md) ha a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2 még nem figyeli a tartományvezérlőt.
2. Ha a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2 szolgáltatással figyeli, és a felügyeleti csoport nincs integrálva az Azure Monitorszolgáltatással, a tartományvezérlő több-homed az Azure Monitor segítségével adatokat gyűjthet, és továbbítja a szolgáltatást, és továbbra is az Operations Manager felügyeli.  
3. Ellenkező esetben, ha az Operations Manager felügyeleti csoport integrálva van a szolgáltatással, hozzá kell adnia a tartományvezérlőket a szolgáltatás általi adatgyűjtéshez az [ügynök által felügyelt számítógépek hozzáadása](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) című részben leírt lépéseket követve, miután engedélyezte a megoldást a munkaterületen.  

A tartományvezérlőn lévő ügynök, amely egy Operations Manager felügyeleti csoportnak jelent, adatokat gyűjt, továbbítja a hozzárendelt felügyeleti kiszolgálóra, majd közvetlenül a felügyeleti kiszolgálóról küldi el az Azure Monitornak.  Az adatok nem lesznek beírva az Operations Manager-adatbázisokba.  

## <a name="active-directory-health-check-data-collection-details"></a>Az Active Directory Health Check által végzett adatgyűjtés részletei

Az Active Directory állapotfelmérés az engedélyezett ügynök használatával gyűjt adatokat a következő forrásokból:

- Registry
- LDAP
- .NET-keretrendszer
- Eseménynapló
- Active Directory-szolgáltatás felhasználói felületei (ADSI)
- Windows PowerShell
- Fájladatok
- Windows Management Instrumentation (WMI)
- DCDIAG eszköz API
- Fájlreplikációs szolgáltatás (NTFRS) API
- Egyéni C# kód

Az adatokat a tartományvezérlőgyűjti, és hétnaponta továbbítja az Azure Monitornak.  

## <a name="understanding-how-recommendations-are-prioritized"></a>A javaslatok rangsorolásának ismertetése

Minden ajánlás súlyozási értéket kap, amely azonosítja az ajánlás relatív fontosságát. Csak a 10 legfontosabb ajánlások jelennek meg.

### <a name="how-weights-are-calculated"></a>A súlyozás kiszámítása

A súlyozás három fő tényezőn alapuló összesített érték:

* Annak *valószínűsége,* hogy egy probléma azonosított problémákat okoz. Nagyobb valószínűséggel felel meg az ajánlás nagyobb összpontszámának.
* A probléma *hatása* a szervezetre, ha problémát okoz. A nagyobb hatás megegyezik a nagyobb általános pontszám az ajánlás.
* Az ajánlás végrehajtásához szükséges *erőfeszítés.* A nagyobb erőfeszítés megegyezik egy kisebb általános pontszám az ajánlás.

Az egyes ajánlások súlyozását az egyes kiemelt területre rendelkezésre álló összpontszám százalékában fejezik ki. Ha például a Biztonság és megfelelőség kiemelt területen egy javaslat 5%-os pontszámmal rendelkezik, a javaslat megvalósítása 5%-kal növeli a teljes biztonsági és megfelelőségi pontszámot.

### <a name="focus-areas"></a>Fókuszterületek

**Biztonság és megfelelőség** – Ez a kiemelt terület a lehetséges biztonsági fenyegetésekre és szabálysértésekre, a vállalati házirendekre, valamint a műszaki, jogi és jogszabályi megfelelőségi követelményekre vonatkozó ajánlásokat mutatja be.

**Rendelkezésre állás és üzletmenet-folytonosság** – Ez a kiemelt terület a szolgáltatás rendelkezésre állására, az infrastruktúra rugalmasságára és az üzleti védelemre vonatkozó javaslatokat mutatja be.

**Teljesítmény és méretezhetőség** – Ez a kiemelt terület javaslatokat jelenít meg a szervezet informatikai infrastruktúrájának növekedéséhez, annak biztosításához, hogy az informatikai környezet megfeleljen az aktuális teljesítménykövetelményeknek, és képes legyen reagálni a változó infrastrukturális igényekre.

**Frissítés, áttelepítés és üzembe helyezés** – Ez a kiemelt terület javaslatokat tartalmaz az Active Directory meglévő infrastruktúrára történő frissítéséhez, áttelepítéséhez és telepítéséhez.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Érdemes 100%-ra törekedni minden fókuszterületen?

Nem feltétlenül. A javaslatok a Microsoft mérnökei által több ezer ügyféllátogatás során szerzett tudáson és tapasztalaton alapulnak. Azonban nincs két kiszolgálói infrastruktúra azonos, és a konkrét javaslatok többé-kevésbé relevánsak lehetnek az Ön számára. Egyes biztonsági javaslatok például kevésbé relevánsak lehetnek, ha a virtuális gépek nincsenek kitéve az internetnek. Egyes rendelkezésre állási javaslatok kevésbé relevánsak lehetnek az alacsony prioritású ad hoc adatgyűjtést és -jelentéskészítést biztosító szolgáltatások esetében. Az érett vállalkozás számára fontos kérdések kevésbé fontosak lehetnek az induló vállalkozások számára. Előfordulhat, hogy meg szeretné határozni, hogy mely fókuszterületek a prioritások, majd nézze meg, hogyan változnak a pontszámok az idő múlásával.

Minden ajánlás útmutatást tartalmaz arról, hogy miért fontos. Ezzel az útmutatóval kiértékelheti, hogy a javaslat megvalósítása megfelelő-e az Informatikai szolgáltatások jellegétől és a szervezet üzleti igényeitől.

## <a name="use-health-check-focus-area-recommendations"></a>Az állapotfelmérés fókuszterületének ajánlásainak használata

A telepítés után megtekintheti a javaslatok összegzését az Azure Portal megoldáslapján található Állapotfelmérés csempe használatával.

Tekintse meg az infrastruktúra összesített megfelelőségi értékeléseit, majd részletezze a javaslatokat.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>A kiemelt területre vonatkozó ajánlások megtekintése és korrekciós intézkedések meghozása

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Az **Áttekintés** lapon kattintson az **Active Directory állapot-ellenőrzési csempéjére.**

2. Az **Állapotfelmérés** lapon tekintse át az összefoglaló információkat a fókuszterület egyik paneljén, majd kattintson az egyikre az adott fókuszterületre vonatkozó javaslatok megtekintéséhez.

3. A fókuszterület bármelyik oldalán megtekintheti a környezetéhez készített rangsorolt javaslatokat. Kattintson az **Érintett objektumok** csoportban egy javaslatra a javaslat meghirdetésének részleteinek megtekintéséhez.

    ![Az állapotfelmérésre vonatkozó ajánlások képe](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. A **Javasolt műveletek**részben javasolt korrekciós intézkedéseket végrehajthatja. Az elem kezelése után a későbbi értékelések rögzítik az ajánlott műveleteket, és a megfelelőségi pontszám növekedni fog. A javított elemek **átadott objektumokként**jelennek meg.

## <a name="ignore-recommendations"></a>Javaslatok figyelmen kívül hagyása

Ha olyan javaslatokat szeretne figyelmen kívül hagyni, hogy hozzon létre egy szöveges fájlt, amely az Azure Monitor fogja használni, hogy megakadályozza a javaslatok jelennek meg az értékelési eredmények.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>A figyelmen kívül hagyott ajánlások azonosítása

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Az alábbi lekérdezéssel felsorolhatja azokat a javaslatokat, amelyek a környezetben lévő számítógépek esetében sikertelenek voltak.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Itt egy képernyőkép a naplólekérdezést mutatja:<

![sikertelen ajánlások](media/ad-assessment/ad-failed-recommendations.png)

Válassza ki a figyelmen kívül hagyni kívánt javaslatokat. A következő eljárásban az Ajánlásazonosító értékeit fogja használni.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt szövegfájl létrehozása és használata

1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.

2. Illessze be vagy írja be az egyes javaslatokkal kapcsolatos javaslatokat, amelyeket az Azure Monitor figyelmen kívül szeretne hagyni egy külön sorban, majd mentse és zárja be a fájlt.

3. Helyezze a fájlt a következő mappába minden olyan számítógépen, ahol azt szeretné, hogy az Azure Monitor figyelmen kívül hagyja a javaslatokat.

   * A Microsoft Monitoring Agent (közvetlenül vagy az Operations Manager en keresztül) rendelkező számítógépeken - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Az Operations Manager 2012 R2 felügyeleti kiszolgálón - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Az Operations Manager 2016 felügyeleti kiszolgálón - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>A javaslatok figyelmen kívül hagyása

A következő ütemezett állapot-ellenőrzés futtatása után alapértelmezés szerint hétnaponta a megadott javaslatok *figyelmen kívül hagyva,* és nem jelennek meg az irányítópulton.

1. A következő naplólekérdezések segítségével listázhatja az összes figyelmen kívül hagyott javaslatot.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Ha később úgy dönt, hogy figyelmen kívül hagyott javaslatokat szeretne látni, távolítsa el az IgnoreRecommendations.txt fájlokat, vagy távolítsa el belőlük az ajánlási azonosítókat.

## <a name="ad-health-check-solutions-faq"></a>AD Health Check megoldás – gyakori kérdések

*Milyen ellenőrzéseket végez az AD-értékelési megoldás?*

* A következő lekérdezés az összes jelenleg végrehajtott ellenőrzés leírását jeleníti meg:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Az eredmények ezután exportálhatók az Excelbe további ellenőrzés céljából.

*Milyen gyakran fut az állapotfelmérés?*

* A csekk hétnaponta működik.

*Van mód annak konfigurálására, hogy milyen gyakran fut az állapot-ellenőrzés?*

* Jelenleg nem.

*Ha egy másik szerver tanoncután már hozzá egy állapot-ellenőrzési megoldás, majd ellenőrizni*

* Igen, amint felfedezik, akkor antól, hétnaponta ellenőrzik.

*Ha egy kiszolgálót leszerelnek, mikor távolítják el az állapotfelmérésből?*

* Ha a kiszolgáló 3 hétig nem küld adatokat, a rendszer eltávolítja azt.

*Mi a neve annak a folyamatnak, amely az adatgyűjtést végzi?*

* TanácsadóÉrtékelése.exe

*Mennyi ideig tart az adatok gyűjtése?*

* A tényleges adatgyűjtés a kiszolgálón körülbelül 1 órát vesz igénybe. A nagyszámú Active Directory-kiszolgálóval kiszolgáló kiszolgálókon hosszabb időt vehet igénybe.

*Van mód az adatgyűjtés időpontjára?*

* Jelenleg nem.

*Miért csak a 10 legfontosabb javaslat jelenjen meg?*

* Ahelyett, hogy a feladatok teljes körű, elsöprő listáját adná meg, javasoljuk, hogy először a rangsorolt ajánlások kezelésére összpontosítson. Miután megszólította őket, további javaslatok válnak elérhetővé. Ha inkább a részletes listát szeretné látni, az összes javaslatot megtekintheti egy naplólekérdezés használatával.

*Van rá mód, hogy figyelmen kívül hagyja az ajánlást?*

* Igen, [lásd: A javaslatok figyelmen kívül hagyása](#ignore-recommendations) című szakaszban.

## <a name="next-steps"></a>További lépések

Az [Azure Monitor naplólekérdezéseivel](../log-query/log-query-overview.md) megtudhatja, hogyan elemezheti az AD állapot-ellenőrzése részletes adatait és javaslatait.
