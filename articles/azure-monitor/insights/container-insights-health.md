---
title: A Kubernetes-fürt állapotának figyelése az Azure Monitor tárolókhoz szolgáltatásával | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan tekintheti meg és elemezheti az AKS és a nem AKS-fürtök állapotát az Azure Monitor tárolókhoz.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843990"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>A Kubernetes-fürt állapotának ismertetése az Azure Monitor tárolókhoz szolgáltatásával

Az Azure Monitor tárolók, figyeli és jelenti a felügyelt infrastruktúra-összetevők állapotát, és az azure-figyelő által támogatott bármely Kubernetes-fürtön futó összes csomópont állapotát. Ez a felület túlnyúlik a fürt állapotállapotának számított és jelentett a [többfürtös nézetben,](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)ahol most már megismerheti, ha egy vagy több csomópont a fürterőforrás-korlátozott, vagy egy csomópont vagy pod nem érhető el, amely hatással lehet a futó alkalmazás a fürtben a válogatott metrikák alapján.

>[!NOTE]
>Az Állapot funkció jelenleg nyilvános előzetes verzióban érhető el.
>

Az Azure Monitor tárolókhoz való engedélyezéséről az [Onboard Azure Monitor tárolókhoz](container-insights-onboard.md)című témakörben talál.

