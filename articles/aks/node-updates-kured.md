---
title: Linux-csomópont újraindításának kezelése kured
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan frissítheti a Linux-csomópontokat, és hogyan indíthatja újra őket automatikusan az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 8006baa3025ee1e794359bed854094cc9005dd14
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668387"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Biztonsági és kernelfrissítések alkalmazása Linux-csomópontokra az Azure Kubernetes szolgáltatásban (AKS)

A fürtök védelme érdekében a biztonsági frissítések automatikusan alkalmazva vannak az AKS Linux-csomópontjaira. Ezek a frissítések tartalmazzák az operációs rendszer biztonsági javításait vagy a kernel frissítéseit. A folyamat befejezéséhez a frissítések egy része a csomópont újraindítását igényli. Az AKS nem indítja újra automatikusan ezeket a Linux-csomópontokat a frissítési folyamat befejezéséhez.

A Windows Server-csomópontok (jelenleg előzetes verzióban az AKS-ben) naprakészen tartása egy kicsit más. A Windows Server-csomópontok nem kapnak napi frissítéseket. Ehelyett hajtson végre egy AKS-frissítést, amely új csomópontokat telepít a legújabb alap Window Server-lemezképpel és javításokkal. A Windows Server-csomópontokat használó AKS-fürtök esetében olvassa el [AKS csomópontkészlet frissítése.][nodepool-upgrade]

Ez a cikk bemutatja, hogyan használhatja a nyílt forráskódú [kured (KUbernetes REboot démon)][kured] nézni a Linux-csomópontok, amelyek újraindítást igényelnek, majd automatikusan kezeli a futó podok és csomópont újraindítási folyamat átütemezését.

> [!NOTE]
> `Kured`a Weaveworks nyílt forráskódú projektje. A projekt támogatása az AKS-ben a legjobb erőfeszítés alapján történik. További támogatás található a #weave-community Slack csatorna.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="understand-the-aks-node-update-experience"></a>Az AKS-csomópontfrissítési élmény ismertetése

Egy AKS-fürtben a Kubernetes-csomópontok Azure virtuális gépekként (VM-ként) futnak. Ezek a Linux-alapú virtuális gépek egy Ubuntu lemezképet használnak, az operációs rendszer pedig úgy van beállítva, hogy minden este automatikusan ellenőrizze a frissítéseket. Ha rendelkezésre állnak biztonsági vagy kernelfrissítések, a rendszer automatikusan letölti és telepíti őket.

![AKS csomópont frissítésés újraindítás folyamat kured](media/node-updates-kured/node-reboot-process.png)

Egyes biztonsági frissítések, például a kernelfrissítések, a folyamat véglegesítéséhez csomópont-újraindítást igényelnek. Az újraindítást igénylő Linux-csomópont a */var/run/reboot-required*nevű fájlt hoz létre. Ez az újraindítási folyamat nem történik meg automatikusan.

Használhatja a saját munkafolyamatok és folyamatok kezelésére csomópont `kured` újraindítások, vagy a folyamat vezénylése. A `kured`, a [DaemonSet][DaemonSet] van telepítve, amely a fürt minden Linux-csomóponton futtat egy podot. Ezek a podok a DaemonSet figyelik a */var/run/reboot-required* fájl létezését, majd kezdeményeznek egy folyamatot a csomópontok újraindításához.

### <a name="node-upgrades"></a>Csomópontfrissítések

Van egy további folyamat az AKS-ben, amely lehetővé teszi a fürt *frissítését.* A frissítés általában a Kubernetes újabb verziójára való áttérés, nem csak a csomópont biztonsági frissítéseinek alkalmazása. Az AKS-frissítés a következő műveleteket hajtja végre:

* Egy új csomópont van telepítve a legújabb biztonsági frissítésekkel és a Kubernetes verziójával.
* Egy régi csomópont kordonnal van elkerítve és lecsapolva.
* Podok vannak ütemezve az új csomóponton.
* A régi csomópont törlődik.

Nem maradhat ugyanazon a Kubernetes-verzión egy frissítési esemény során. Meg kell adnia a Kubernetes újabb verzióját. A Kubernetes legújabb verziójára való frissítéshez [frissítheti az AKS-fürtöt.][aks-upgrade]

## <a name="deploy-kured-in-an-aks-cluster"></a>AKS-fürtben telepített telepítés

A `kured` DaemonSet telepítéséhez telepítse a következő hivatalos Kured Helm diagramot. Ez létrehoz egy szerepkört és fürtszerepkört, kötéseket és egy szolgáltatásfiókot, majd telepíti a DaemonSet-et a használatával. `kured`

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

További paramétereket is konfigurálhat a számára, például a Prometheus vagy a `kured`Tartaléklasztó integrálását. További információ a további konfigurációs paraméterekről a [kured Helm diagramban található.][kured-install]

## <a name="update-cluster-nodes"></a>Fürtcsomópontok frissítése

Alapértelmezés szerint az AKS Linux-csomópontjai minden este frissítéseket keresnek. Ha nem szeretne várni, manuálisan is végrehajthat egy `kured` frissítést a megfelelő enfutás ellenőrzéséhez. Először kövesse az SSH lépéseket [az Egyik AKS-csomóponthoz][aks-ssh]. Ha már van SSH-kapcsolata a Linux-csomódhoz, ellenőrizze a frissítéseket, és alkalmazza őket az alábbiak szerint:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Ha olyan frissítéseket alkalmaznak, amelyek hez csomópont újraindítása szükséges, a rendszer a */var/run/reboot-required kapcsolóba*írja a fájlt. `Kured`alapértelmezés szerint 60 percenként újraindítást igénylő csomópontokat keres.

## <a name="monitor-and-review-reboot-process"></a>Újraindítási folyamat figyelése és áttekintése

Ha a DaemonSet egyik replikája azt észlelte, hogy egy csomópont újraindítása szükséges, a Kubernetes API-n keresztül a csomópontra zárolás kerül. Ez a zárolás megakadályozza, hogy további podok vannak ütemezve a csomóponton. A zárolás azt is jelzi, hogy egyszerre csak egy csomópontot kell újraindítani. A csomópont kordonnal van elkerítve, a futó podok kiürítése a csomópontról, és a csomópont újraindul.

A [kubectl get csomópontok][kubectl-get-nodes] paranccsal figyelheti a csomópontok állapotát. A következő példakimenetegy olyan csomópontot jelenít *meg, amelynek állapota SchedulingDisabled,* miközben a csomópont az újraindítási folyamatra készül:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Miután a frissítési folyamat befejeződött, megtekintheti a csomópontok állapotát a [kubectl][kubectl-get-nodes] get `--output wide` csomópontok parancs a paraméterrel. Ez a további kimenet lehetővé teszi, hogy az alapul szolgáló csomópontok *KERNEL-VERSION* verziójának különbségét láthassa, ahogy az a következő példa kimenetben látható. Az *aks-nodepool1-28993262-0* egy előző lépésben frissült, és a *4.15.0-1039-azure*kernel verziót jeleníti meg. Az *aks-nodepool1-28993262-1* csomópont, amely nem lett frissítve, a *kernel 4.15.0-1037-azure verzióját*jeleníti meg.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>További lépések

Ez a cikk `kured` részletesen ismerteti, hogyan kell használni a Linux-csomópontok automatikus újraindítása a biztonsági frissítési folyamat részeként. A Kubernetes legújabb verziójára való frissítéshez [frissítheti az AKS-fürtöt.][aks-upgrade]

A Windows Server-csomópontokat használó AKS-fürtök esetében olvassa el [AKS csomópontkészlet frissítése.][nodepool-upgrade]

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
