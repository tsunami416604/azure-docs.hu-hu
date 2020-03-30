---
title: Rendelkezésre állási zónák használata az Azure Kubernetes szolgáltatásban (AKS)
description: Megtudhatja, hogyan hozhat létre olyan fürtöt, amely csomópontokat oszt el az Azure Kubernetes-szolgáltatás (AKS) rendelkezésre állási zónái között.
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596810"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Azure Kubernetes-szolgáltatás (AKS) fürt létrehozása, amely rendelkezésre állási zónákat használ

Az Azure Kubernetes-szolgáltatás (AKS) fürt elosztja az erőforrásokat, például a csomópontokat és a tárolást az alapul szolgáló Azure számítási infrastruktúra logikai szakaszai között. Ez a telepítési modell gondoskodik arról, hogy a csomópontok külön frissítési és tartalék tartományokon fussanak egyetlen Azure-adatközpontban. Az ezzel az alapértelmezett viselkedéssel telepített AKS-fürtök magas rendelkezésre állást biztosítanak a hardverhiba vagy a tervezett karbantartási esemény elleni védelemhez.

Az alkalmazások magasabb szintű rendelkezésre állásának biztosítása érdekében az AKS-fürtök a rendelkezésre állási zónák között is eloszthatók. Ezek a zónák fizikailag különálló adatközpontok egy adott régióban. Ha a fürtösszetevők több zónában vannak elosztva, az AKS-fürt képes elviselni az egyik ilyen zónában besikerült hibákat. Az alkalmazások és a felügyeleti műveletek továbbra is elérhetők, még akkor is, ha egy teljes adatközpont problémával rendelkezik.

Ez a cikk bemutatja, hogyan hozhat létre egy AKS-fürtöt, és hogyan oszthatja el a csomópont-összetevőket a rendelkezésre állási zónák között.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.76-os vagy újabb verziójára van szükség telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="limitations-and-region-availability"></a>Korlátozások és a régió elérhetősége

Az AKS-fürtök jelenleg a következő régiók ban rendelkezésre állási zónák használatával hozhatók létre:

* USA középső régiója
* USA 2. keleti régiója
* USA keleti régiója
* Közép-Franciaország
* Kelet-Japán
* Észak-Európa
* Délkelet-Ázsia
* Az Egyesült Királyság déli régiója
* Nyugat-Európa
* USA nyugati régiója, 2.

A következő korlátozások érvényesek, ha rendelkezésre állási zónák használatával hoz létre AKS-fürtöt:

* A rendelkezésre állási zónák csak a fürt létrehozásakor engedélyezhetők.
* A fürt létrehozása után a rendelkezésre állási zóna beállításai nem frissíthetők. Egy meglévő, nem rendelkezésre álló zónafürt nem frissíthető a rendelkezésre állási zónák használatához.
* Az AKS-fürt rendelkezésre állási zónái nem tilthatók le létrehozásuk után.
* A kiválasztott csomópontméretnek (VM Termékváltozat) minden rendelkezésre állási zónában elérhetőnek kell lennie.
* A rendelkezésre állási zónákkal rendelkező fürtök az Azure Standard Load Balancers használatát igénylik a zónák közötti elosztáshoz.
* A Standard Load Balancers üzembe helyezéséhez a Kubernetes 1.13.5-ös vagy újabb verzióját kell használnia.

AKS-fürtök, amelyek a rendelkezésre állási zónák at kell használnia az Azure load balancer *szabványos* Termékváltozat, amely az alapértelmezett érték a terheléselosztó típus. Ez a terheléselosztó típus csak fürtlétrehozási időpontban adható meg. További információkért és a standard terheléselosztó korlátaiért tekintse meg az [Azure terheléselosztó szabványos termékváltozatának korlátait.][standard-lb-limitations]

### <a name="azure-disks-limitations"></a>Az Azure-lemezek korlátai

Az Azure felügyelt lemezeket használó kötetek jelenleg nem zónaszintű erőforrások. Az eredeti zónától eltérő zónába átütemezett podok nem tudják újra csatolni az előző lemez(eke)t. Ajánlott állapot nélküli számítási feladatok futtatásához, amelyek nem igényelnek állandó tárolási, amely előfordulhat, hogy a zónaszintű problémák.

Ha állapotalapú számítási feladatokfuttatásához, használja a pod specifikációiban a taints és a tűrések használatával, hogy a Kubernetes-ütemező a lemezekhez tartozó podok létrehozása ugyanabban a zónában. Másik lehetőségként használjon hálózati alapú tárolót, például az Azure Files, amely a zónák közötti ütemezés során a podokhoz csatolható.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS-fürtök rendelkezésre állási zónáinak áttekintése

A rendelkezésre állási zónák egy magas rendelkezésre állású ajánlat, amely megvédi az alkalmazásokat és az adatokat az adatközponti hibáktól. A zónák egy Azure-régión belül egyedi fizikai helyek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében legalább három különálló zóna van az összes engedélyezett régióban. A rendelkezésreállási zónák régión belüli fizikai elkülönítése védelmet biztosít az alkalmazások és az adatok számára az adatközpont meghibásodása esetén. A zónaredundáns szolgáltatások replikálják az alkalmazásokat és az adatokat a rendelkezésre állási zónák között, hogy megvédjék az egypontosokat.

További információ: [Mik a rendelkezésre állási zónák az Azure-ban?][az-overview].

