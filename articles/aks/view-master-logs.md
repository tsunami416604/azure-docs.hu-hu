---
title: Az Azure Kubernetes szolgáltatás (ak) vezérlő naplófájljainak megtekintése
description: Megtudhatja, hogyan engedélyezheti és tekintheti meg a Kubernetes fő csomópontjának naplóit az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: 504d6a5216f3345f22a601c4ae084488aeb97c8d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128959"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>A Kubernetes főcsomópont-naplóinak engedélyezése és áttekintése az Azure Kubernetes Service-ben (AKS)

Az Azure Kubernetes szolgáltatással (ak) a fő összetevők, például a *Kube-apiserver* és a *Kube-Controller-Manager* felügyelt szolgáltatásként vannak megadva. Létrehozhatja és kezelheti a *kubelet* és a tároló futtatókörnyezetét futtató csomópontokat, és az alkalmazásokat a felügyelt Kubernetes API-kiszolgálón keresztül helyezheti üzembe. Az alkalmazások és szolgáltatások hibakereséséhez előfordulhat, hogy meg kell tekintenie a fő összetevők által létrehozott naplókat. Ez a cikk bemutatja, hogyan engedélyezheti és kérdezheti le a naplókat a Kubernetes fő összetevőiről Azure Monitor naplók használatával.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez egy Azure-fiókban futó meglévő AK-fürtre van szükség. Ha még nem rendelkezik AK-fürttel, hozzon létre egyet az [Azure CLI][cli-quickstart] vagy a [Azure Portal][portal-quickstart]használatával. Azure Monitor naplók a RBAC és a nem RBAC-kompatibilis AK-fürtökkel is működnek.

## <a name="enable-resource-logs"></a>Erőforrás-naplók engedélyezése

A több forrásból származó adatok gyűjtésének és áttekintésének megkönnyítéséhez Azure Monitor-naplók egy lekérdezési nyelvet és elemzési motort biztosítanak, amely elemzéseket biztosít a környezet számára. A munkaterületek az adatválogatásra és az adatelemzésre használhatók, és integrálható más Azure-szolgáltatásokkal, például Application Insightsekkel és Security Centerokkal. Ha egy másik platformot szeretne használni a naplók elemzéséhez, dönthet úgy, hogy erőforrás-naplókat küld egy Azure Storage-fiókba vagy az Event hub-ba. További információ: [Mi az a Azure monitor logs?][log-analytics-overview].

Azure Monitor naplók engedélyezve vannak és kezelhetők a Azure Portal. Ha az AK-fürt Kubernetes fő összetevőinek naplózási gyűjteményét szeretné engedélyezni, nyissa meg a Azure Portal egy böngészőben, és végezze el a következő lépéseket:

1. Válassza ki az AK-fürthöz tartozó erőforráscsoportot, például *myResourceGroup*. Ne válassza ki azt az erőforráscsoportot, amely az egyedi AK-fürterőforrás (például *MC_myResourceGroup_myAKSCluster_eastus*) tartalmazza.
1. A bal oldali oldalon válassza a **diagnosztikai beállítások**lehetőséget.
1. Válassza ki az AK-fürtöt, például *myAKSCluster*, majd válassza a **diagnosztikai beállítás hozzáadását**.
1. Adjon meg egy nevet, például *myAKSClusterLogs*, majd válassza ki a **log Analytics küldésére szolgáló**lehetőséget.
1. Válasszon ki egy meglévő munkaterületet, vagy hozzon létre egy újat. Ha létrehoz egy munkaterületet, adja meg a munkaterület nevét, egy erőforráscsoportot és egy helyet.
1. Az elérhető naplók listájában válassza ki az engedélyezni kívánt naplókat. A gyakori naplók közé tartozik a *Kube-apiserver*, a *Kube-Controller-Manager*és a *Kube-Scheduler*. Engedélyezheti a további naplókat, például a *Kube-audit* és a *cluster-autoskálázást*. Ha Log Analytics munkaterületek engedélyezve vannak, visszaállíthatja és módosíthatja az összegyűjtött naplókat.
1. Ha elkészült, válassza a **Mentés** lehetőséget a kiválasztott naplók gyűjtésének engedélyezéséhez.

