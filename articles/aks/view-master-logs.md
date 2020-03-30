---
title: Az Azure Kubernetes-szolgáltatás (AKS) vezérlőnaplóinak megtekintése
description: Ismerje meg, hogyan engedélyezheti és tekintheti meg a Kubernetes főcsomópontnaplóit az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259382"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>A Kubernetes főcsomópont-naplóinak engedélyezése és áttekintése az Azure Kubernetes Service-ben (AKS)

Az Azure Kubernetes Service (AKS) segítségével a fő összetevők, például a *kube-apiserver* és a *kube-controller-manager* felügyelt szolgáltatásként vannak biztosítva. Hozza létre és kezelje a *kubelet* és a tároló futásidejű csomópontokat, és telepítse az alkalmazásokat a felügyelt Kubernetes API-kiszolgálón keresztül. Az alkalmazás és a szolgáltatások hibaelhárításához előfordulhat, hogy meg kell tekintenie a fő összetevők által létrehozott naplókat. Ez a cikk bemutatja, hogyan használhatja az Azure Monitor naplók a Kubernetes fő összetevőiből származó naplók engedélyezéséhez és lekérdezéséhez.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk egy meglévő AKS-fürt azure-fiókban futó. Ha még nem rendelkezik AKS-fürttel, hozzon létre egyet az [Azure CLI][cli-quickstart] vagy az [Azure Portal][portal-quickstart]használatával. Az Azure Monitor-naplók rbac és nem RBAC-kompatibilis AKS-fürtökkel is működik.

## <a name="enable-diagnostics-logs"></a>Diagnosztikai naplók engedélyezése

Több forrásból származó adatok gyűjtése és áttekintése érdekében az Azure Monitor naplói lekérdezési nyelvet és elemzési motort biztosít, amely betekintést nyújt a környezetbe. A munkaterület az adatok szétválogatására és elemzésére szolgál, és integrálható más Azure-szolgáltatásokkal, például az Application Insights és a Security Center használatával. Egy másik platform használatával a naplók elemzéséhez, ehelyett dönthet úgy, hogy diagnosztikai naplókküldése egy Azure storage-fiók vagy eseményközpont. További információ: [Mi az Azure Monitor naplók?][log-analytics-overview].

Az Azure Monitor-naplók engedélyezve vannak és kezelhetők az Azure Portalon. Ha engedélyezni szeretné a Kubernetes főösszetevőinek naplógyűjteményét az AKS-fürtben, nyissa meg az Azure-portált egy webböngészőben, és hajtsa végre az alábbi lépéseket:

1. Válassza ki az AKS-fürt erőforráscsoportját, például a *myResourceGroup*csoportot. Ne válassza ki az egyes AKS-fürterőforrásokat tartalmazó erőforráscsoportot, például *a MC_myResourceGroup_myAKSCluster_eastus.*
1. A bal oldalon válassza a **Diagnosztikai beállítások lehetőséget.**
1. Válassza ki az AKS-fürtöt, például *a myAKSCluster*t, majd válassza **a Diagnosztikai beállítás hozzáadása lehetőséget.**
1. Írjon be egy nevet, például *a myAKSClusterLogs*nevet, majd válassza a **Küldés a Log Analytics szolgáltatásba**lehetőséget.
1. Jelöljön ki egy meglévő munkaterületet, vagy hozzon létre egy újat. Ha munkaterületet hoz létre, adjon meg egy munkaterület nevét, egy erőforráscsoportot és egy helyet.
1. Az elérhető naplók listájában válassza ki az engedélyezni kívánt naplókat. A gyakori naplók közé tartozik a *kube-apiserver*, *kube-controller-manager*és *kube-scheduler*. További naplókat is engedélyezhet, például *a kube-auditot* és *a fürt-automatikus skálázót.* A Log Analytics-munkaterületek engedélyezése után visszaküldheti és módosíthatja az összegyűjtött naplókat.
1. Ha készen áll, válassza a **Mentés** lehetőséget a kijelölt naplók gyűjtésének engedélyezéséhez.

