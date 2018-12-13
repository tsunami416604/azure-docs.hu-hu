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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: 588dcabb35660c860f3d96dd03c82ed95a1d4d5b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087209"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Az AKS fürtteljesítmény és az Azure Monitor-tárolókhoz ismertetése 
Az Azure monitorral tárolók segítségével a teljesítmény diagramokat és az állapot az Azure Kubernetes Service (AKS)-fürtök két perspektíva adatai, közvetlenül az AKS-fürt vagy az Azure-ból összes AKS-fürt az előfizetéshez, a számítási feladat figyeléséhez Ez a figyelő. Megtekintése az Azure Container Instances (ACI) esetén is lehetséges egy adott AKS-fürt monitorozására.

A cikknek a segítségével megismerheti, hogyan segít gyorsan felmérheti, kivizsgálásán és elhárításán észlelt problémák, és a két perspektíva adatai között a felhasználói élményt.

A tárolók az Azure Monitor engedélyezésével kapcsolatos információkért lásd: [előkészítése az Azure Monitor-tárolókhoz](container-insights-onboard.md).

Az Azure Monitor az előfizetéseken lévő erőforráscsoportok telepített összes figyelt AKS fürt állapotát megjelenítő több fürt nézetet biztosít.  AKS-fürtök észlelt, amely nem a megoldás által figyelt jeleníti meg. Azonnal képes megérteni a fürt állapotát, és innen lefúrhat a csomópont és a tartományvezérlő teljesítmény lapján, vagy keresse meg a fürt teljesítménydiagramok megtekintéséhez.  AKS fürtök felderített és a nem figyelt azonosította engedélyezheti a tetszőleges időpontban, hogy a fürt figyelése.  

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Az Azure Monitor több fürt megtekintése 
Üzembe helyezett összes AKS-fürt állapotának megtekintéséhez válassza **figyelő** a bal oldali ablaktáblán, az Azure Portalon.  Alatt a **Insights** szakaszban jelölje be **tárolók**.  

![Az Azure Monitor több fürt irányítópultja – példa](./media/container-insights-analyze/azmon-containers-multiview-1018.png)

Az a **fürtök figyelt** lapon is tudja ismerje meg a következőket:

1. Hány fürtök kritikus vagy nem megfelelő állapotú, és hány megfelelő vagy nem jelentéskészítési (néven állapota ismeretlen) vannak?
1. Összes saját [Azure Kubernetes-(AKS-motor) motor](https://github.com/Azure/aks-engine) kifogástalan üzemelő példányok?
1. Hány csomópontokat, a felhasználó és a rendszer podok fürtönként vannak telepítve.  

Tartalmazza a rendszerállapot-állapotok az alábbiak: 

* **Kifogástalan állapotú** – nem problémát észlelt a virtuális gép, és szükség szerint működik-e. 
* **Kritikus fontosságú** – egy vagy több kritikus problémák észlelhetők, amely kell oldani annak érdekében, hogy a várt módon állítsa vissza a rendes működési állapot.
* **Figyelmeztetés** – egy vagy több problémák észlelhetők, amelyeket meg kell oldani, vagy az egészségügyi feltétel kritikus válhat.
* **Ismeretlen** – Ha a szolgáltatás nem tudta létesítsen kapcsolatot a csomópont- vagy pod, az állapot módosul, állapota ismeretlen.
* **Nem található** - vagy a munkaterületen, az erőforráscsoportra vagy törölték a megoldás, amely tartalmazza a munkaterület előfizetés.
* **Jogosulatlan** -felhasználó nem rendelkezik az adatok a munkaterület olvasásához szükséges engedélyekkel.
* **Hiba** -hiba történt a munkaterület adatainak olvasására tett kísérlet során.
* **Konfigurált MIS** -tárolókhoz az Azure Monitor nincs megfelelően konfigurálva az adott munkaterületen.
* **Nincs adat** -adatok nem jelentette a munkaterülethez az elmúlt 30 percben.

Állapot alapján számítja ki a teljes fürt állapotát, *legrosszabb,*", egy kivétellel – a három állapotok, ha a három állapotok valamelyikében van *ismeretlen*, megjelenik a teljes fürt állapota **ismeretlen**.  

A következő táblázat nyújt információkat a számítás, a figyelt fürt a fürt több nézet állapotokat szabályozása.

| |status |Rendelkezésre állás |  
|-------|-------|-----------------|  
|**Felhasználói Pod**| | |  
| |Kifogástalan |100% |  
| |Figyelmeztetés |90 - 99 %-os |  
| |Kritikus |< 90 %-a |  
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |  
|**Rendszer-Pod**| | |  
| |Kifogástalan |100% |
| |Figyelmeztetés |– |
| |Kritikus |< 100 %-os |
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |
|**Node** | | |
| |Kifogástalan |> 85 % felett |
| |Figyelmeztetés |60 - 84 % |
| |Kritikus |< 60 % |
| |Ismeretlen |Ha az elmúlt 30 percben nem jelentettek |

Fürtök listájában, akkor nyissa a **fürt** lapra kattintva a fürt nevét, a **csomópontok** teljesítmény lapra kattintva a kumulatív frissítést a csomópontok a **csomópontok** oszlopot adott fürtben, vagy a feltárásához az **tartományvezérlők** teljesítmény lapon kattintson az összegző a **felhasználói podok** vagy **rendszer podok**oszlop.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>AKS-fürt közvetlenül a teljesítmény megtekintése
Az Azure Monitor-tárolókhoz is elérhető közvetlenül egy AKS-fürtöt a kiválasztásával **Insights** a bal oldali panelen. Az AKS-fürt adatainak megtekintése a következő szakaszokba négy szempont:

- Fürt
- Csomópontok 
- Vezérlők  
- Containers

Az alapértelmezett oldalt megnyitni kattintva **Insights** van **fürt**, és a négy sorban teljesítménydiagramok megjelenítése a fürt fő teljesítménymutatói tartalmazza. 

![Példa teljesítménydiagramok fürt lapján](./media/container-insights-analyze/containers-cluster-perfview.png)

A teljesítmény diagramon négy teljesítmény-mérőszámait jeleníti meg:

- **Csomópont CPU-kihasználtság&nbsp;%**: egy összesített szempontjából gyűjtik a CPU-kihasználtság az egész fürt számára. Kijelölésével szűrheti az eredményeket a időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95.** percentilisei-választójában jelenítse a diagram felett vagy külön-külön vagy együtt. 
- **Csomópont memóriahasználat&nbsp;%**: egy összesített szempontjából gyűjtik a memóriahasználat, az egész fürt számára. Kijelölésével szűrheti az eredményeket a időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95.** percentilisei-választójában jelenítse a diagram felett vagy külön-külön vagy együtt. 
- **Csomópontok száma**: A csomópontok száma és a Kubernetes állapota. A fürtcsomópontok jelölt állapotok a *összes*, *készen*, és *nem áll készen* és szűrt külön-külön vagy együtt a választó a diagram felett a. 
- **Tevékenységnapló-pod száma**: A podok számát és a Kubernetes állapota. A podok jelöli az állapotok a *összes*, *függőben lévő*, *futó*, és *ismeretlen* és szűrt külön-külön vagy együtt a a a diagram felett választó. 

Ha úgy vált, hogy **csomópontok**, **tartományvezérlők**, és **tárolók** lap jobb oldalán található az automatikusan megjelennek a tulajdonság panelen.  Azt mutatja, beleértve való Kubernetes-objektumokat rendszerezése címkék, kijelölt elem tulajdonságainak. Kattintson a **>>** összekapcsolása a panelen view\hide a panelen.  

![Példa Kubernetes perspektívák tulajdonságait tartalmazó ablaktáblán](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Kibontja a hierarchia az objektumok, ahogy a Tulajdonságok panelen frissítések alapján a kiválasztott objektum. A panelen is megtekintheti az előre definiált naplókeresések Kubernetes-események kattintva a **nézet Kubernetes eseménynaplók** a panel tetején lévő hivatkozásra. Kubernetes-naplóadatok megtekintésével kapcsolatos további információkért lásd: [keresni a naplókban az adatelemzéshez](#search-logs-to-analyze-data). Amíg a tárolók tekinti át a **tárolók** nézetben látható tároló naplók valós időben. Ez a szolgáltatás és a konfigurációt, és hozzáférést biztosít a szükséges kapcsolatos további információkért lásd: [tároló naplók valós időben az Azure Monitor szolgáltatással tárolók megtekintése](container-insights-live-logs.md). 

Használja a **+ szűrő hozzáadása** lehetőséget az oldal tetején a nézet által az eredmények szűréséhez **szolgáltatás**, **csomópont**, vagy **Namespace** és után a szűrő hatókör kiválasztása, majd válasszon ki egy látható értékeket a **érték(ek) kiválasztása** mező.  A szűrés konfigurálása után érvényes globálisan az AKS-fürt bármely szempontjából megtekintése közben.  A képlet csak az egyenlőségjel támogatja.  A találatok további szűkítéséhez az eredményeket a legelső felül további szűrőket adhat hozzá.  Ha például egy szűrő által megadott **csomópont**, a második szűrőt csak lehetővé tenné, hogy válassza ki **szolgáltatás** vagy **Namespace**.  

![Példa eredmények szűkítéséhez a szűrő használatával](./media/container-insights-analyze/add-filter-option-01.png)

Megadhat egy szűrőt egy lapon továbbra is alkalmazható, amikor kiválaszt egy másik, és törlődik, miután rákattintott a **x** mellett található a megadott szűrő.   

Váltson a **csomópontok** lapra, és a sor hierarchia követi a Kubernetes hálózatiobjektum-modellje, kezdve a fürt egyik csomópontjához. Bontsa ki a csomópontot, és a csomóponton futó egy vagy több podok is megtekintheti. Ha egynél több tároló van csoportosítva podot, jelennek meg az utolsó sorban a hierarchiában. Hány nem pod kapcsolódó számítási feladatok futnak a gazdagépen, ha a gazdagép processzort vagy a rendelkezésre álló memória mennyisége is megtekintheti.

![Kubernetes-csomópontot példahierarchia a teljesítmény nézet](./media/container-insights-analyze/containers-nodes-view.png)

Az Azure Container Instances virtuális csomópontok a Linux operációs rendszert futtató a listában lévő utolsó AKS fürtcsomópont után jelennek meg.  Amikor kibővít egy ACI virtuális csomópont, egy vagy több ACI podok és tárolók a csomóponton futó megtekintheti.  Metrikák nem összegyűjtött és jelentett csomópontok, csak a podok.

![A Container Instances felsorolt példahierarchia csomópont](./media/container-insights-analyze/nodes-view-aci.png)

Egy kibontott csomópontból részletezhet a pod vagy teljesítményadatok szűrve, hogy a tartományvezérlő a tartományvezérlőre, amely a csomóponton futó tárolót. Kattintson az érték a a **vezérlő** oszlopban az adott csomópont számára.   
![Példa Lehatolás csomópontból a teljesítmény nézet-vezérlő](./media/container-insights-analyze/drill-down-node-controller.png)

Válassza ki a tartományvezérlők vagy a lap tetején lévő tárolókat, és tekintse át az állapot- és erőforrás-felhasználást azokat az objektumokat.  Ha ehelyett meg szeretné tekinteni a memóriahasználat, az a **metrika** legördülő listában válassza **memória RSS** vagy **memória-munkakészlet**. **Memória RSS** csak Kubernetes 1.8-as és újabb verziók esetében támogatott. Ellenkező esetben, tekintse meg az értékeket **Min&nbsp; %**  , *NaN&nbsp;%*, azaz egy nem definiált képviselő numerikus típus értéke vagy ábrázolható érték. 

![Tároló csomópontok teljesítmény nézet](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Alapértelmezés szerint a teljesítményadatokat az elmúlt hat órán alapul, de az ablak használatával módosíthatja a **TimeRange** lehetőséget a bal felső sarokban. Kiválasztásával is szűrheti az eredményeket időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95** PERCENTILIS-választójában jelenítse. 

![Az adatok szűrésének. percentilis kiválasztása](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Amikor egérmutatót alatt az oszlopdiagram a **Trend** oszlop, minden egyes sávon látható, vagy a CPU, vagy a memória kihasználtsága, attól függően, amelyek metrika van kijelölve, 15 percen belül minta.  

![Sáv diagram rámutatáskor példa trend](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

A következő példában, vegye figyelembe a lista – az első node *aks-nodepool1 -*, értéke **tárolók** érték 9, amely egy összegző üzembe helyezett tárolókat teljes száma.

![A tárolók száma példája összegzése](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Azt is segítenek gyorsan azonosítani, hogy van-e a tárolók a fürtben lévő csomópontok közötti megfelelő egyensúly. 

A csomópontok megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A gazdagép neve. |
| status | A csomópont állapota Kubernetes nézete. |
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | A csomópont átlagos százalékos PERCENTILIS alapján a kijelölt időszakra. |
| Avg, Min, Max, 50, 90 | Csomópontok átlagos tényleges értéket során kiválasztott töltött idő százalékos érték alapján. Az átlagos érték mérése történik egy csomópont; beállítása CPU/memória felső korlátja podok és tárolók az értéke az avg a gazdagép által jelentett. |
| Containers | A tárolók száma. |
| Hasznos üzemidő | Mivel a csomópont elindult, és újra lett indítva a idejét jelzi. |
| Vezérlők | Csak a tárolók és a podokat. Azt mutatja, hogy melyik tartományvezérlő van a hozzá tartozó. Nem minden podok egy vezérlőt, vannak, ezért néhány megjelenítheti **N/A**. | 
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Oszlopdiagram trend átlagos PERCENTILIS mérőszám százalékos aránya a vezérlő jelöli. |

Válassza ki a választó **tartományvezérlők**.

![Kijelölés vezérlők megjelenítése](./media/container-insights-analyze/containers-controllers-tab.png)

Itt megtekintheti a tartományvezérlők és ACI virtuális csomópont tartományvezérlőkön vagy egy tartományvezérlő nem csatlakozik virtuális csomópont podok teljesítménybeli állapotát.

![< név > tartományvezérlők teljesítmény nézet](./media/container-insights-analyze/containers-controllers-view.png)

A sor hierarchia vezérlő kezdődik, és amikor kibővít egy tartományvezérlő, megtekintheti az egy vagy több podok.  Bontsa ki a pod, és az utolsó sort jeleníti meg a tároló a pod szerint vannak csoportosítva. Egy kibontott vezérlőből származó részletes elemzését is a csomópontot, szűri az adott csomópont teljesítményadatok futtató. Egy tartományvezérlő nem csatlakozik az ACI podok utolsó szerepelnek a listában.

![A Container Instances podok felsorolt példahierarchia vezérlők](./media/container-insights-analyze/controllers-view-aci.png)

Kattintson az érték a a **csomópont** oszlopban az adott vezérlő.   

![Példa részletezés csomópontból a teljesítmény nézet-vezérlő](./media/container-insights-analyze/drill-down-controller-node.png)

A tartományvezérlők megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve.|
| status | A tárolókat, ha befejeződött, például a futó állapotú, összesítő állapotát *OK*, *kilépett*, *sikertelen* *leállítva*, vagy *Szüneteltetve*. Ha a tároló fut-e, de a állapota volt, vagy nem megfelelően jelenik meg, vagy volt nem dolgozza fel az ügynök és a 30 percnél hosszabb ideig nem válaszolt, az állapot értéke *ismeretlen*. További részletek a állapot ikon az alábbi táblázatban szerepelnek.|
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Minden entitás, a kiválasztott metrika és PERCENTILIS hányada összesítő átlaga. |
| Avg, Min, Max, 50, 90  | A kiválasztott PERCENTILIS tárolója átlagos CPU millicore vagy memóriát teljesítményének összesítése. Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Containers | A vezérlő vagy a pod tárolók száma összesen. |
| Újraindul | A tárolók újraindítás száma összegzése. |
| Hasznos üzemidő | Egy tároló indítása óta idejét jelzi. |
| Csomópont | Csak a tárolók és a podokat. Melyik, a hozzá tartozó tartományvezérlő jeleníti meg. | 
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;%| Oszlopdiagram trend a átlagos PERCENTILIS mérőszám, a vezérlő jelöli. |

Az ikonok az állapot mezőben a tárolók online állapotát jelzi:
 
| Ikon | status | 
|--------|-------------|
| ![Készen áll, futó állapot ikon](./media/container-insights-analyze/containers-ready-icon.png) | Fut (kész)|
| ![Várakozás vagy szüneteltetett állapot ikon](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy fel van függesztve|
| ![Utolsó jelentett futó állapotikon](./media/container-insights-analyze/containers-grey-icon.png) | Utolsó jelentett fut, de 30 percnél hosszabb ideig nem válaszolt.|
| ![A sikeres állapot ikon](./media/container-insights-analyze/containers-green-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|

Az állapotjelző ikon alapján a pod biztosít számát jeleníti meg. A legrosszabb kétállapotú jeleníti meg, és amikor a kurzort az állapot, a tárolóban megjeleníti az összes podok egy összesítő állapotát. Ha nincs kész állapotú, az állapot értékét jeleníti meg **(0)**. 

Válassza ki a választó **tárolók**.

![Válassza ki a tárolók megtekintése](./media/container-insights-analyze/containers-containers-tab.png)

Itt megtekintheti az Azure-beli Kubernetes és az Azure Container Instances a tárolók teljesítménybeli állapotát.  

![< név > tartományvezérlők teljesítmény nézet](./media/container-insights-analyze/containers-containers-view.png)

Egy tárolóban, a részletes elemzését is egy pod vagy egy csomópontot, szűri az adott objektum teljesítményadatainak megjelenítéséhez. Kattintson az érték a a **Pod** vagy **csomópont** oszlopban az adott tároló.   

![Példa részletezés csomópontból a teljesítmény nézet-vezérlő](./media/container-insights-analyze/drill-down-controller-node.png)

A tárolók megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve.|
| status | A tárolók, ha van ilyen állapotát. További részletek a állapot ikon a következő táblázatban találhatók.|
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Az átlagos százalékos aránya a kiválasztott metrika és PERCENTILIS minden entitás összegzése. |
| Avg, Min, Max, 50, 90  | Az átlagos CPU millicore vagy a memória teljesítményét a kiválasztott PERCENTILIS tárolója összegzése. Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Pod | A pod tartalmazó tároló.| 
| Csomópont |  Csomópont, amelyben a tároló található. | 
| Újraindul | Egy tároló indítása óta idejét jelzi. |
| Hasznos üzemidő | Mivel egy tároló volt elindítva vagy újraindítva idejét jelzi. |
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Oszlopdiagram trend átlagos PERCENTILIS mérőszám százalékos aránya a tárolót képviseli. |

Az állapot mezőben az ikonok jelzi a podok, online válik, az alábbi táblázatban leírtak szerint:
 
| Ikon | status |  
|--------|-------------|  
| ![Készen áll, futó állapot ikon](./media/container-insights-analyze/containers-ready-icon.png) | Fut (kész)|  
| ![Várakozás vagy szüneteltetett állapot ikon](./media/container-insights-analyze/containers-waiting-icon.png) | Várakozás vagy fel van függesztve|  
| ![Utolsó jelentett futó állapotikon](./media/container-insights-analyze/containers-grey-icon.png) | Utolsó jelentett fut, de a 30 percnél hosszabb ideig nem válaszolt|  
| ![Elbocsátott állapotikon](./media/container-insights-analyze/containers-terminated-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|  
| ![Sikertelen állapotikon](./media/container-insights-analyze/containers-failed-icon.png) | Hibás állapotban |  


## <a name="container-data-collection-details"></a>Tároló-adatokat gyűjtő részletei
Container Insights tároló-gazdagépek és -tárolók különböző mérőszámokban és naplófájlokban teljesítményadatokat gyűjt. Az adatgyűjtés percen át 3 percenként történik.

### <a name="container-records"></a>Tárolórekordok

Az alábbi táblázatban példák a tárolók és az adattípusok, amely a napló keresési eredmények között megjelenik az Azure Monitor által gyűjtött rekordok jelennek meg:

| Adattípus | Naplókeresési adatok típusa | Mezők |
| --- | --- | --- |
| Gazdagépek és-tárolók teljesítménye | `Perf` | Számítógép, ObjectName, CounterName &#40;processzoridő, a lemez beolvassa a MB, lemezre ír MB memória kihasználtsága (MB), hálózati fogadott bájtok, hálózati küldése memória, processzor kihasználtsága (mp), hálózati&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem |
| Tároló leltár | `ContainerInventory` | TimeGenerated, a számítógép, a tároló nevét, ContainerHostname, kép, ImageTag, ContainerState, ExitCode, EnvironmentVar, a parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, cseréjekor a Tárolóazonosító, ImageID |
| Tárolórendszerkép leltárát | `ContainerImageInventory` | TimeGenerated, számítógép, kép, ImageTag, ImageSize, VirtualSize, futó, fel van függesztve, leállítja, sikertelen, SourceSystem, ImageID, TotalContainer |
| Tároló-napló | `ContainerLog` | TimeGenerated, a számítógép, a lemezkép-azonosító, a Tárolónév esetén LogEntrySource, LogEntry, SourceSystem, cseréjekor a Tárolóazonosító |
| Container service-napló | `ContainerServiceLog`  | TimeGenerated, számítógép, TimeOfCommand, kép, a parancs, SourceSystem, cseréjekor a Tárolóazonosító |
| Tárolócsomópont-készlet | `ContainerNodeInventory_CL`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Tárolófolyamat | `ContainerProcess_CL` | TimeGenerated, számítógép, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-fürt podok leltára | `KubePodInventory` | TimeGenerated, számítógép, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, szolgáltatásnév, ControllerKind, ControllerName, tároló, Cseréjekor a Tárolóazonosító, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, pod IP-címére, SourceSystem |
| Kubernetes-fürt csomópontjai részét leltára | `KubeNodeInventory` | TimeGenerated, számítógép, ClusterName, ClusterId, LastTransitionTimeReady, címkék, állapot, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-események | `KubeEvents_CL` | TimeGenerated, számítógép, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, üzenet, SourceSystem | 
| Kubernetes-fürtben a szolgáltatások | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| A Kubernetes-fürt csomópontok részei a teljesítmény-mérőszámok | Teljesítményoptimalizált &#124; ahol ObjectName == "K8SNode" | Számítógép, ObjectName, CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem | 
| A Kubernetes-fürt része tárolók teljesítménymetrikáit | Teljesítményoptimalizált &#124; ahol ObjectName == "K8SContainer" | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Keresési naplókat az adatelemzéshez
A log Analytics segítségével keresése trendek, diagnosztizálhatja a szűk keresztmetszeteket, előrejelzési vagy összevetését adatokat, amelyek segítségével határozza meg, hogy optimális működik-e az aktuális fürtbeállításokat. Előre definiált naplókeresések rögtön használatba, illetve adja vissza a kívánt módon szabhatja testre a biztosított. 

Kiválasztásával a munkaterület az adatok interaktív elemzés céljából is végezhet a **nézet Kubernetes eseménynaplók** vagy **tárolónaplók megtekintése** lehetőség az előnézeti ablaktáblában láthatja. A **naplóbeli keresés** ablak jobb oldalán, az Azure portal oldalán, amelyen korábban volt.

![Adatok elemzése a Log Analyticsben](./media/container-insights-analyze/container-health-log-search-example.png)   

A tároló naplók kimenete, a Log Analytics továbbíthatja a rendszer az STDOUT és STDERR. Azure figyelő által figyelt Azure által felügyelt Kubernetes-(AKS), mert Kube rendszer nem gyűjti jelenleg generált adatok nagy mennyisége miatt. 

### <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezések
Gyakran hasznos hozhatók létre olyan lekérdezések, amelyek például vagy a kettő, és módosítsa őket az igényeinek. Annak érdekében, hogy speciális lekérdezések felépítését, kísérletezhet, és az alábbi mintalekérdezések:

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124;Számítógép, név, kép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime projekt<br> &#124;táblázat megjelenítése | Összes egy tároló tárolóéletciklus-adat listázása| 
| KubeEvents_CL<br> &#124;ahol not(isempty(Namespace_s))<br> &#124;Rendezés a TimeGenerated desc<br> &#124;táblázat megjelenítése | Kubernetes-események|
| ContainerImageInventory<br> &#124;summarize AggregatedValue futó = count() by ImageTag, kép | Rendszerképek leltára | 
| **Válassza ki a megjelenítendő lehetőség**:<br> Perf<br> &#124;ahol ObjectName == "K8SContainer" és a CounterName == "cpuUsageNanoCores" &#124; AvgCPUUsageNanoCores összefoglalója avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló CPU | 
| **Válassza ki a megjelenítendő lehetőség**:<br> Perf<br> &#124;ahol ObjectName == "K8SContainer" és a CounterName == "memoryRssBytes" &#124; AvgUsedRssMemoryBytes összefoglalója avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló memória |

## <a name="alerting"></a>Riasztások kezelése
Az Azure Monitor for containers szolgáltatásban nem tartalmazza a riasztásokat, amelyek másolhatja és módosíthatja a támogató folyamatok és eljárások alapján előre meghatározott. Addig is tekintse meg [riasztások létrehozása az Azure Monitor szolgáltatással](../../monitoring-and-diagnostics/alert-log.md?toc=/azure/azure-monitor/toc.json) , és ismerje meg, hogyan hozhat létre saját riasztások készlete.  