---
title: A Kubernetes-fürt állapotának figyelése az Azure Monitor for containers szolgáltatással | Microsoft Docs
description: Ez a cikk bemutatja, hogyan tekintheti meg és elemezheti az AK-beli és a nem ak-beli fürtök állapotát a Azure Monitor for containers használatával.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 9ee710eb916923756633e65f3287751ba9a9dde3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405095"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Kubernetes-fürt állapotának megismerése a Tárolókhoz készült Azure Monitorral

A tárolók Azure Monitorával figyeli és jelenti a felügyelt infrastruktúra összetevőinek, valamint a Azure Monitor által a tárolók által támogatott Kubernetes-fürtökön futó összes csomópontnak az állapotát. Ez a felület a fürt állapotának kiszámításához és a [több fürtre vonatkozó nézeten](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)jelentett állapotához is kiterjed, ahol most már megtudhatja, hogy a fürt egy vagy több csomópontja rendelkezik-e erőforrás-korlátozással, vagy egy csomópont vagy pod nem érhető el, amely hatással lehet egy futó alkalmazásra a fürtben, a kurátori mérőszámok alapján.

>[!NOTE]
>Az állapot szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
>

További információ a tárolók Azure Monitorének engedélyezéséről: [Azure monitor a tárolók számára](container-insights-onboard.md).