AKS-fürtök, amelyek a rendelkezésre állási zónák használatával telepített csomópontok több zónában egy régión belül. Az *USA keleti régiójában 2* régióban lévő fürt például az *USA keleti régiójában 2 csomópontot*hozhat létre mindhárom rendelkezésre állási zónában. Az AKS-fürt erőforrások ezen elosztása javítja a fürt rendelkezésre állását, mivel egy adott zóna meghibásodásával szemben ellenállóak.

![AKS-csomópont elosztása a rendelkezésre állási zónák között](media/availability-zones/aks-availability-zones.png)

Zónakimaradás esetén a csomópontok manuálisan vagy a fürt automatikus skálázójával kiegyensúlyozhatók. Ha egyetlen zóna elérhetetlenné válik, az alkalmazások továbbra is futnak.

## <a name="create-an-aks-cluster-across-availability-zones"></a>AKS-fürt létrehozása a rendelkezésre állási zónák között

Amikor az [aks create][az-aks-create] paranccsal hoz `--zones` létre fürtöt, a paraméter határozza meg, hogy mely zónákba telepíti a rendszer az ügynökcsomópontokat. A fürt AKS vezérlősík-összetevői is zónák között vannak elosztva `--zones` a legmagasabb rendelkezésre álló konfigurációban, amikor a paramétert a fürt létrehozásakor definiálja.

Ha a KS-fürt létrehozásakor nem határoz meg zónákat az alapértelmezett ügynökkészlethez, a fürt AKS vezérlősík-összetevői nem fognak rendelkezésre állási zónákat használni. További csomópontkészleteket adhat hozzá az [aks nodepool][az-aks-nodepool-add] add `--zones` paranccsal, és megadhatja az új csomópontokat, azonban a vezérlősík összetevői a rendelkezésre állási zóna ismertsége nélkül maradnak. A csomópontkészlet vagy az AKS vezérlősík-összetevők zónaismertségét nem módosíthatja üzembe helyezésük után.

A következő példa létrehoz egy *myResourceGroup*nevű AKS-fürtöt, amely *nek neve myAKSCluster.* Összesen *3* csomópont jön létre - egy ügynök az *1*zónában , egy a *2-ből*, majd egy a *3-ból*. Az AKS vezérlősík-összetevők is a legmagasabb rendelkezésre álló konfigurációban vannak elosztva a zónák között, mivel a fürt létrehozási folyamatának részeként vannak definiálva.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Az AKS-fürt létrehozása néhány percet vesz igénybe.

## <a name="verify-node-distribution-across-zones"></a>A csomópontok zónák közötti eloszlásának ellenőrzése

Amikor a fürt készen áll, sorolja fel az ügynök csomópontokat a méretezési csoportban, hogy milyen rendelkezésre állási zónában vannak telepítve.

Először az AKS-fürt hitelesítő adatait az [az aks get-credentials][az-aks-get-credentials] paranccsal kapja meg:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Ezután használja a [kubectl leírás parancsot][kubectl-describe] a fürt csomópontjainak listázásához. Szűrje a *failure-domain.beta.kubernetes.io/zone* értéket a következő példában látható módon:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

A következő példa kimeneti mutatja a három csomópont elosztva a megadott régióban és a rendelkezésre állási zónák, például *eastus2-1* az első rendelkezésre állási zóna és *eastus2-2* a második rendelkezésre állási zóna:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

További csomópontok hozzáadásakor az ügynökkészlet, az Azure platform automatikusan elosztja az alapul szolgáló virtuális gépek et a megadott rendelkezésre állási zónák között.

Ne feledje, hogy az újabb Kubernetes verziókban (1.17.0 `topology.kubernetes.io/zone` és újabb) az AKS az elavult on kívül az újabb címkét is `failure-domain.beta.kubernetes.io/zone`használja.

## <a name="verify-pod-distribution-across-zones"></a>A podok zónák közötti eloszlásának ellenőrzése

A jól ismert címkék, a jegyzetek és a [taints][kubectl-well_known_labels]dokumentálva a `failure-domain.beta.kubernetes.io/zone` Kubernetes a címkét használja a podok automatikus elosztására a replikációs vezérlőben vagy szolgáltatásban a különböző elérhető zónák között. Ennek teszteléséhez 3-ról 5 csomópontra skálázhatja a fürtöt, hogy ellenőrizze a pod megfelelő terjedését:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Ha a méretezési művelet néhány perc `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` elteltével befejeződik, a parancsnak a mintához hasonló kimenetet kell adnia:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Amint láthatja, most már két további csomópontunk van az 1. Három kópiából álló alkalmazást telepíthet. Az NGINX-et fogjuk példaként használni:

```console
kubectl run nginx --image=nginx --replicas=3
```

Ha ellenőrzi, hogy a podok, ahol a podok futnak, látni fogja, hogy a podok futnak a podok három különböző rendelkezésre állási zónák nak megfelelő. Például a `kubectl describe pod | grep -e "^Name:" -e "^Node:"` parancs akkor kap egy kimenet hasonló ehhez:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Mint látható az előző kimenet, az első pod fut csomópont 0, amely `eastus2-1`a rendelkezésre állási zónában található. A második pod fut a 2-es `eastus2-3`csomóponton, amely megfelel a , `eastus2-2`és a harmadik a 4-es csomópontban, a alkalmazásban. További konfiguráció nélkül a Kubernetes a podok egyenletesen eloszlik mindhárom rendelkezésre állási zónában.

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen ismerteti, hogyan hozhat létre egy AKS-fürt, amely a rendelkezésre állási zónákat használja. A magas rendelkezésre állású fürtökre vonatkozó további szempontokért olvassa el [az Üzleti folytonossággal és a vészhelyreállítással kapcsolatos gyakorlati tanácsok az AKS-ben című témakört.][best-practices-bc-dr]

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
