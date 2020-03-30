---
title: Kubernetes figyelés az Azure Monitor tárolókhoz | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan tekintheti meg és elemezheti a Kubernetes-fürt teljesítményét az Azure Monitor tárolókhoz.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 227fe70512536790d179797394b6fba22e7eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298375"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>A Kubernetes-fürt teljesítményének figyelése az Azure Monitor tárolókhoz

Az Azure Monitor tárolók, használhatja a teljesítménydiagramok és az állapot az Azure Kubernetes-szolgáltatás (AKS), az Azure Stack vagy más környezetkét szempontból üzemeltetett Kubernetes-fürtök munkaterhelésének figyeléséhez. Közvetlenül a fürtből figyelheti, vagy megtekintheti az összes fürtelőfizetésben az Azure Monitorból. Az Azure Container Instances megtekintése is lehetséges, ha egy adott AKS-fürt figyelése.

Ez a cikk segít megérteni a két perspektívát, és azt, hogy az Azure Monitor hogyan segít az észlelt problémák gyors felmérésében, kivizsgálásában és megoldásában.

Az Azure Monitor tárolókhoz való engedélyezéséről az [Onboard Azure Monitor tárolókhoz](container-insights-onboard.md)című témakörben talál.

Az Azure Monitor egy többfürtös nézetet biztosít, amely az előfizetésekben lévő erőforráscsoportok között üzembe helyezett összes felügyelt Kubernetes-fürt állapotát mutatja. Azt mutatja, hogy a megoldások nem figyelik az összes környezetben felderített fürtöket. Azonnal megismerheti a fürt állapotát, és innen leáshat a csomópont és a vezérlő teljesítménylapjára, vagy navigálhat a fürt teljesítménydiagramjainak megtekintéséhez. A felderített és nem figyeltként azonosított AKS-fürtök esetében bármikor engedélyezheti a figyelésüket. 

A Windows Server-fürt és az Azure Monitor tárolók figyelésének főbb különbségeit a Linux-fürthöz képest [itt](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) az áttekintő cikk ismerteti.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

## <a name="multi-cluster-view-from-azure-monitor"></a>Többfürtös nézet az Azure Monitorból

Az összes üzembe helyezett Kubernetes-fürt állapotának megtekintéséhez válassza a **Figyelő** lehetőséget az Azure Portal bal oldali ablaktáblájában. Az **Insights (Insights)** csoportban válassza a **Tárolók lehetőséget.** 

![Példa az Azure Monitor többfürtöző irányítópultjára](./media/container-insights-analyze/azmon-containers-multiview.png)

A rácsban megjelenő eredmények hatóköre a következő fürtök megjelenítésére szolgál:

* **Azure** – Az Azure Kubernetes szolgáltatásban üzemeltetett AKS- és AKS-Engine-fürtök
* **Azure Stack (előzetes verzió)** – Az Azure Stacken tárolt AKS-Engine-fürtök
* **Nem Azure (előzetes verzió)** – A helyszínen üzemeltetett Kubernetes-fürtök
* **Minden** – Az Azure-ban, az Azure Stackben és a helyszíni környezetekben üzemeltetett összes Kubernetes-fürt megtekintése, amelyek az Azure Monitortárolókra vannak berendezve

Ha egy adott környezetből szeretne fürtöket megtekinteni, válassza ki azt a lap bal felső sarkában található **Környezetek** pirulából.