>[!NOTE]
>Az AKS Engine-fürtök támogatásához ellenőrizze, hogy azok megfelelnek-e az alábbiaknak:
>- A [HELM ügyfél](https://helm.sh/docs/using_helm/)legújabb verzióját használja.
>- A containerized ügynök verziója *microsoft/oms:ciprod11012019*. Az ügynök frissítéséhez tekintse meg [a frissítési ügynököt a Kubernetes-fürtön.](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster)
>

## <a name="overview"></a>Áttekintés

Az Azure Monitor tárolók, az Állapot (előzetes verzió) szolgáltatás proaktív állapotfigyelésa a Kubernetes-fürt segítségével azonosítani és diagnosztizálni a problémákat. Lehetővé teszi az észlelt jelentős problémák megtekintését. A fürt állapotát kiértékelő figyelők a fürt tárolóügynökén futnak, és az állapotadatok a Log Analytics-munkaterület **KubeHealth-táblájába** vannak írva. 

A Kubernetes-fürt állapota a következő Kubernetes-objektumok és absztrakciók által rendezett figyelési forgatókönyveken alapul:

- Kubernetes-infrastruktúra – a processzor- és memóriakihasználtság kiértékelésével a fürtben telepített csomópontokon futó Kubernetes API-kiszolgáló, A Replikakészletek és a DaemonSets összesítését biztosítja.

    ![Kubernetes infrastruktúra állapotösszesítési nézete](./media/container-insights-health/health-view-kube-infra-01.png)

- Csomópontok – biztosít egy összesítést a csomópontkészletek és az egyes csomópontok állapota az egyes készletekben, a PROCESSZOR és a memória kihasználtságának kiértékelésével, valamint egy csomópont állapotát a Kubernetes által jelentett állapot.

    ![Csomópontok állapotösszesítő nézete](./media/container-insights-health/health-view-nodes-01.png)

Jelenleg csak egy virtuális kubelet állapota támogatott. A virtuális kublet csomópontok processzor- és memóriakihasználtságának állapota **ismeretlen,** mivel nem érkezik jel.

Az összes figyelő hierarchikus elrendezésben jelenik meg az Állapothierarchia ablaktáblában, ahol a Kubernetes-objektumot vagy absztrakciót (azaz a Kubernetes-infrastruktúrát vagy csomópontokat) képviselő összesítő figyelő a legfelső monitor, amely az összes kombinált állapotát tükrözi. gyermekmonitorok. Az állapot okának kiszámításához használt legfontosabb figyelési forgatókönyvek a következők:

* Értékelje ki a processzor kihasználtságát a csomópontról és a tárolóból.
* Értékelje ki a memória kihasználtságát a csomópontról és a tárolóból.
* Podok és csomópontok állapota a Kubernetes által jelentett kész állapotuk számítása alapján.

Az állapotot a következő ikonok jelölik:

|Ikon|Jelentés|  
|--------|-----------|  
|![Kifogástalan állapotot jelző zöld pipa ikonja](./media/container-insights-health/healthyicon.png)|Sikeres, az állapot rendben van (zöld)|  
|![Figyelmeztetést jelző sárga háromszög és felkiáltójel](./media/container-insights-health/warningicon.png)|Figyelmeztetés (sárga)|  
|![Kritikus állapotot jelző piros gomb fehér X-szel](./media/container-insights-health/criticalicon.png)|Kritikus (piros)|  
|![Szürkénjelenik ikon](./media/container-insights-health/grayicon.png)|Ismeretlen (szürke)|  

## <a name="monitor-configuration"></a>Monitor konfigurációja

Az Azure Monitort a tárolók állapota szolgáltatást támogató figyelők viselkedésének és konfigurációjának megismeréséhez olvassa el az [Állapotfigyelő konfigurációs útmutatóját.](container-insights-health-monitors-config.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>AKS- vagy nem AKS-fürt állapotának megtekintése

Az Azure Monitor tárolók állapota (előzetes verzió) funkció közvetlenül érhető el egy AKS-fürt kiválasztásával **Insights** a bal oldali ablaktáblában az Azure Portalon. Az **Insights (Insights)** csoportban válassza a **Tárolók lehetőséget.** 

Ha nem AKS-fürtből szeretne állapotot, azaz a helyszínen vagy az Azure Stackben üzemeltetett AKS-motor-fürtből való állapotmegtekintéséhez válassza az **Azure Monitor** elemet az Azure Portalbal oldali ablaktáblájában. Az **Insights (Insights)** csoportban válassza a **Tárolók lehetőséget.**  A többfürtös lapon válassza ki a nem AKS-fürtöt a listából.

Az Azure Monitor tárolók, a **fürt** lapon válassza **az Állapot**lehetőséget.

![Példa fürtállapot-irányítópultra](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>A fürt állapotának áttekintése

Amikor az Állapot lap megnyílik, alapértelmezés szerint a **Kubernetes-infrastruktúra** van kiválasztva az **Állapot szempont** rácsban.  A hálózat a Kubernetes-infrastruktúra és a fürtcsomópontok aktuális állapotösszesítési állapotát foglalja össze. Bármelyik állapotszempont kiválasztása frissíti az eredményeket az Állapothierarchia ablaktáblában (azaz a középső ablaktáblában), és az összes gyermekfigyelőt hierarchikus elrendezésben jeleníti meg, az aktuális állapotukat jelenítve meg. Ha további információkat szeretne megtekinteni bármely függő monitorról, kijelölhet egyet, és a tulajdonságablak automatikusan megjelenik a lap jobb oldalán. 

![Fürt állapottulajdonság-ablaktáblája](./media/container-insights-health/health-view-property-pane.png)

A tulajdonságpanelen a következőket olvashatja:

- Az **Áttekintés** lapon a kijelölt figyelő aktuális állapotát, a figyelő utolsó számításának időpontjának és az utolsó állapotváltozás nak a módját jeleníti meg. További információk jelennek meg a hierarchiában kiválasztott figyelő típusától függően.

    Ha az Állapothierarchia ablaktáblán kijelöl egy összesítő figyelőt, a tulajdonságablak **Áttekintés** lapján a hierarchiában lévő gyermekfigyelők teljes számának összesítése, valamint a kritikus, figyelmeztetési és kifogástalan állapotú összesítő figyelők összesítése látható. 

    ![Állapottulajdonság-ablaktábla Áttekintés e lapja az összesítő figyelőhöz](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Ha kijelöl egy egységfigyelőt az Állapothierarchia ablaktáblában, az **az Utolsó állapot csoportban** is megjelenik az előző minták módosítása, amelyeket a tárolóügynök számított ki és jelentett az elmúlt négy órában. Ez az egységfigyelők számításán alapul, amely több egymást követő érték összehasonlítására alkalmas az állapot meghatározásához. Ha például a *Pod ready state* unit figyelőt választotta, akkor az utolsó két mintát jeleníti meg, amelyeket a *ConsecutiveSamplesForStateTransition*paraméter vezérel. További információt az [egységmonitorok](container-insights-health-monitors-config.md#unit-monitors)részletes leírásában talál.
    
    ![Az Állapottulajdonság ablaktábla – Áttekintés lap](./media/container-insights-health/health-overview-unit-monitor.png)

    Ha az **utolsó állapot változása** által jelentett idő egy nap vagy annál régebbi, akkor a figyelő állapotának nem változik. Ha azonban az egységfigyelőhöz utoljára érkezett minta több mint négy órás, ez valószínűleg azt jelzi, hogy a tárolóügynök nem küldött adatokat. Ha az ügynök tudja, hogy egy adott erőforrás létezik, például egy csomópont, de nem kapott adatokat a csomópont processzor- vagy memóriakihasználtsági figyelőitől (például), akkor a figyelő állapota **Ismeretlen**értékre van állítva.  

- A**Config** lapon megjeleníti az alapértelmezett konfigurációs paraméter beállításait (csak az egységfigyelők esetében, nem az összesítő monitorokesetében) és azok értékeit.
- A **Tudás** lapon a figyelő viselkedését és a nem megfelelő állapot kiértékelését ismertető információkat tartalmazza.

A lap adatainak figyelése nem frissül automatikusan, és a frissítés **a** lap tetején lehetőséget kell választania a fürttől kapott legfrissebb állapot megtekintéséhez.

## <a name="next-steps"></a>További lépések

Tekintse meg [a naplólekérdezési példákat](container-insights-log-search.md#search-logs-to-analyze-data) az előre definiált lekérdezések és példák megtekintéséhez vagy testreszabásához a fürtök riasztásához, megjelenítéséhez vagy elemzéséhez.
