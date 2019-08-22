---
title: AKS-fürt teljesítmény figyelése és az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan megtekintheti és a teljesítmény- és naplófájl-adatok elemzése az Azure Monitor for containers szolgáltatásban.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 154848c33960cb78b10c58e7a39ddec669d4fae0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872989"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Az AKS fürtteljesítmény és az Azure Monitor-tárolókhoz ismertetése
A tárolók Azure Monitor a teljesítmény-diagramok és az állapot használatával figyelheti az Azure Kubernetes Service-(ak-) fürtök munkaterhelését két perspektívából. A monitort közvetlenül egy AK-fürtről is figyelheti, vagy a Azure Monitor-előfizetésben lévő összes AK-fürtöt nyomon követheti. A Azure Container Instances megtekintése akkor is lehetséges, ha egy adott AK-fürtöt figyel.

Ez a cikk segít megérteni a két perspektívát, és hogyan segíti a Azure Monitor az észlelt problémák gyors felmérését, kivizsgálását és megoldását.

További információ a tárolók Azure Monitorének engedélyezéséről: [Azure monitor a tárolók számára](container-insights-onboard.md).

A Azure Monitor egy több fürtből álló nézetet biztosít, amely a Linux és a Windows Server 2019 rendszert futtató összes figyelt AK-fürt állapotát megjeleníti az előfizetésekben található erőforráscsoportok között. Azt mutatja, hogy a rendszer a megoldás által nem figyelt AK-fürtöket észlelt. Azonnal megismerheti a fürt állapotát, és itt megtekintheti a csomópont-és vezérlő teljesítmény lapját, vagy megnyithatja a fürthöz tartozó teljesítménymutatókat. A felderített és nem ellenőrzöttként azonosított AK-fürtök esetében bármikor engedélyezheti a figyelést. 

A Windows Server-fürtöknek a Linux-fürthöz képest Azure Monitorekkel való figyelésével kapcsolatos fő különbségek a következők:

- A memóriabeli RSS-metrika nem érhető el a Windows-csomópontok és-tárolók esetében.
- A lemezes tárolás kapacitására vonatkozó információk nem érhetők el Windows-csomópontok esetén.
- Az élő naplók támogatása a Windows-tároló naplófájljainak kivételével érhető el.
- Csak a pod környezetek figyelhetők meg, nem pedig a Docker-környezetek.
- Az előzetes kiadásban legfeljebb 30 Windows Server-tároló támogatott. Ez a korlátozás nem vonatkozik a Linux-tárolók esetében. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Az Azure Monitor több fürt megtekintése

Az összes telepített AK-fürt állapotának megtekintéséhez válassza a **figyelő** elemet a Azure Portal bal oldali paneljén. Alatt a **Insights** szakaszban jelölje be **tárolók**. 

![Az Azure Monitor több fürt irányítópultja – példa](./media/container-insights-analyze/azmon-containers-multiview.png)

A **figyelt fürtök** lapon megismerheti a következőket:

- Hány fürt kritikus vagy nem kifogástalan állapotban van, illetve hogy hányan vannak kifogástalan vagy nem jelentett jelentések (más néven ismeretlen állapot).
- Függetlenül attól, hogy az összes [Azure Kubernetes Engine (ak-Engine)](https://github.com/Azure/aks-engine) üzemelő példány kifogástalan állapotú-e.
- A fürtben lévő csomópontok és a felhasználói és a rendszerbeli hüvelyek száma.
- Mekkora lemezterület áll rendelkezésre, és hogy van-e kapacitási probléma.

Tartalmazza a rendszerállapot-állapotok az alábbiak: 

* **Kifogástalan**: A virtuális géphez nem észlelhető probléma, és szükség szerint működik. 
* **Kritikus**: A rendszer egy vagy több kritikus problémát észlelt, amelyeket a várt módon kell kezelni a normál működési állapot visszaállításához.
* **Figyelmeztetés**: A rendszer egy vagy több olyan problémát észlelt, amelyet meg kell oldani, vagy ha az állapot kritikus fontosságú lehet.
* **Ismeretlen**: Ha a szolgáltatás nem tudott kapcsolódni a csomóponthoz vagy a hüvelyhez, az állapot ismeretlen állapotra vált.
* **Nem található**: A munkaterületet, az erőforráscsoportot vagy az előfizetést, amely a megoldás munkaterületét tartalmazza, törölve lett.
* Nem **engedélyezett**: A felhasználó nem rendelkezik a munkaterületen lévő információk olvasásához szükséges engedélyekkel.
* **Hiba**: Hiba történt az adatok munkaterületről való beolvasására tett kísérlet során.
* **Helytelenül van konfigurálva**: A tárolók Azure Monitor nem megfelelően lettek konfigurálva a megadott munkaterületen.
* **Nincs adat**: Az elmúlt 30 percben nem jelentettek jelentést a munkaterületre.

Az állapot kiszámítja a fürt általános állapotát úgy, hogy a három állapot közül a legrosszabbat egy kivétellel adja ki. Ha a három állam bármelyike ismeretlen, a teljes fürt állapota **ismeretlen**. 

A következő táblázat a megfigyelt fürt állapotának a többfürtes nézeten belüli állapotát szabályozó számítás részletezését tartalmazza.

| |State |Rendelkezésre állás |  
|-------|-------|-----------------|  
|**Felhasználói Pod**| | |  
| |Kifogástalan |100% |  
| |Figyelmeztetés |90 - 99 %-os |  
| |Kritikus |< 90 %-a |  
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |  
|**System Pod**| | |  
| |Kifogástalan |100% |
| |Figyelmeztetés |– |
| |Kritikus |< 100 %-os |
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |
|**Node** | | |
| |Kifogástalan |> 85 % felett |
| |Figyelmeztetés |60 - 84 % |
| |Kritikus |< 60 % |
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |

A fürtök listájából kiválaszthatja a **fürt** lapját a fürt nevének kiválasztásával. Ezután lépjen a **csomópontok** teljesítmény lapra úgy, hogy kijelöli az adott fürt **csomópontok** oszlopában lévő csomópontok összesítését. Vagy a **vezérlők** teljesítmény lapjára kattintva megtekintheti a **felhasználói hüvelyek** vagy a **System hüvelyek** oszlop összesítését.  

## <a name="view-performance-directly-from-an-aks-cluster"></a>AKS-fürt közvetlenül a teljesítmény megtekintése

A tárolók Azure Monitorhoz való hozzáférése közvetlenül egy AK-fürtből érhető el, ha a bal oldali panelen kijelöli az eredményeket. Az AK-fürtre vonatkozó információk négy perspektívába vannak rendezve:

- Fürt
- Csomópontok 
- Vezérlők 
- Containers

Az alapértelmezett lap megnyílik, amikor kiválasztja > az áttekintési**fürtöt**. A négy soros teljesítményű diagram a fürt fő teljesítménymutatóit jeleníti meg. 

![Példa teljesítménydiagramok fürt lapján](./media/container-insights-analyze/containers-cluster-perfview.png)

A teljesítmény-diagramok négy teljesítménymutatót jelenítenek meg:

- **&nbsp;Csomópont CPU-%kihasználtsága**: A teljes fürt CPU-kihasználtságának összesített perspektívája. Az időtartomány eredményeinek szűréséhez válassza az **AVG**, a **min**, az **50**, a **kilencven**, a **95.** vagy a **Max** lehetőséget a diagram feletti percentilis-választóban. A szűrők akár egyénileg, akár kombinálva is használhatók. 
- **&nbsp;Csomópont memóriájának%kihasználtsága**: A teljes fürt memória-kihasználtságának összesített perspektívája. Az időtartomány eredményeinek szűréséhez válassza az **AVG**, a **min**, az **50**, a **kilencven**, a **95.** vagy a **Max** lehetőséget a diagram feletti percentilis-választóban. A szűrők akár egyénileg, akár kombinálva is használhatók. 
- **Csomópontok száma**: Csomópontok száma és állapota a Kubernetes. A megjelenített fürtcsomópontok állapota összesen, kész és nem üzemkész. A diagram felett a választóban egyenként vagy kombinálva is szűrhetők. 
- **Aktív Pod-szám**: A Kubernetes származó Pod-szám és-állapot. A képviselt hüvelyek állapota összesen, függőben, fut, ismeretlen, sikeres vagy sikertelen. A diagram felett a választóban egyenként vagy kombinálva is szűrhetők. 

A bal és jobb nyílbillentyűk használatával a diagramon lévő egyes adatpontokon keresztül válthat. Használja a fel és le nyílbillentyűket a percentilis-vonalakon való váltáshoz. Az egyik diagram jobb felső sarkában válassza a rögzítés ikont, hogy rögzítse a kiválasztott diagramot az utoljára megtekintett Azure-irányítópulton. Az irányítópultról átméretezheti és áthelyezheti a diagramot. Ha kiválasztja a diagramot az irányítópultról, a rendszer átirányítja Azure Monitor a tárolók számára, és betölti a megfelelő hatókört és nézetet.

A tárolók Azure Monitor támogatja a Azure Monitor [metrikák Explorert](../platform/metrics-getting-started.md)is, ahol saját diagramokat hozhat létre, összekapcsolhatja és megvizsgálhatja a trendeket, és rögzítheti az irányítópultokat. A metrikák Explorerrel a mérőszámok [metrika-alapú riasztási szabályának](../platform/alerts-metric.md)alapjaként beállított feltételeket is használhatja. 

## <a name="view-container-metrics-in-metrics-explorer"></a>Tároló metrikáinak megtekintése a metrikák Explorerben

A metrikák Explorerben megtekintheti az összesített csomópont-és Pod-kihasználtsági metrikákat Azure Monitor a tárolók számára. A következő táblázat összefoglalja a részleteket, amelyekkel megismerheti, Hogyan jeleníthető meg a metrikai diagramok a tároló metrikáinak megjelenítéséhez.

|Névtér | Metrika |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| bepillantást nyerhet. tároló/hüvely | |
| | PodCount |

A mérőszámokat megtekintheti dimenzió alapján, és megjelenítheti, hogy a különböző szegmensek hogyan hasonlítanak egymáshoz. [](../platform/metrics-charts.md#apply-splitting-to-a-chart) Csomópont esetén a diagramot a *gazdagép* dimenziója alapján is szegmentálhatja. Egy Pod-ból a következő méretek alapján szegmentálhatja azt:

* Vezérlő
* Kubernetes-névtér
* Csomópont
* Fázis

## <a name="analyze-nodes-controllers-and-container-health"></a>Csomópontok, vezérlők és tároló állapotának elemzése

Amikor átvált a **csomópontokra**, a **vezérlőkre**és a **tárolók** lapra, a rendszer automatikusan megjeleníti a tulajdonságlap jobb oldalán lévő tulajdonságok ablaktáblát. Megjeleníti a kiválasztott elem tulajdonságait, beleértve a Kubernetes objektumok rendszerezéséhez definiált címkéket is. Linux-csomópont kiválasztásakor a **helyi lemez kapacitása** szakasz a rendelkezésre álló lemezterületet és a csomóponton bemutatott egyes lemezek százalékos arányát is megjeleníti. A panel megjelenítéséhez vagy elrejtéséhez kattintson a panelen található **hivatkozásra.>>**

![Példa Kubernetes perspektívák tulajdonságai ablaktáblák](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Kibontja a hierarchia az objektumok, ahogy a Tulajdonságok panelen frissítések alapján a kiválasztott objektum. A panelen megtekintheti az előre definiált Kubernetes eseményeket az ablaktábla tetején található **Kubernetes megtekintése** hivatkozásra kattintva. A Kubernetes-naplózási adatok megtekintésével kapcsolatos további információkért lásd: [keresési naplók az adatok elemzéséhez](container-insights-log-search.md). A fürt erőforrásainak áttekintése közben valós időben láthatja a tároló naplóit és eseményeit. További információ erről a szolgáltatásról, valamint a hozzáférés biztosításához és vezérléséhez szükséges konfigurációról: a [naplók valós idejű megtekintése a Azure monitor for containers](container-insights-live-logs.md)használatával. 

A lap tetején a **+ szűrő hozzáadása** lehetőséggel szűrheti a nézet eredményeit a **szolgáltatás**, a **csomópont**, a **névtér**vagy a **csomópont-készlet**alapján. Miután kiválasztotta a szűrő hatókörét, válassza ki a **kívánt érték (ek)** mezőben megjelenő értékek egyikét. A szűrő konfigurálása után a rendszer globálisan alkalmazza az AK-fürt bármely perspektívájának megtekintésekor. A képlet csak az egyenlőségjel támogatja. A találatok további szűkítéséhez az eredményeket a legelső felül további szűrőket adhat hozzá. Ha például egy szűrőt ad meg a **csomópont**alapján, csak a második szűrőhöz tartozó **szolgáltatás** vagy **névtér** lehetőséget választhatja.

![Példa eredmények szűkítéséhez a szűrő használatával](./media/container-insights-analyze/add-filter-option-01.png)

Egy szűrő egy lapon való megadása továbbra is alkalmazható, amikor kiválaszt egy másikat. A megadott szűrő melletti **x** szimbólum kiválasztása után törlődik. 

Váltson a **csomópontok** lapra, és a sor hierarchiája a Kubernetes objektum modelljét követi, amely a fürt egy csomópontjához indul. Bontsa ki a csomópontot egy vagy több, a csomóponton futó hüvely megtekintéséhez. Ha egynél több tároló van csoportosítva egy Pod-ba, akkor a hierarchia utolsó soraként jelennek meg. Azt is megtekintheti, hogy a gazdagépen hány nem a hüvelyhez kapcsolódó számítási feladat fusson, ha a gazdagép processzor-vagy memória-nyomást tartalmaz.

![Példa a Kubernetes csomópont-hierarchiára a teljesítmény nézetben](./media/container-insights-analyze/containers-nodes-view.png)

A Windows Server 2019 operációs rendszert futtató Windows Server-tárolók a listában szereplő összes Linux-alapú csomópont után jelennek meg. Egy Windows Server-csomópont kibontásakor megtekintheti a csomóponton futó egy vagy több hüvelyt és tárolót. Egy csomópont kijelölése után a Tulajdonságok ablaktábla a verziószámot jeleníti meg. Az ügynök adatai ki vannak zárva, mert a Windows Server-csomópontok nem rendelkeznek ügynökkel. 

![Példa a csomópont-hierarchiára a felsorolt Windows Server-csomópontokkal](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container Instances a Linux operációs rendszert futtató virtuális csomópontok a listában az utolsó AK-fürt csomópontja után jelennek meg. Container Instances virtuális csomópont kibontásakor megtekintheti a csomóponton futó egy vagy több Container Instances hüvelyt és tárolót. A metrikák nem gyűjtése és jelentése a csomópontok számára, csak hüvelyek esetében.

![A Container Instances felsorolt példahierarchia csomópont](./media/container-insights-analyze/nodes-view-aci.png)

Egy kibontott csomópontból lehatolhat a csomóponton futó Pod vagy tárolóból a vezérlőre, így megtekintheti a vezérlőhöz tartozó teljesítményadatokat. Válassza ki az értéket az adott csomópont **vezérlő** oszlopában.
 
![Példa a csomópontról a teljesítmény nézet vezérlőre való részletezésére](./media/container-insights-analyze/drill-down-node-controller.png)

Válassza az oldal tetején található vezérlők vagy tárolók lehetőséget az objektumok állapotának és erőforrás-kihasználtságának áttekintéséhez. A memóriahasználat áttekintéséhez a **metrika** legördülő listában válassza ki a **memória RSS** vagy a **memória**munkakészlete elemet. **Memória RSS** csak Kubernetes 1.8-as és újabb verziók esetében támogatott. Ellenkező esetben, tekintse meg az értékeket **Min&nbsp; %**  , *NaN&nbsp;%* , azaz egy nem definiált képviselő numerikus típus értéke vagy ábrázolható érték.

A **memória** -munkakészletben a rezidens memória és a virtuális memória (gyorsítótár) is látható, és az alkalmazás teljes egészében a használatos. A **memória RSS** csak a fő memóriát jeleníti meg, amely a rezidens memória. Ez a metrika a rendelkezésre álló memória tényleges kapacitását mutatja.

![Tároló csomópontok teljesítmény nézet](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Alapértelmezés szerint a teljesítményadatok az elmúlt hat órában alapulnak, de a bal felső sarokban található **TimeRange** lehetőség használatával módosíthatja az ablakot. Az eredményeket az időtartományon belül is szűrheti, ha a percentilis választóban a **min**, az **AVG**, az **50**, a **kilencven**, a **95.** és a **Max** elemet választja. 

![Az adatok szűrésének. percentilis kiválasztása](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Ha a **trend** oszlop alatt lévő oszlopdiagram fölé viszi a mutatót, akkor az egyes sávokban a CPU vagy a memóriahasználat érték látható, attól függően, hogy melyik metrikát választotta ki, egy 15 perces mintavételi időszakon belül. Miután kiválasztotta a trend diagramot a billentyűzeten, használja az ALT + Page Up vagy az ALT + Page Down billentyűt az egyes sávokon való váltáshoz. Ugyanazokat az adatokat fogja megkapni, mint a sáv fölé.

![Példa a trend oszlopdiagram fölé](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

A következő példában a lista első csomópontjának *AK-nodepool1 –* értéke a **tárolók** esetében 9. Ez az érték a telepített tárolók teljes számának összesítése.

![Tárolók összesítése – példa egy csomópontra](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Ezek az információk segítenek gyorsan megállapítani, hogy megfelelő-e a tárolók közötti egyensúly a fürt csomópontjai között. 

A csomópontok lap megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A gazdagép neve. |
| status | A csomópont állapota Kubernetes nézete. |
| Min&nbsp;.%,&nbsp;AVG%,&nbsp;50%,&nbsp;90%,&nbsp;95.%, Max&nbsp;%  | A csomópont átlagos százalékos PERCENTILIS alapján a kijelölt időszakra. |
| Min, AVG, 50, 90, 95., Max | A csomópontok átlagos tényleges értéke a percentilis alapján a megadott időtartam alatt. Az átlagos értéket a rendszer a csomópont processzor-/memória-korlátja alapján méri. A hüvelyek és a tárolók esetében ez a gazdagép által jelentett átlagos érték. |
| Containers | A tárolók száma. |
| Hasznos üzemidő | Mivel a csomópont elindult, és újra lett indítva a idejét jelzi. |
| Vezérlő | Csak a tárolók és a podokat. Azt mutatja, hogy melyik vezérlő található a ben. Nem minden podok egy vezérlőt, vannak, ezért néhány megjelenítheti **N/A**. | 
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | Oszlopdiagram trend átlagos PERCENTILIS mérőszám százalékos aránya a vezérlő jelöli. |

Válassza ki a választó **tartományvezérlők**.

![Vezérlők nézet kiválasztása](./media/container-insights-analyze/containers-controllers-tab.png)

Itt megtekintheti a vezérlők teljesítményének állapotát, és Container Instances virtuális csomópont-vezérlőket vagy virtuális csomópontok hüvelyeit, amelyek nem csatlakoznak a vezérlőhöz.

![\<Név > vezérlők teljesítmény nézete](./media/container-insights-analyze/containers-controllers-view.png)

A sor hierarchiája egy vezérlővel kezdődik. A vezérlő kibontásakor egy vagy több hüvelyt fog megtekinteni. Bontsa ki a hüvelyt, az utolsó sorban pedig a pod-ba csoportosított tároló látható. Egy kibontott vezérlőből megtekintheti a csomóponton futó csomópontot, és megtekintheti az adott csomópontra szűrt teljesítményadatokat. A lista utolsó részében Container Instances hüvelyek nem csatlakoznak vezérlőhöz.

![A Container Instances podok felsorolt példahierarchia vezérlők](./media/container-insights-analyze/controllers-view-aci.png)

Válassza ki az értéket az adott vezérlő **csomópont** oszlopában.

![Példa a csomópontról a teljesítmény nézet vezérlőre való részletezésére](./media/container-insights-analyze/drill-down-controller-node.png)

A vezérlők megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve.|
| State | A tárolók összesítési állapota, miután befejezte az állapotot, például *az OK*, a leállítva, a *sikertelen*, a leállított vagy a *szüneteltetve*állapotot. Ha a tároló fut, de az állapot nem volt megfelelően megjelenítve, vagy nem az ügynök vette át, és 30 percnél nem válaszolt, az állapot *ismeretlen*. Az állapotjelző ikon további részleteit a következő táblázat tartalmazza.|
| Min&nbsp;.%,&nbsp;AVG%,&nbsp;50%,&nbsp;90%,&nbsp;95.%, Max&nbsp;%| Minden entitás, a kiválasztott metrika és PERCENTILIS hányada összesítő átlaga. |
| Min, AVG, 50, 90, 95., Max  | A kiválasztott PERCENTILIS tárolója átlagos CPU millicore vagy memóriát teljesítményének összesítése. Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Containers | A vezérlő vagy a pod tárolók száma összesen. |
| Újraindul | A tárolók újraindítás száma összegzése. |
| Hasznos üzemidő | Egy tároló indítása óta idejét jelzi. |
| Csomópont | Csak a tárolók és a podokat. Azt mutatja, hogy melyik vezérlő található a ben. | 
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | Oszlopdiagram trend a átlagos PERCENTILIS mérőszám, a vezérlő jelöli. |

Az állapot mezőben lévő ikonok jelzik a tárolók online állapotát.
 
| Ikon | status | 
|--------|-------------|
| ![Készen áll, futó állapot ikon](./media/container-insights-analyze/containers-ready-icon.png) | Fut (kész)|
| ![Várakozó vagy szüneteltetett állapot ikonja](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy fel van függesztve|
| ![Utolsó jelentett futó állapotikon](./media/container-insights-analyze/containers-grey-icon.png) | Legutóbbi jelentett Futtatás, de 30 percnél hosszabb ideig nem válaszolt|
| ![A sikeres állapot ikon](./media/container-insights-analyze/containers-green-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|

Az állapotjelző ikon alapján a pod biztosít számát jeleníti meg. A legrosszabb kétállapotú jeleníti meg, és amikor a kurzort az állapot, a tárolóban megjeleníti az összes podok egy összesítő állapotát. Ha nincs kész állapotú, az állapot értékét jeleníti meg **(0)** .

Válassza ki a választó **tárolók**.

![Tárolók nézet kiválasztása](./media/container-insights-analyze/containers-containers-tab.png)

Itt megtekintheti az Azure-beli Kubernetes és az Azure Container Instances a tárolók teljesítménybeli állapotát. 

![\<Név > tárolók teljesítmény nézete](./media/container-insights-analyze/containers-containers-view.png)

Egy tárolóban, a részletes elemzését is egy pod vagy egy csomópontot, szűri az adott objektum teljesítményadatainak megjelenítéséhez. Válassza ki az értéket az adott tárolóhoz tartozó **Pod** vagy **Node** oszlopban.

![Példa a csomópontok és a tárolók közötti részletezésre a teljesítmény nézetben](./media/container-insights-analyze/drill-down-controller-node.png)

A tárolók megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve.|
| status | A tárolók, ha van ilyen állapotát. További részletek a állapot ikon a következő táblázatban találhatók.|
| Min&nbsp;.%,&nbsp;AVG%,&nbsp;50%,&nbsp;90%,&nbsp;95.%, Max&nbsp;% | Az átlagos százalékos aránya a kiválasztott metrika és PERCENTILIS minden entitás összegzése. |
| Min, AVG, 50, 90, 95., Max | Az átlagos CPU millicore vagy a memória teljesítményét a kiválasztott PERCENTILIS tárolója összegzése. Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Pod | A pod tartalmazó tároló.| 
| Csomópont |  Csomópont, amelyben a tároló található. | 
| Újraindul | Egy tároló indítása óta idejét jelzi. |
| Hasznos üzemidő | Mivel egy tároló volt elindítva vagy újraindítva idejét jelzi. |
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | Oszlopdiagram trend átlagos PERCENTILIS mérőszám százalékos aránya a tárolót képviseli. |

Az állapot mezőben látható ikonok a hüvelyek online állapotát jelzik, az alábbi táblázatban leírtak szerint.
 
| Ikon | status |  
|--------|-------------|  
| ![Készen áll, futó állapot ikon](./media/container-insights-analyze/containers-ready-icon.png) | Fut (kész)|  
| ![Várakozó vagy szüneteltetett állapot ikonja](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy fel van függesztve|  
| ![Utolsó jelentett futó állapotikon](./media/container-insights-analyze/containers-grey-icon.png) | Utolsó jelentett fut, de a 30 percnél hosszabb ideig nem válaszolt|  
| ![Elbocsátott állapotikon](./media/container-insights-analyze/containers-terminated-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|  
| ![Sikertelen állapotikon](./media/container-insights-analyze/containers-failed-icon.png) | Hibás állapotban |  

## <a name="workbooks"></a>Munkafüzetek

A munkafüzetek szövegeket, [naplókat](../log-query/query-language.md), [metrikákat](../platform/data-platform-metrics.md)és paramétereket egyesítenek gazdag interaktív jelentésekben. A munkafüzetek szerkeszthető más csapattagok számára, akik ugyanahhoz az Azure-erőforrásokhoz férnek hozzá.

A tárolók Azure Monitor négy munkafüzetet tartalmaznak az első lépések elkezdéséhez:

- **Lemez kapacitása**: Interaktív lemezhasználat-diagramokat jelenít meg a tárolóban a csomóponton a következő perspektívák által bemutatott lemezekhez:

    - Az összes lemez százalékos kihasználtsága.
    - Szabad lemezterület az összes lemez számára.
    - Egy rács, amely megjeleníti az egyes csomópontok lemezeit, a felhasznált terület százalékos arányát, a felhasznált terület százalékos arányát, a szabad lemezterületet (GiB) és a szabad lemezterület (GiB) trendjét. Ha a táblázatban egy sor van kiválasztva, a rendszer a felhasznált terület és a szabad lemezterület (GiB) százalékos arányát jeleníti meg a sor alatt. 

- **Lemez i/o**: Interaktív lemez-kihasználtsági diagramokat jelenít meg a tárolóban a csomóponton a következő perspektívák által bemutatott lemezekhez:

    - Lemez I/O összegzése az összes lemezről olvasási sebesség (bájt/s), bájt/mp írás, olvasás és írás bájt/mp tendenciák.
    - Nyolc teljesítményű diagramon a fő teljesítménymutatók láthatók a lemez I/O-szűk keresztmetszetek mérése és azonosítása érdekében.

- **Kubelet**: A két rácsot tartalmaz, amelyek a fő csomópont működési statisztikáit mutatják:

    - A Node Grid áttekintés a teljes művelet, a teljes hibák és a sikeres műveletek összesítésével összegzi az egyes csomópontok százalékát és trendjét.
    - Az áttekintés a műveleti típus szerint összefoglalja a teljes művelet, az összes hiba és a sikeres műveletek százalékos és trend szerinti műveleteit.

- **Hálózat**: Interaktív hálózati kihasználtsági diagramokat jelenít meg az egyes csomópontok hálózati adapterei számára, a rács pedig a fő teljesítménymutatókat mutatja be a hálózati adapterek teljesítményének méréséhez.

Ezeket a munkafüzeteket úgy érheti el, ha kiválasztja a **munkafüzetek megtekintése** legördülő listát.

![Munkafüzetek megtekintése legördülő lista](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>További lépések

- Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](container-insights-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások
- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásához, megjelenítéséhez vagy elemzéséhez.
