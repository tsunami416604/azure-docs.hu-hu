---
title: Az Azure Kubernetes Service (AKS) vezérlő naplók megtekintése
description: Ismerje meg, hogyan engedélyezése és megtekintése a naplókban talál a fő Kubernetes-csomópontot az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 07/26/2018
ms.author: iainfou
ms.openlocfilehash: 7caa98b65b35d1eb1a000f1e9099fbf8eb3f8861
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406041"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Engedélyezze, és tekintse át a Kubernetes Azure Kubernetes Service (AKS) naplózza a fő csomópont

Az Azure Kubernetes Service (AKS), mint például a fő összetevőket a *kube-apiserver* és *kube-tartományvezérlő-manager* felügyelt szolgáltatásként biztosított. Létrehozásához és kezeléséhez a csomópontokat, amelyeken a *kubelet* és tároló-futtatókörnyezet, és a felügyelt Kubernetes API-kiszolgálón keresztül alkalmazások üzembe helyezése. Az alkalmazások és szolgáltatások hibáinak elhárítása érdekében szükség lehet a fő összetevői által létrehozott naplók megtekintése. Ez a cikk bemutatja, hogyan lehet engedélyezni, és a fő Kubernetes-összetevők naplóinak lekérdezése az Azure Log Analytics használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk egy meglévő AKS-fürtöt az Azure-fiókban futó igényel. Ha Ön még nem rendelkezik egy AKS-fürtöt, hozzon létre egyet a [Azure CLI-vel] [ cli-quickstart] vagy [az Azure portal][portal-quickstart]. A log Analytics mindkét RBAC dolgozik, és nem RBAC engedélyezve van az AKS-fürt.

## <a name="enable-diagnostics-logs"></a>Diagnosztikai naplók engedélyezése

Könnyebben gyűjteni, és tekintse át a több forrásból származó adatokat, a Log Analytics biztosít egy lekérdezési nyelvet és elemzési motor, amely a környezet elemzéseket biztosít. A munkaterület szerinti rendezés és elemezheti az adatokat, és más Azure-szolgáltatások például az Application Insights és a Security Center integrálható. A különböző platform segítségével a naplók elemzéséhez, választhatja a diagnosztikai naplók küldése egy Azure storage-fiók vagy eseményközpont. További információkért lásd: [Mi az Azure Log Analytics?] [log-analytics-overview].

A log Analytics engedélyezve van, és kezeli az Azure Portalon. Ahhoz, hogy a Kubernetes az AKS-fürt fő összetevőinek naplógyűjtés, egy webböngészőben nyissa meg az Azure Portalon, és kövesse az alábbi lépéseket:

1. Válassza ki például az AKS-fürt erőforráscsoportjának *myResourceGroup*. Ne válassza ki az erőforráscsoport, amely tartalmaz az egyes AKS-fürt erőforrásokat, például *MC_myResourceGroup_myAKSCluster_eastus*.
1. A bal oldalon válassza ki a **diagnosztikai beállítások**.
1. Válassza ki például az AKS-fürt *myAKSCluster*, majd válassza a **diagnosztika bekapcsolása**.
1. Adjon meg egy nevet, például *myAKSLogs*, majd válassza a **Küldés a Log Analyticsnek**.
    * Válassza ki a *konfigurálása* – Naplóelemzés, majd válassza ki a meglévő munkaterület vagy **új munkaterület létrehozása**.
    * Hozzon létre egy munkaterületet van szüksége, ha adjon meg egy nevet, egy erőforráscsoportot és egy helyen.
1. A naplók listájából válassza ki a naplók például engedélyezni kívánt *kube-apiserver*, *kube-tartományvezérlő-manager*, és *kube-ütemező*. Lépjen vissza, és az összegyűjtött naplók módosítani, ha a Log Analytics engedélyezve vannak.
1. Ha elkészült, válassza ki a **mentése** a kiválasztott naplók gyűjtésének engedélyezéséhez.