![Környezetvédelmi tabletta választó példa](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

A **Figyelt fürtök** lapon a következőket ismerheti meg:

- Hány fürtök kritikus vagy nem kifogástalan állapotú állapotban, szemben, hogy hány kifogástalan vagy nem jelentési (a továbbiakban ismeretlen állapot).
- Függetlenül attól, hogy az összes [Azure Kubernetes Engine (AKS-motor)](https://github.com/Azure/aks-engine) központi telepítések kifogástalan.
- Hány csomópont, valamint a felhasználó és a rendszer podok fürtözött fürtenként.
- A rendelkezésre álló lemezterület, és ha kapacitásprobléma merül fel.

Az állapotok a következők: 

* **Kifogástalan:** A virtuális gép nem észlelt problémákat, és szükség szerint működik. 
* **Kritikus**: Egy vagy több kritikus problémát észlel, amelyet meg kell oldani a normál működési állapot várható visszaállításához.
* **Figyelmeztetés:** A rendszer egy vagy több olyan problémát észlel, amelyet meg kell oldani, vagy az állapot kritikussá válhat.
* **Ismeretlen**: Ha a szolgáltatás nem tudott kapcsolatot létesíteni a csomóval vagy a poddal, az állapot ismeretlen állapotra változik.
* **Nem található:** a munkaterület, az erőforráscsoport vagy a megoldás munkaterületét tartalmazó előfizetés törölve lett.
* **Jogosulatlan**: A felhasználó nem rendelkezik a munkaterületi adatok olvasásához szükséges engedélyekkel.
* **Hiba:** Hiba történt az adatok munkaterületről történő olvasása közben.
* **Helytelenül konfigurált:** Az Azure-figyelő tárolók nem megfelelően konfigurálva a megadott munkaterületen.
* **Nincs adat**: Az adatok nem jelentették a munkaterületnek az elmúlt 30 percben.

Az állapot kiszámítja a fürt általános állapotát a három állapot közül a *legrosszabbként,* egy kivétellel. Ha a három állapot bármelyike ismeretlen, a teljes fürtállapot **ismeretlen**. 

Az alábbi táblázat a többfürtös nézetben figyelt fürt állapotát szabályozó számítás bontását tartalmazza.

| |status |Rendelkezésre állás |  
|-------|-------|-----------------|  
|**Felhasználói pod**| | |  
| |Kifogástalan |100% |  
| |Figyelmeztetés |90 - 99% |  
| |Kritikus |<90% |  
| |Ismeretlen |Ha nem jelentik az utolsó 30 percben |  
|**Rendszerpod**| | |  
| |Kifogástalan |100% |
| |Figyelmeztetés |N/A |
| |Kritikus |<100% |
| |Ismeretlen |Ha nem jelentik az utolsó 30 percben |
|**Node** | | |
| |Kifogástalan |>85% |
| |Figyelmeztetés |60 - 84% |
| |Kritikus |<60% |
| |Ismeretlen |Ha nem jelentik az utolsó 30 percben |

A fürtök listájából a fürt nevének kiválasztásával részletezhet a **Fürt** lapig. Ezután nyissa meg a **Csomópontok** teljesítménylapját, ha kiválasztja az adott fürt **csomópontok** oszlopában lévő csomópontok összesítését. Vagy a **Vezérlők** teljesítménylapjára is leáshat a **Felhasználói podok** vagy a **Rendszerpodok** oszlop összesítésének kiválasztásával.

## <a name="view-performance-directly-from-a-cluster"></a>Teljesítmény megtekintése közvetlenül a fürtből

Az Azure Monitor hoz való hozzáférés a tárolók közvetlenül érhető el egy AKS-fürt kiválasztásával **Insights** > **Cluster** a bal oldali ablaktáblából, vagy ha kiválasztott egy fürtöt a többfürtös nézetben. A fürttel kapcsolatos információk négy szempontból vannak elrendezve:

- Fürt
- Csomópontok 
- Vezérlők 
- Containers

>[!NOTE]
>A jelen cikk további részében ismertetett élmény is alkalmazható az Azure Stackben vagy más környezetben üzemeltetett Kubernetes-fürtök teljesítményének és állapotának megtekintésére, ha a többfürtös nézetből van kiválasztva. 

Az alapértelmezett lap megnyílik, és négy vonalteljesítmény-diagramot jelenít meg, amelyek a fürt legfontosabb teljesítménymutatóit mutatják. 

![Példa teljesítménydiagramokra a Fürt lapon](./media/container-insights-analyze/containers-cluster-perfview.png)

A teljesítménydiagramok négy teljesítménymutatót jelenítnek meg:

- **Csomópont cpu-kihasználtsága&nbsp;**: A teljes fürt CPU-kihasználtságának összesített perspektívája. Az időtartomány eredményeinek szűréséhez válassza az **Átlag**, **Min**, **50th**, **90th**, **95th**vagy **Max** lehetőséget a diagram feletti percentilis választóban. A szűrők külön-külön vagy kombinálva is használhatók. 
- **Csomópontmemória-kihasználtság:&nbsp;** A teljes fürt memóriakihasználtságának összesített perspektívája. Az időtartomány eredményeinek szűréséhez válassza az **Átlag**, **Min**, **50th**, **90th**, **95th**vagy **Max** lehetőséget a diagram feletti percentilis választóban. A szűrők külön-külön vagy kombinálva is használhatók. 
- **Csomópontszám**: A csomópontszáma és állapota a Kubernetes.Node count : A csomópont száma és állapota a Kubernetes. A képviselt fürtcsomópontok állapotai: Összes, Kész és Nem áll készen. Ezek külön-külön vagy kombinálva szűrhetők a diagram feletti választóban. 
- **Aktív podok száma:** A pod száma és állapota Kubernetes. A képviselt podok állapotai: Összes, Függőben, Futó, Ismeretlen, Sikeres vagy Sikertelen. Ezek külön-külön vagy kombinálva szűrhetők a diagram feletti választóban. 

A Bal és a Jobb nyílbillentyűkkel lépkedhet a diagram egyes adatpontjain. A Fel és a Le nyílbillentyűkkel lépkedjen a percentilis vonalak között. Jelölje ki a pin ikont bármelyik diagram jobb felső sarkában, és rögzítse a kijelölt diagramot az utoljára megtekintett Azure-irányítópultra. Az irányítópultról átméretezheti és áthelyezheti a diagramot. A diagram kiválasztása az irányítópultról átirányítja az Azure Monitortárolók és betölti a megfelelő hatókört és nézetet.

Az Azure Monitor tárolókhoz támogatja az Azure Monitor [metrikakezelőjét](../platform/metrics-getting-started.md)is, ahol létrehozhatja saját nyomtatási diagramjait, korrelálhat és megvizsgálhatja a trendeket, és rögzítheti az irányítópultokat. A metrikakezelőben a beállított feltételek segítségével a metrikák at egy [metrikaalapú riasztási szabály](../platform/alerts-metric.md)alapjaként jelenítheti meg. 

## <a name="view-container-metrics-in-metrics-explorer"></a>Tárolómetrikák megtekintése a metrikák kezelőjében

A metrikakezelőben megtekintheti az Azure Monitor összesített csomópont- és pod-kihasználtsági metrikákat a tárolókhoz. Az alábbi táblázat összefoglalja a részleteket, hogy könnyebben megértheti, hogyan használhatja a metrikadiagramokat a tárolómetrikák megjelenítéséhez.

|Névtér | Metrika | Leírás | 
|----------|--------|-------------|
| insights.container/csomópontok | |
| | cpuUsageMillicores | A processzorhasználat összesített mérése a fürtön. Ez egy 1000 egységre osztott CPU mag (milli = 1000). A magok használatának meghatározására szolgál egy olyan tárolóban, ahol sok alkalmazás egy magot használhat.| 
| | cpuUsageSzázalék | A fürtben mért összesített átlagos CPU-kihasználtság százalékban.|
| | memoryRssBytes | A bájtban használt RSS-memória.| 
| | memoryRssPercentage | A tároló RSS-memóriája százalékban van használva.|
| | memóriaWorkingSetBytes | A tároló munkakészlet memóriája.| 
| | memoryWorkingSetSzázalék | A tárolómunkakészlet memóriája százalékban van tárolva. | 
| | csomópontok száma | Csomópontszám a Kubernetes-től.|
| insights.container/podok | |
| | PodCount (PodCount) | Egy pod szám Kubernetes-től.|

A metrikát [feloszthatja,](../platform/metrics-charts.md#apply-splitting-to-a-chart) hogy dimenzió szerint tekintse meg, és vizualizálja, hogy a különböző szegmensei hogyan viszonyulnak egymáshoz. Egy csomópont esetében szegmentálhatja a diagramot az *állomásdimenzió* szerint. Egy podból a következő dimenziók szerint szegmentálhatja:

* Vezérlő
* Kubernetes névtér
* Csomópont
* Fázis

## <a name="analyze-nodes-controllers-and-container-health"></a>Csomópontok, vezérlők és tárolók állapotának elemzése

Amikor a **Csomópontok**, **Vezérlők**és **Tárolók** lapokra vált, a tulajdonságablak automatikusan megjelenik a lap jobb oldalán. A kijelölt elem tulajdonságait jeleníti meg, beleértve a Kubernetes-objektumok rendszerezéséhez megadott címkéket. Ha egy Linux-csomópont van kiválasztva, a **Helyi lemezkapacitás** szakaszban is megjelenik a rendelkezésre álló lemezterület és a csomópontnak bemutatott egyes lemezek százalékos aránya. Jelölje **>>** ki a csatolást az ablaktáblán az ablaktábla megtekintéséhez vagy elrejtéséhez.

A hierarchia objektumainak kibontásakor a tulajdonságok ablaktábla a kijelölt objektum alapján frissül. Az ablaktáblán is megtekintheti Kubernetes tároló naplók (stdout/stderror), események és pod metrikák kiválasztásával az **élő adatok megtekintése (előzetes verzió)** hivatkozás az ablaktábla tetején. Az adatok megtekintéséhez szükséges hozzáférés biztosításához és szabályozásához szükséges konfigurációról [az Élő adatok beállítása (előzetes verzió) című](container-insights-livedata-setup.md)témakörben talál további információt. A fürterőforrások áttekintése közben ezeket az adatokat valós időben láthatja a tárolóból. A funkcióról további információt a [Kubernetes-naplók, események és pod-metrikák valós idejű megtekintése](container-insights-livedata-overview.md)című témakörben talál. Ha előre definiált naplókeresések alapján szeretné megtekinteni a kubernetes naplóadatait a munkaterületen, válassza a **Tárolónaplók megtekintése lehetőséget** a **Nézet elemzésközben** legördülő listából. A témakörről további információt az [adatok elemzését szolgáló naplók keresése](container-insights-log-search.md#search-logs-to-analyze-data)című témakörben talál.

A lap tetején található **+ Szűrő hozzáadása** beállítással szűrheti a nézet eredményeit **szolgáltatás,** **csomópont**, **névtér**vagy **csomópontkészlet**szerint. A szűrőhatókör kiválasztása után jelöljön ki egyet a **Select value(ok)** mezőben látható értékek közül. A szűrő konfigurálása után globálisan alkalmazza azt az AKS-fürt bármely perspektívájának megtekintésekor. A képlet csak az egyenlőségjelet támogatja. Az eredmények további szűrőkhozzáadásával az első helyett további szűrőket adhat hozzá. Ha például **csomópont**szerint ad meg egy szűrőt, akkor csak a **Második szűrőhöz választhatja** a Szolgáltatás vagy a **Névtér** lehetőséget.

A szűrő egyik lapon történő megadása továbbra is érvényben van, amikor másikat jelöl ki. A program törlődik, miután kiválasztotta az **x** szimbólumot a megadott szűrő mellett. 

Váltson a **Csomópontok** lapra, és a sorhierarchia a Kubernetes objektummodellt követi, amely a fürt csomópontjával kezdődik. Bontsa ki a csomópontot egy vagy több, a csomóponton futó pod megtekintéséhez. Ha egynél több tároló van csoportosítva egy podhoz, akkor a hierarchia utolsó soraként jelennek meg. Azt is megtekintheti, hogy hány nem pod kapcsolatos számítási feladatok futnak a gazdagépen, ha a gazdagép processzor vagy memórianyomás.

![Példa a Kubernetes-csomópont hierarchiára a teljesítménynézetben](./media/container-insights-analyze/containers-nodes-view.png)

A Windows Server 2019 operációs rendszert futtató Windows Server-tárolók a listában szereplő összes Linux-alapú csomópont után jelennek meg. Windows Server-csomópont kibontásakor megtekintheti a csomóponton futó egy vagy több podot és tárolót. A csomópont kiválasztása után a tulajdonságok ablaktábláján megjelennek a verzióadatok. Az ügynökadatok ki vannak zárva, mert a Windows Server-csomópontokon nincs telepítve ügynök. 

![Példa csomóponthierarchiára listázott Windows Server-csomópontokkal](./media/container-insights-analyze/nodes-view-windows.png) 

A Linux operációs rendszert futtató Azure Container Instances virtuális csomópontok a lista utolsó AKS-fürtcsomópontja után jelennek meg. A tárolópéldányok virtuális csomópontjának kibontásakor megtekintheti a csomóponton futó tárolópéldányok és tárolók megtekintését. Metrikák nem gyűjtik, és a csomópontok, csak a podok.

![Példa csomóponthierarchiára a felsorolt tárolópéldányokkal](./media/container-insights-analyze/nodes-view-aci.png)

Egy kibővített csomópontról leáshat a csomóponton futó podról vagy tárolóból a vezérlőre az adott vezérlőre szűrt teljesítményadatok megtekintéséhez. Válassza ki az adott csomópont **Vezérlő** oszlopa alatti értéket.
 
![Példa részletezés csomópontról vezérlőre a teljesítmény nézetben](./media/container-insights-analyze/drill-down-node-controller.png)

Válassza ki a vezérlők vagy tárolók a lap tetején, hogy áttekintse az állapotát és az erőforrás-kihasználtság az objektumok. A memóriakihasználtság áttekintéséhez a **Metrika** legördülő listában válassza a **Memória RSS** vagy **a Memória munkakészlet lehetőséget.** **A memória RSS csak** a Kubernetes 1.8-as és újabb verzióinál támogatott. Ellenkező esetben a **Min&nbsp; ** értékeit *&nbsp;NaN-ként*tekintheti meg, amely egy numerikus adattípus-érték, amely egy nem definiált vagy nem ábrázolható értéket jelöl.

![Tárolócsomópontok teljesítménynézete](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**A memóriamunkakészlet** a bebiztosított memória memóriát és virtuális memóriát (gyorsítótárat) is megjeleníti, és az alkalmazás által használt összesen. **Memória RSS** azt mutatja, csak a fő memória (ami nem más, mint a rezidens memória más szóval). Ez a mérőszám a rendelkezésre álló memória tényleges kapacitását mutatja. Mi a különbség a rezidens memória és a virtuális memória között?

- A rezidens memória vagy a fő memória a fürt csomópontjai számára rendelkezésre álló gépmemória tényleges mennyisége.

- A virtuális memória fenntartott merevlemez-terület (gyorsítótár), amelyet az operációs rendszer arra használ, hogy memórianyomás alatt adatokat cseréljen a memóriáról a lemezre, majd szükség esetén visszaolvassa a memóriába.

Alapértelmezés szerint a teljesítményadatok az elmúlt hat órán alapulnak, de az ablakot a bal felső sarokban található **TimeRange** beállítással módosíthatja. Az eredményeket az időtartományon belül is szűrheti, ha a percentilis **95th** **választóban** a **Min**, **Az Átlag, az** **50.,** **a 90.** 

![Százalékos kijelölés adatszűréshez](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Ha az egérmutatót a **Trend** oszlop alatti sávdiagram fölé viszi, minden sáv a PROCESSZOR- vagy memóriahasználatot jeleníti meg, attól függően, hogy melyik metrika van kiválasztva, 15 percen belül. Miután billentyűzeten keresztül kiválaszthata a trenddiagramot, az Alt+Page up vagy az Alt+Page down billentyűvel egyenként lépkedhet az egyes sávok között. Ugyanazokat a részleteket kapod, mint ha a bár fölött lebegnél.

![Trendsávdiagram mutatófölé mutat](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

A következő példában a lista első csomópontja, az *aks-nodepool1-* esetében a **Tárolók** értéke 9. Ez az érték az üzembe helyezett tárolók teljes számának összesítése.

![Tárolók csomópontonkénti összesítésének példája](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Ez az információ segítségével gyorsan azonosíthatja, hogy rendelkezik-e a tárolók megfelelő egyensúlyával a fürt csomópontjai között. 

A **Csomópontok** lap megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Oszlop | Leírás | 
|--------|-------------|
| Név | A gazdagép neve. |
| status | Kubernetes nézet a csomópont állapotáról. |
| Min&nbsp;%,&nbsp;Átlag %,&nbsp;50%,&nbsp;90%,&nbsp;95.&nbsp;%  | A csomópont százalékos aránya a kiválasztott időtartam alatt. |
| Min, Avg, 50., 90., 95., Max. | Átlagos csomópontok tényleges értéke percentilis alapján a kiválasztott időtartam alatt. Az átlagos értéket a csomóponthoz beállított CPU/memória korlátalapján mérik. A podok és tárolók esetében ez a gazdagép által jelentett átlagos érték. |
| Containers | Konténerek száma. |
| Uptime | A csomópont indulása vagy újraindítása óta eltelt időt jelöli. |
| Vezérlő | Csak konténerekhez és hüvelyekhez. Megmutatja, hogy melyik vezérlőben lakik. Nem minden pod van a vezérlőben, így néhányan **n/a-t**jeleníthetnek meg. | 
| Trend&nbsp;Min %,&nbsp;Átlag %,&nbsp;50%,&nbsp;90%,&nbsp;95% %, Max&nbsp;% | Az sávdiagram-trend a vezérlő átlagos percentilis metrikaszázalékát jelöli. |

Az **Egyéb folyamat**nevű csomópont kibővítése után munkaterhelést tapasztalhat. Nem tárolóba helyezett folyamatokat jelöl, amelyek a csomóponton futnak, és a következőket tartalmazza:

* Saját irányítású vagy felügyelt Kubernetes nem konténeres folyamatok

* Tárolófutási idő folyamatok  

* Kubelet  

* A csomóponton futó rendszerfolyamatok

* A csomóponthardveren vagy virtuális gépen futó egyéb, nem Kubernetes-számítási feladatok

Számítása: *Teljes használat a CAdvisor-használatból* - a*konténeres folyamatból.*  

A választóban válassza a **Vezérlők**lehetőséget.

![Vezérlők nézet kiválasztása](./media/container-insights-analyze/containers-controllers-tab.png)

Itt megtekintheti a vezérlők és a tárolópéldányok virtuális csomópont-vezérlők vagy a vezérlőhöz nem csatlakoztatott virtuális csomópont-podok teljesítményállapotát.

![\<Név> vezérlők teljesítménynézete](./media/container-insights-analyze/containers-controllers-view.png)

A sorhierarchia vezérlővel kezdődik. Vezérlő kibontásakor egy vagy több podot tekinthet meg. Bontsa ki a pod, és az utolsó sorban megjeleníti a tároló csoportosítva a pod. Egy kibontott vezérlőből leáshat arra a csomópontra, amelyen fut, hogy megtekinthesse az adott csomópontra szűrt teljesítményadatokat. A vezérlőhöz nem csatlakoztatott tárolópéldányok podjai az utolsóként szerepelnek a listában.

![Példa vezérlők hierarchia a tárolópéldányok podok felsorolt](./media/container-insights-analyze/controllers-view-aci.png)

Válassza ki az adott vezérlő **Csomópont oszlopa** alatti értéket.

![Példa részletezés csomópontról vezérlőre a teljesítmény nézetben](./media/container-insights-analyze/drill-down-controller-node.png)

A vezérlők megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Oszlop | Leírás | 
|--------|-------------|
| Név | A vezérlő neve.|
| status | A tárolók összesítő állapota, miután befejezte a futást az *OK*, *Terminated*, *Failed*, *Stopped*vagy *Paused*állapotú állapottal. Ha a tároló fut, de az állapot vagy nem volt megfelelően megjelenik, vagy az ügynök nem vette fel, és 30 percnél tovább nem válaszolt, az állapot *ismeretlen*. Az állapotikon további részleteit az alábbi táblázat tartalmazza.|
| Min&nbsp;%,&nbsp;Átlag %,&nbsp;50%,&nbsp;90%,&nbsp;95.&nbsp;%| Az egyes entitások átlagos százalékos értékének összesítő átlaga a kiválasztott metrikában és percentilisben. |
| Min, Avg, 50., 90., 95., Max.  | A kijelölt percentilis tároló átlagos CPU-millimagjának vagy memóriateljesítményének összesítése. Az átlagos értéket a podhoz beállított CPU/memória korlátalapján mérik. |
| Containers | A vezérlő vagy a hüvely tárolóinak száma összesen. |
| Újraindul | Az újraindítások számának összesítése a tárolókból. |
| Uptime | A tároló indítása óta eltelt időt jelöli. |
| Csomópont | Csak konténerekhez és hüvelyekhez. Megmutatja, hogy melyik vezérlőben lakik. | 
| Trend&nbsp;Min %,&nbsp;Átlag %,&nbsp;50%,&nbsp;90%,&nbsp;95% %, Max&nbsp;% | Az sávdiagram-trend a vezérlő átlagos percentilis metrikáját jelöli. |

Az állapotmezőben lévő ikonok jelzik a tárolók online állapotát.
 
| Ikon | status | 
|--------|-------------|
| ![Futásra kész állapot ikon](./media/container-insights-analyze/containers-ready-icon.png) | Futás (kész)|
| ![Várakozás vagy szüneteltetett állapot ikon](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy szüneteltetve|
| ![Utoljára jelentett futó állapot ikon](./media/container-insights-analyze/containers-grey-icon.png) | Utolsó jelentett futás, de nem válaszolt több mint 30 perc|
| ![Sikeres állapot ikon](./media/container-insights-analyze/containers-green-icon.png) | Sikeresen leállt, vagy nem sikerült leállítani|

Az állapotikon a pod által biztosított állapotjelző konnitalapul jeleníti meg a számlálót. A legrosszabb két állapotot jeleníti meg, és amikor az állapot fölé viszi az egérmutatót, a tárolóban lévő összes pod összesítő állapotát jeleníti meg. Ha nincs kész állapot, az állapotérték **(0)** jelenik meg.

A választóban válassza a **Tárolók**lehetőséget.

![Tárolók nézet kiválasztása](./media/container-insights-analyze/containers-containers-tab.png)

Itt megtekintheti az Azure Kubernetes és az Azure Container Instances tárolók teljesítményállapotát. 

![\<Név> tárolók teljesítménynézete](./media/container-insights-analyze/containers-containers-view.png)

Egy tárolóból leáshat egy podvagy csomópont az adott objektumra szűrt teljesítményadatok megtekintéséhez. Válassza ki az adott tároló **pod** vagy **node** oszlopa alatti értéket.

![Példa részletezés csomópontról tárolókra a teljesítménynézetben](./media/container-insights-analyze/drill-down-controller-node.png)

A tárolók megtekintésekor megjelenő információkat az alábbi táblázat ismerteti.

| Oszlop | Leírás | 
|--------|-------------|
| Név | A vezérlő neve.|
| status | A konténerek állapota, ha vannak ilyenek. Az állapotikon további részletei a következő táblázatban találhatók.|
| Min&nbsp;%,&nbsp;Átlag %,&nbsp;50%,&nbsp;90%,&nbsp;95.&nbsp;% | A kiválasztott metrika és percentilis entitások átlagos százalékának összesítése. |
| Min, Avg, 50., 90., 95., Max. | A kijelölt percentilis tároló átlagos CPU-millimagjának vagy memóriateljesítményének összesítése. Az átlagos értéket a podhoz beállított CPU/memória korlátalapján mérik. |
| Pod | A tartály, ahol a pod található.| 
| Csomópont |  A tároló helye szerinti csomópont. | 
| Újraindul | A tároló indítása óta eltelt időt jelöli. |
| Uptime | A tároló indítása vagy újraindítása óta eltelt időt jelöli. |
| Trend&nbsp;Min %,&nbsp;Átlag %,&nbsp;50%,&nbsp;90%,&nbsp;95% %, Max&nbsp;% | Az eszköztárdiagram-trend a tároló átlagos percentilis metrikaszázalékát jelöli. |

Az állapotmezőben szereplő ikonok a podok online állapotát jelzik az alábbi táblázatban leírtak szerint.
 
| Ikon | status |  
|--------|-------------|  
| ![Futásra kész állapot ikon](./media/container-insights-analyze/containers-ready-icon.png) | Futás (kész)|  
| ![Várakozás vagy szüneteltetett állapot ikon](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy szüneteltetve|  
| ![Utoljára jelentett futó állapot ikon](./media/container-insights-analyze/containers-grey-icon.png) | Utolsó jelentett futás, de nem válaszolt több mint 30 perc alatt|  
| ![Leállítva állapot ikon](./media/container-insights-analyze/containers-terminated-icon.png) | Sikeresen leállt, vagy nem sikerült leállítani|  
| ![Sikertelen állapot ikon](./media/container-insights-analyze/containers-failed-icon.png) | Sikertelen állapot |  

## <a name="workbooks"></a>Munkafüzetek

A munkafüzetek a szöveget, [a naplólekérdezéseket,](../log-query/query-language.md) [a mutatókat](../platform/data-platform-metrics.md)és a paramétereket gazdag interaktív jelentésekké egyesítik. A munkafüzeteket bármely más csapattag szerkeszti, akik hozzáférnek ugyanazokhoz az Azure-erőforrásokhoz.

Az Azure Monitor tárolók hoz négy munkafüzetet a kezdéshez:

- **Lemezkapacitás**: Interaktív lemezhasználati diagramokat jelenít meg a tárolón belüli csomópontnak a következő szempontok szerint bemutatott lemezekhez:

    - Az összes lemez lemezszázaléka.
    - Szabad lemezterület az összes lemez számára.
    - Az egyes csomópontok lemezét, a felhasznált terület százalékos arányát, a felhasznált terület százalékos arányát, a szabad lemezterületet (GiB) és a szabad lemezterület (GiB) trendjét megjelenítő rács. Ha a táblázatban egy sor van kijelölve, a felhasznált terület és a szabad lemezterület (GiB) százaléka jelenik meg a sor alatt. 

- **Lemez IO**: Interaktív lemezkihasználtsági diagramokat jelenít meg a tárolón belüli csomópontnak a következő szempontok szerint bemutatott lemezekhez:

    - Az I/O-lemez az összes lemezközött olvasási bájt/mp, másodpercenként ibájt, olvasási és írási bájtok/mp függvények szerint összesítve.
    - Nyolc teljesítménydiagram mutatja a fő teljesítménymutatókat a lemez I/O-szűk keresztmetszeteinek mérésére és azonosítására.

- **Kubelet**: Két rácsot tartalmaz, amelyek a legfontosabb csomópont működési statisztikáit mutatják:

    - A csomópontrács áttekintése az összes műveletet, az összes hibát, valamint a sikeres műveleteket százalékkal és trenddel foglalja össze az egyes csomópontokesetében.
    - A művelettípus onkénti áttekintése összefoglalja az egyes műveletekteljes műveletet, az összes hibát, valamint a sikeres műveleteket százalékkal és trenddel.

- **Hálózat**: Interaktív hálózati kihasználtsági diagramokat jelenít meg az egyes csomópontok hálózati adaptereihez, és egy rács mutatja be a fő teljesítménymutatókat a hálózati adapterek teljesítményének méréséhez.

Ezeket a munkafüzeteket úgy érheti el, hogy mindegyiket kijelöli a **Munkafüzetek megtekintése** legördülő listából.

![Munkafüzetek legördülő listájának megtekintése](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>További lépések

- Tekintse [át a Teljesítményriasztások létrehozása az Azure Monitor tárolók,](container-insights-alerts.md) hogyan hozhat létre riasztásokat a magas CPU és a memória kihasználtsága a DevOps vagy a működési folyamatok és eljárások támogatása érdekében.

- Tekintse meg [a naplólekérdezési példákat](container-insights-log-search.md#search-logs-to-analyze-data) az előre definiált lekérdezések és példák megtekintéséhez vagy testreszabásához a fürtök riasztásához, megjelenítéséhez vagy elemzéséhez.

- Tekintse [meg a fürt állapotának figyelése,](container-insights-health.md) hogy megtudjon a Kubernetes-fürt állapotának megtekintéséről.
