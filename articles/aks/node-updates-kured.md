---
title: Frissítse, és indítsa újra a csomópontokat a kured Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan csomópontok frissítéséhez, és automatikusan kured Azure Kubernetes Service (AKS)-újraindítás
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 75057f6bd92fbdc805da2e0e36dc2bff7b069f26
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243329"
---
# <a name="apply-security-and-kernel-updates-to-nodes-in-azure-kubernetes-service-aks"></a>Biztonsági és a kernel-frissítések alkalmazása csomópontok az Azure Kubernetes Service (AKS)

A fürtök védelme érdekében biztonsági frissítések automatikusan lépnek csomópontok az aks-ben. Ezek a frissítések közé tartozik, az operációs rendszer biztonsági javítások és a kernel-frissítések. Néhány esetben a folyamat befejezéséhez csomópont újraindítása szükséges. Az AKS nem automatikusan indítsa újra a csomópontokat a frissítés befejezéséhez.

Ez a cikk bemutatja, hogyan használhatja a nyílt forráskódú [kured (démon a KUbernetes újraindítás)] [ kured] tekintse meg a csomópontok az újraindítást igénylő, majd automatikusan kezelni újraütemezése futó podok és csomópont-újraindítás a folyamat.

> [!NOTE]
> `Kured` van egy nyílt forráskódú projekt Weaveworks szerint. Az aks-ben Ez a projekt támogatási szolgáltatását az egy képességeinkhez mérten biztosítjuk. További támogatási tekintheti meg a #weave közösségi slack-csatornára,

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.59 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Az AKS csomópont frissítési élmény ismertetése

Az AKS-fürtben a Kubernetes-csomópontokon futtató Azure virtuális gépeken (VM). Ezek a Linux-alapú virtuális gépek egy Ubuntu-rendszerkép használata az operációs rendszer automatikus keresése az frissítések éjszakánként konfigurálva. Ha a biztonsági vagy kernelfrissítés érhetők el, azok automatikusan letölthető és telepíthető.

![AKS-csomópont frissítése, és kured folyamat újraindítása](media/node-updates-kured/node-reboot-process.png)

Egyes biztonsági frissítések, például a kernel-frissítések, a folyamat befejezésére csomópont újraindítása szükséges. Egy csomópont újraindítását igénylő létrehoz egy fájlt */var/run/reboot-required*. Ez a folyamat újraindítás automatikusan nem valósul meg.

Kezeli a csomópont újraindítása, vagy használhatja a saját munkafolyamatokat és folyamatokat `kured` előkészíthető a folyamatot. A `kured`, amely egy [DaemonSet] [ DaemonSet] üzemel podot futtat, amely a fürt egyes csomópontjaihoz. Ezek a DaemonSet podok tekintse meg a létezik-e a */var/run/reboot-required* fájlt, és ezután folyamatot kezdeményez, indítsa újra a csomópontokat.

### <a name="node-upgrades"></a>Csomópont frissítése

Az aks-ben, amely lehetővé teszi, hogy van egy folyamat *frissítése* egy fürtöt. Frissítés az általában áthelyezése a Kubernetes egy újabb verziója, nem csak a csomópont biztonsági frissítések alkalmazásához. Az AKS frissítés a következő műveleteket hajtja végre:

* Új csomópontot a legújabb biztonsági frissítéseket és a alkalmazni Kubernetes-verzió van telepítve.
* Egy régi csomópont van szigetelve és ürítve.
* Az új csomópont podok ütemezése.
* A régi csomópont törlődik.

Ha nem továbbra is ugyanazt a Kubernetes-verziót egy frissítési esemény során. Meg kell adnia a Kubernetes egy újabb verziója. A kubernetes a legújabb verzióra frissítéséhez is [az AKS-fürt frissítése][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Kured az AKS-fürt üzembe helyezése

Üzembe helyezéséhez a `kured` DaemonSet, a következő mintát, a saját GitHub projektoldalon manifest YAML a alkalmazni. A jegyzékfájl hoz létre egy szerepkört és fürtszerepkör, kötéseit és a szolgáltatásfiók, majd üzembe helyezi a DaemonSet használatával `kured` 1.1.0-s verzió, amely támogatja az AKS-fürtök 1.9 vagy újabb.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

A további paramétereket is konfigurálhat `kured`, például az integráció az Prometheus és Slack. További konfigurációs paraméterekkel kapcsolatos további információkért lásd: a [kured telepítési docs][kured-install].

## <a name="update-cluster-nodes"></a>Frissítse a fürtcsomópontokat

Alapértelmezés szerint az AKS-csomópontok minden este frissítések keresése. Ha nem szeretné megvárni, ellenőrizze, hogy a frissítés manuális módszerrel is elvégezheti `kured` megfelelően fut-e. Először kövesse a lépéseket a [az AKS-csomópontok egyikére SSH][aks-ssh]. Miután az SSH-kapcsolatot a csomópontra, keressen frissítéseket, és alkalmazhatja ezeket a következőképpen:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Frissítések alkalmazott egy csomópont újraindítását igénylő, ha egy fájl beíródik */var/run/reboot-required*. `Kured` Alapértelmezés szerint 60 percenként az újraindítást igénylő csomópontok ellenőrzi.

## <a name="monitor-and-review-reboot-process"></a>Figyelő, és nézze újraindítás folyamatban

A DaemonSet a replikára azt észlelte, hogy egy csomópont-újraindítás szükséges, a zárolás a Kubernetes API-n keresztül a csomóponton kerül. A zárolás megakadályozza, hogy újabb podok a csomóponton lett ütemezve. A zárolás azt is jelzi, újra kell indítani, hogy csak egy csomópont, egyszerre. A csomópont szigetelve, a futó podok a csomópontból vannak ürítve, és a csomópont újraindul.

A csomópontok használatával figyelheti a [kubectl get csomópontok] [ kubectl-get-nodes] parancsot. Az alábbi példa kimenetében látható egy csomópont állapottal *SchedulingDisabled* , a csomópont előkészítése az újraindítási folyamatot:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

A frissítési folyamat befejezése után a használt csomópontok állapotát is megtekintheti a [kubectl get csomópontok] [ kubectl-get-nodes] parancsot a `--output wide` paraméter. Ez a kimenet lehetővé teszik az eltérő *-KERNELVERZIÓ* alapul szolgáló csomóponton az alábbi példa kimenetében látható módon. A *aks-nodepool1-28993262-0* megtörtént egy előző lépést, és a kernel verziója látható *4.15.0-1039-azure*. A csomópont *aks-nodepool1-28993262-1* , amely még nem frissített látható kernel verziója *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>További lépések

Ez a cikk részletes használata `kured` csomópontok automatikusan újraindítja a biztonsági frissítés telepítési folyamatának részeként. A kubernetes a legújabb verzióra frissítéséhez is [az AKS-fürt frissítése][aks-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