Az alábbi példában portál képernyőképe a *diagnosztikai beállítások* ablakban, majd létrehozhat egy Log Analytics-munkaterület:

![Log Analytics-munkaterületet a Log Analytics az AKS-fürt engedélyezése](media/view-master-logs/enable-oms-log-analytics.png)

>[!NOTE]
>OMS-munkaterülete mostantól Log Analytics-munkaterületek nevezik. 

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Az AKS-fürtöt teszt podján ütemezése

Az egyes naplófájlokat hoznak létre, hozzon létre egy új pod az AKS-fürt. A következő példa YAML-jegyzék segítségével hozzon létre egy alapszintű NGINX-példány. Hozzon létre egy fájlt `nginx.yaml` egy szövegszerkesztőben, és illessze be az alábbi tartalommal:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

A pod-létrehozása a [kubectl létrehozása] [ kubectl-create] parancsot, majd adja meg a YAML-fájlt az alábbi példában látható módon:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Összegyűjtött naplók megtekintése

Eltarthat néhány percig, engedélyezni kell, és a Log Analytics-munkaterületen jelennek meg a diagnosztikai naplókat. Az Azure Portalon válassza ki az erőforráscsoportot, a Log Analytics-munkaterület, például *myResourceGroup*, majd válassza ki a Log Analytics-erőforrás, például *myAKSLogs*.

![Válassza ki a Log Analytics-munkaterületet az AKS-fürt esetében](media/view-master-logs/select-log-analytics-workspace.png)

A bal oldalon válassza ki a **naplóbeli keresés**. Megtekintéséhez a *kube-apiserver*, a szövegmezőbe írja be a következő lekérdezést:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Rengeteg valószínűleg vissza az API-kiszolgálóhoz. Hatókörét le a lekérdezés a naplók megtekintéséhez a NGINX-pod az előző lépésben létrehozott kapcsolatban, vegye fel egy további *ahol* utasítással keresse meg *podok/nginx* , ahogyan a következő példalekérdezés:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

A meghatározott naplókban az NGINX-pod jelennek meg, a következő példaként szolgáló képernyőképen látható módon:

![Log Analytics lekérdezési példa az NGINX-pod eredményei](media/view-master-logs/log-analytics-query-results.png)

További a naplók megtekintéséhez, frissítheti a lekérdezést a *kategória* neve a *kube-tartományvezérlő-manager* vagy *kube-ütemező*, attól függően, hogy milyen további naplózza, Engedélyezze a. További *ahol* utasítások ezután felhasználhatók az eseményeket, Ön által keresett szűkítése céljából.

Lekérdezése és szűrése a naplózási adatokat vonatkozó további információkért lásd: [megtekintése vagy elemzése a Log Analytics naplóbeli keresés gyűjtött adatok][analyze-log-analytics].

## <a name="log-event-schema"></a>Eseménysémája

Annak érdekében, hogy a naplóadatok elemzéséhez, az alábbi táblázat ismerteti az egyes események küldésére használatos sémát:

| Mező neve               | Leírás |
|--------------------------|-------------|
| *resourceId*             | Azure-erőforrás, amely a naplófájl előállítása |
| *idő*                   | Időbélyeg, ha a napló feltöltése |
| *kategória*               | A napló létrehozása tároló/összetevő neve |
| *OperationName*          | Mindig *Microsoft.ContainerService/managedClusters/diagnositicLogs/Read* |
| *Properties.log*         | A napló az összetevőtől teljes szövege |
| *Properties.Stream*      | *stderr* vagy *stdout* |
| *Properties.Pod*         | A napló tartalomcsomagokból származó podnév |
| *properties.containerID* | Ez a napló honnan származnak a docker-tároló azonosítója |

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és a naplók a Kubernetes az AKS-fürt fő összetevőinek áttekintése. Figyelheti, és további hibaelhárítást is [Kubelet-naplók megtekintése] [ kubelet-logs] és [engedélyezi az SSH-csomópont elérése][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../log-analytics/log-analytics-tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
