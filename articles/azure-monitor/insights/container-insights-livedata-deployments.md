---
title: Az Azure Monitor megtekintése a tárolók központi telepítéséhez (előzetes verzió) | Microsoft dokumentumok
description: Ez a cikk ismerteti a Kubernetes-telepítések valós idejű nézetét kubectl használata nélkül az Azure Monitor tárolókhoz.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404772"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>A központi telepítések megtekintése (előzetes verzió) valós időben

Az Azure Monitor tárolók, a view Deployments (előzetes verzió) funkció emulálja a közvetlen `kubeclt get deployments` `kubectl describe deployment {your deployment}` hozzáférést kubernetes telepítési objektumok valós időben kiteszik a és a parancsok. 

>[!NOTE]
>Ez a szolgáltatás nem támogatja a [privát fürtökként](https://azure.microsoft.com/updates/aks-private-cluster/) engedélyezett AKS-fürtöket. Ez a szolgáltatás a Kubernetes API közvetlen elérésére támaszkodik a böngészőből származó proxykiszolgálón keresztül. Ha engedélyezi a hálózati biztonságot a Kubernetes API letiltásának a proxyról, az blokkolja ezt a forgalmat. 

>[!NOTE]
>Ez a funkció minden Azure-régióban elérhető, beleértve az Azure China-t is. Jelenleg nem érhető el az Azure US Government.

További információért tekintse át a Kubernetes [központi telepítésekről szóló dokumentációját.](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 

## <a name="how-it-works"></a>Működés

A Live Data (előzetes verzió) funkció közvetlenül hozzáfér a Kubernetes API-hoz, és további információk a hitelesítési modellről [itt](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)találhatók. 

A Központi telepítések (előzetes verzió) szolgáltatás egyszeri (frissíthető) terhelést hajt végre a központi telepítések végpontja ellen. `/apis/apps/v1/deployments` Lehetővé teszi egy adott központi telepítés kiválasztását és az adott `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`központi telepítés leírásának részleteit a központi telepítés végpontjával szemben. 

A lap bal felső részén a **Frissítés** lehetőséget választva a központi telepítési lista frissül. Ez szimulálja a `kubectl` parancs újbóli futtatását. 

>[!IMPORTANT]
>A szolgáltatás működése során a rendszer nem tárol véglegesen adatokat. A munkamenet során rögzített összes információ törlődik, amikor bezárja a böngészőt, vagy elnavigál a böngészőből.  

>[!NOTE]
>Élő adatok (előzetes verzió) adatok at a konzolról egy Azure-irányítópulton rögzítheti.

## <a name="deployments-describe"></a>A központi telepítések leírják

A központi telepítés leírásának részleteinek megtekintéséhez, amely egyenértékű a `kubectl describe deployment`alkalmazással, hajtsa végre a következő lépéseket.

1. Az Azure Portalon keresse meg az AKS-fürt erőforráscsoportot, és válassza ki az AKS-erőforrást.

2. Az AKS-fürt irányítópultján a **Figyelés** a bal oldalon válassza az **Insights**lehetőséget. 

3. Válassza a **Központi telepítések (előzetes verzió)** lapot.

    ![Központi telepítések nézet az Azure Portalon](./media/container-insights-livedata-deployments/deployment-view.png)

A nézet az összes futó központi telepítés listáját, valamint a névteret és egyéb részletes `kubectl get deployments –all-namespaces`információkat jeleníti meg, amelyek a parancs végrehajtását utánozzák. Az eredményeket az oszlopok bármelyikének kijelölésével rendezheti. 

![A Központi telepítések tulajdonságaiablak részletei](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Amikor kiválaszt egy központi telepítést a listából, a tulajdonságablak automatikusan megjelenik a lap jobb oldalán. A kijelölt központi telepítéssel kapcsolatos információkat jeleníti meg, `kubectl describe deployment {deploymentName}`amelyeket a parancs futtatása esetén meg szeretne tekinteni. Lehet, hogy észrevette, hogy a leíró információ hiányzik néhány részletet. Leginkább a **sablon** hiányzik. A **Nyers** lap kiválasztásával navigálhat az elemzés nélküli Leírás részleteihez.  

![Központi telepítések tulajdonságaiablak nyers részletei](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

A központi telepítés részleteinek áttekintése közben valós időben láthatja a tárolónaplókat és -eseményeket. Válassza az **Élő konzol megjelenítése lehetőséget,** és a Live Data (előzetes verzió) konzol ablaktábla megjelenik a központi telepítési adatrács alatt, ahol megtekintheti az élő naplóadatokat egy folyamatos adatfolyamban. Ha a lehívási állapot jelzőzöld pipát jelenít meg, amely az ablaktábla jobb szélén található, az azt jelenti, hogy az adatok lehívhatók, és a konzolra való streamelés megkezdődik.

Névtér- vagy fürtszintű események szerint is szűrhet. Ha többet szeretne megtudni a konzolon valós idejű megtekintési adatokról, olvassa el az [Élő adatok megtekintése (előzetes verzió) az Azure Monitor tárolókhoz című témakört.](container-insights-livedata-overview.md) 

![A központi telepítések élő adatokat tekintenek meg a konzolon](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>További lépések

- Az Azure [Kubernetes-szolgáltatás állapotának megtekintése.](container-insights-analyze.md)

- Tekintse meg [a naplólekérdezési példákat](container-insights-log-search.md#search-logs-to-analyze-data) az előre definiált lekérdezések és példák megtekintéséhez riasztások, vizualizációk létrehozásához vagy a fürtök további elemzéséhez.