>[!NOTE]
>Az AK-alapú motor-fürtök támogatásához ellenőrizze, hogy az megfelel-e a következőknek:
>- A [Helm-ügyfél](https://helm.sh/docs/using_helm/)legújabb verzióját használja.
>- A tároló ügynök verziója a *Microsoft/OMS: ciprod11012019*. Az ügynök frissítését lásd: az [ügynök frissítése a Kubernetes-fürtön](container-insights-manage-agent.md#upgrading-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Áttekintés

A tárolók Azure Monitor a Health (előzetes verzió) funkció a Kubernetes-fürt proaktív állapotának figyelését biztosítja a problémák azonosításához és diagnosztizálásához. Lehetővé teszi az észlelt jelentős problémák megtekintését. A fürt állapotának kiértékelésére szolgáló figyelők a fürtben lévő tároló ügynökön futnak, és az állapotadatok a Log Analytics munkaterületen lévő **KubeHealth** táblába íródnak. 

A Kubernetes-fürt állapota a következő Kubernetes-objektumok és absztrakciók által szervezett figyelési forgatókönyveken alapul:

- Kubernetes-infrastruktúra – a Kubernetes API-kiszolgáló,-ReplicaSets és-DaemonSets összesítését biztosítja a fürtben üzembe helyezett csomópontokon a processzor és a memória kihasználtságának kiértékelésével, valamint a hüvely rendelkezésre állásával

    ![Kubernetes infrastruktúra állapotának összesítési nézete](./media/container-insights-health/health-view-kube-infra-01.png)

- Csomópontok – az egyes készletekben található csomópont-készletek és az egyes csomópontok állapotának összesítését biztosítja a processzor és a memória kihasználtságának kiértékelésével, valamint a csomópontnak a Kubernetes által jelentett állapotával.

    ![Csomópontok állapotának összesítési nézete](./media/container-insights-health/health-view-nodes-01.png)

Jelenleg csak egy virtuális kubelet állapota támogatott. A virtuális kublet-csomópontok CPU-kihasználtságának és memóriahasználat állapotának állapota **ismeretlen**, mert a rendszer nem fogadja a jeleket.

Az állapot-hierarchia ablaktáblán az összes figyelő hierarchikus elrendezésben látható, ahol a Kubernetes objektumot vagy absztrakciót jelképező összesítő figyelő (azaz Kubernetes-infrastruktúra vagy-csomópontok) a legfelső szintű figyelő, amely az összes elem együttes állapotát tükrözi függő gyermek figyelők. Az állapot származtatása kulcsfontosságú figyelési forgatókönyvek:

* Értékelje ki a CPU-kihasználtságot a csomópontból és a tárolóból.
* A memória kihasználtságának kiértékelése a csomópontról és a tárolóból.
* A hüvelyek és a csomópontok állapota a Kubernetes által jelentett készenléti állapot kiszámítása alapján.

Az állapotot a következő ikonok jelölik:

|Ikon|Jelentés|  
|--------|-----------|  
|![Kifogástalan állapotot jelző zöld pipa ikonja](./media/container-insights-health/healthyicon.png)|Sikeres, az állapot rendben van (zöld)|  
|![Figyelmeztetést jelző sárga háromszög és felkiáltójel](./media/container-insights-health/warningicon.png)|Figyelmeztetés (sárga)|  
|![Kritikus állapotot jelző piros gomb fehér X-szel](./media/container-insights-health/criticalicon.png)|Kritikus (piros)|  
|![Szürke – ki ikon](./media/container-insights-health/grayicon.png)|Ismeretlen (szürke)|  

## <a name="monitor-configuration"></a>Konfiguráció figyelése

A containers Health Azure Monitort támogató egyes figyelők viselkedésének és konfigurációjának megismeréséhez tekintse meg az [állapotfigyelő konfigurációs útmutató](container-insights-health-monitors-config.md)című témakört.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>AK-vagy nem ak-alapú fürt állapotának megtekintése

A Azure Monitor for containers Health (előzetes verzió) szolgáltatáshoz való hozzáférés közvetlenül egy AK-fürtből érhető el **, ha az** Azure Portal bal oldali ablaktáblájában kijelöli a kiszámítások lehetőséget. Az **áttekintések** szakaszban válassza a **tárolók**lehetőséget. 

Ha egy nem ak-alapú fürt állapotát szeretné megtekinteni, amely egy helyszíni vagy Azure Stackon üzemeltetett AK-alapú motor-fürt, válassza a **Azure monitor** lehetőséget a Azure Portal bal oldali ablaktábláján. Az **áttekintések** szakaszban válassza a **tárolók**lehetőséget.  A többfürtes lapon válassza ki a nem ak típusú fürtöt a listából.

A tárolók Azure Monitor a **fürt** lapon válassza az **állapot**lehetőséget.

![Fürt állapotának irányítópultja – példa](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>A fürt állapotának áttekintése

Amikor megnyílik az állapot lap, alapértelmezés szerint a **Kubernetes-infrastruktúra** van kiválasztva az **állapotadatok** rácsban.  A rács a Kubernetes-infrastruktúra és-fürtcsomópontok aktuális állapotának összesítő állapotát összegzi. Az állapot kiválasztásával frissítheti az eredményeket az állapot-hierarchia ablaktáblán (azaz a középső ablaktáblán), és az összes gyermek figyelőt egy hierarchikus elrendezésben jeleníti meg, amely megjeleníti az aktuális állapotot. Ha további információkat szeretne megtekinteni a függő figyelővel kapcsolatban, kiválaszthat egyet, és az oldal jobb oldalán automatikusan megjelenik a Tulajdonságok ablaktábla. 

![A fürt állapota tulajdonság ablaktábla](./media/container-insights-health/health-view-property-pane.png)

A Tulajdonságok ablaktáblán a következőket sajátíthatja el:

- Az **Áttekintés** lapon a kiválasztott figyelő aktuális állapotát jeleníti meg, a figyelő utolsó kiszámítását és a legutóbbi állapot változását követően. A hierarchiában kiválasztott figyelő típusától függően további információk jelennek meg.

    Ha kijelöl egy összesítő figyelőt az állapotfigyelő hierarchia ablaktáblán, akkor a Tulajdonságok ablaktábla **Áttekintés** lapján látható a hierarchiában lévő gyermek figyelők összesített száma, valamint a kritikus, figyelmeztetés és kifogástalan állapotú összesített figyelők számának összesítése. 

    ![Az állapot tulajdonságai ablaktábla Áttekintés lapja az összesített figyelőhöz](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Ha a Health hierarchia ablaktáblán kiválaszt egy egység-figyelőt, az **az utolsó négy** órában a tároló ügynök által kiszámított és jelentett előző mintákat is megjeleníti. Ez az egység figyeli a számítást, amely több egymást követő érték összehasonlításával határozza meg az állapotát. Ha például a *Pod Ready állapotú* egység figyelőt választotta, az a *ConsecutiveSamplesForStateTransition*paraméter által vezérelt utolsó két mintát mutatja. További információkért tekintse meg az [egység figyelők](container-insights-health-monitors-config.md#unit-monitors)részletes leírását.
    
    ![Állapot tulajdonságai ablaktábla – Áttekintés lap](./media/container-insights-health/health-overview-unit-monitor.png)

    Ha a **Legutóbbi állapotváltozás** által jelentett idő egy nap vagy régebbi, akkor a figyelő állapota nem módosul. Ha azonban egy egység figyelőhöz kapott utolsó minta több mint négy órája van, akkor ez valószínűleg azt jelzi, hogy a tároló ügynök nem küldött adatokat. Ha az ügynök tudja, hogy létezik egy adott erőforrás, például egy csomópont, de nem kapott adatokat a csomópont CPU-vagy memóriahasználat-figyelőtől (például), akkor a figyelő állapota **ismeretlenre**van állítva.  

- A**config** (konfiguráció) lapon a konfigurációs paraméterek alapértelmezett beállításai (csak az egység figyelőknél, a nem összesítő figyelőknél) és azok értékei láthatók.
- Az **ismeret** lapon a figyelő viselkedését és a nem Kifogástalan állapot kiértékelését ismertető információt tartalmaz.

Az ezen a lapon lévő figyelési adatok nem frissülnek automatikusan, és az oldal tetején lévő **frissítés** lehetőség kiválasztásával megtekintheti a fürttől kapott legutóbbi állapotot.

## <a name="next-steps"></a>Következő lépések

Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásához, megjelenítéséhez vagy elemzéséhez.