A következő példaportál képernyőképe a *Diagnosztikai beállítások ablakot* mutatja:

![Log Analytics-munkaterület engedélyezése az AKS-fürt Azure Monitor-naplóihoz](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Tesztegység ütemezése az AKS-fürtön

Naplók létrehozásához hozzon létre egy új podot az AKS-fürtben. A következő példa YAML jegyzékfájl segítségével hozzon létre egy alapvető NGINX-példány. Hozzon létre `nginx.yaml` egy megnevezett fájlt egy ön által választott szerkesztőben, és illessze be a következő tartalmat:

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

Hozza létre a podot a [kubectl create][kubectl-create] paranccsal, és adja meg a YAML-fájlt, ahogy az a következő példában látható:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Összegyűjtött naplók megtekintése

A diagnosztikai naplók engedélyezése és a Log Analytics-munkaterületen való megjelenésnéhány percet is igénybe vehet. Az Azure Portalon válassza ki a Log Analytics-munkaterület erőforráscsoportját, például *a myResourceGroupot,* majd válassza ki a naplóelemzési erőforrást, például *a myAKSLogs erőforrást.*

![Válassza ki az AKS-fürt Log Analytics-munkaterületét](media/view-master-logs/select-log-analytics-workspace.png)

A bal oldalon válassza a **Naplók**lehetőséget. A *kube-apiserver*megtekintéséhez írja be a következő lekérdezést a szövegmezőbe:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Valószínűleg sok naplót ad vissza az API-kiszolgáló. Ha az előző lépésben létrehozott NGINX-podról szóló naplók megtekintéséhez le szeretné hatókört a lekérdezéshatókörben, adjon hozzá egy további *where* utasítást a *podok/nginx* kereséséhez, ahogy az a következő példalekérdezésben látható:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Az NGINX-pod ra vonatkozó konkrét naplók jelennek meg, amint az a következő példa képernyőképen látható:

![Az NGINX-pod minta lekérdezési eredményei](media/view-master-logs/log-analytics-query-results.png)

További naplók megtekintéséhez frissítheti a *lekérdezést* a kategória neve *kube-controller-manager* vagy *kube-scheduler*, attól függően, hogy milyen további naplók engedélyezése. További, *ahol* a kimutatások segítségével finomíthatja a keresett eseményeket.

A naplóadatok lekérdezéséről és szűréséről a [Naplóelemzési naplókereséssel gyűjtött adatok megtekintése vagy elemzése című][analyze-log-analytics]témakörben talál további információt.

## <a name="log-event-schema"></a>Eseményséma naplózása

A naplóadatok elemzéséhez az alábbi táblázat részletezi az egyes eseményekhez használt sémát:

| Mező neve               | Leírás |
|--------------------------|-------------|
| *resourceId*             | A naplót eredményező Azure-erőforrás |
| *Idő*                   | A napló feltöltési idejének időbélyegzője |
| *Kategória*               | A naplót létrehozó tároló/összetevő neve |
| *operationName*          | Mindig *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | A napló teljes szövege az összetevőből |
| *properties.stream*      | *stderr* vagy *stdout* |
| *properties.pod*         | Pod név, amelyből a napló származik |
| *properties.containerID* | A napló ból származó docker-tároló azonosítója |

## <a name="log-roles"></a>Naplószerepek

| Szerepkör                     | Leírás |
|--------------------------|-------------|
| *aksService*             | A vezérlősík műveletének naplójában a megjelenítendő név (a hcpService szolgáltatásból) |
| *masterclient (főügyfél)*           | A MasterClientCertificate naplójának megjelenítendő neve, az az aks get-credentials tanúsítványa |
| *csomópont-ügyfél*             | Az ügyintézői csomópontok által használt ClientCertificate megjelenítendő neve |

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és tekintheti át az AKS-fürt Kubernetes fő összetevőinek naplóit. A további figyelés és hibaelhárítás érdekében [megtekintheti a Kubelet naplókat][kubelet-logs] is, és engedélyezheti az [SSH-csomóponthoz való hozzáférést.][aks-ssh]

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