A következő példa-portálon a *diagnosztikai beállítások* ablak látható:

![Log Analytics munkaterület engedélyezése az AK-fürt Azure Monitor naplóihoz](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Tesztelési Pod-t ütemezhet az AK-fürtön

A naplók létrehozásához hozzon létre egy új Pod-t az AK-fürtben. Az alábbi példa YAML-jegyzék használatával hozhat létre alapszintű NGINX-példányt. Hozzon létre egy `nginx.yaml` nevű fájlt egy tetszőleges szerkesztőben, és illessze be az alábbi tartalmat:

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

Hozza létre a pod-t a [kubectl Create][kubectl-create] paranccsal, és adja meg a YAML-fájlt az alábbi példában látható módon:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Összegyűjtött naplók megtekintése

A diagnosztikai naplók engedélyezéséhez és a Log Analytics munkaterületen való megjelenítéséhez néhány percet is igénybe vehet. A Azure Portal válassza ki a Log Analytics munkaterülethez tartozó erőforráscsoportot, például *myResourceGroup*, majd válassza ki a log Analytics-erőforrást, például *myAKSLogs*.

![Válassza ki az Log Analytics munkaterületet az AK-fürthöz](media/view-master-logs/select-log-analytics-workspace.png)

A bal oldali oldalon válassza a **naplók**lehetőséget. A *Kube-apiserver*megtekintéséhez írja be a következő lekérdezést a szövegmezőbe:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Az API-kiszolgáló valószínűleg sok naplót ad vissza. Ha le szeretné tekinteni a lekérdezés hatókörét az előző lépésben létrehozott NGINX Pod-naplók megtekintéséhez, adjon hozzá egy további *Where* utasítást a *hüvelyek/NGINX* kereséséhez az alábbi példában látható módon:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Az NGINX Pod-hoz tartozó naplók megjelennek az alábbi képernyőképen látható módon:

![A log Analytics lekérdezési eredményei a minta NGINX Pod-hoz](media/view-master-logs/log-analytics-query-results.png)

További naplók megtekintéséhez frissítse a kategória nevét a *Kube-Controller-Manager* vagy a *Kube-Scheduler*értékre, attól függően, hogy az Ön által megadott további naplókra milyen további beállítások *tartoznak* . További *Where* utasítások használatával pontosíthatja a keresett eseményeket.

A naplózási adatok lekérdezésével és szűrésével kapcsolatos további információkért lásd a [log Analytics-naplóbeli kereséssel gyűjtött adatok megtekintése és elemzése][analyze-log-analytics]című témakört.

## <a name="log-event-schema"></a>Napló esemény sémája

A naplózási adatok elemzéséhez a következő táblázat részletezi az egyes eseményekhez használt sémát:

| Mező neve               | Leírás |
|--------------------------|-------------|
| *resourceId*             | A naplót előkészítő Azure-erőforrás |
| *idő*                   | A napló feltöltésének időbélyege |
| *Kategória*               | A naplót létrehozó tároló/összetevő neve |
| *operationName*          | Mindig *Microsoft. tárolószolgáltatás/managedClusters/diagnosticLogs/Read* |
| *Properties. log*         | Az összetevőből származó napló teljes szövege |
| *Properties. stream*      | *stderr* vagy *StdOut* |
| *Properties. Pod*         | Az a pod-név, amelyből a napló származik |
| *Properties. containerID* | A naplóból érkezett Docker-tároló azonosítója |

## <a name="log-roles"></a>Naplók szerepkörei

| Szerepkör                     | Leírás |
|--------------------------|-------------|
| *aksService*             | A vezérlő síkja művelethez tartozó megjelenített név a naplóban (a hcpService) |
| *MasterClient*           | A MasterClientCertificate naplójának megjelenített neve, az az AK Get-hitelesítő adatokkal rendelkező tanúsítvány |
| *nodeclient*             | Az ügynök csomópontjai által használt ClientCertificate megjelenített neve |

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és tekintheti át a Kubernetes fő összetevőinek naplóit az AK-fürtben. A további figyeléshez és a hibák megoldásához tekintse meg [a Kubelet-naplókat][kubelet-logs] , és [engedélyezze az SSH-csomópontok elérését][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
