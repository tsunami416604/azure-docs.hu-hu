---
title: Rendelkezésre állási zónák használata az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan hozhat létre olyan fürtöt, amely a csomópontokat a rendelkezésre állási zónák között osztja el az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77596810"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Rendelkezésre állási zónákat használó Azure Kubernetes-szolgáltatásbeli (ak-) fürt létrehozása

Az Azure Kubernetes Service (ak)-fürt erőforrásokat, például a csomópontokat és a tárolókat a mögöttes Azure számítási infrastruktúra logikai fejezetei között osztja el. Ez a telepítési modell gondoskodik arról, hogy a csomópontok külön frissítési és tartalék tartományokon futnak egyetlen Azure-adatközpontban. Az ezzel az alapértelmezett viselkedéssel üzembe helyezett AK-fürtök magas szintű rendelkezésre állást biztosítanak a hardverhiba vagy tervezett karbantartási események elleni védelemhez.

Az alkalmazások magasabb szintű rendelkezésre állásának biztosítása érdekében az AK-fürtök a rendelkezésre állási zónák között terjeszthetők. Ezek a zónák fizikailag különálló adatközpontok az adott régión belül. Ha a fürt összetevői több zónában vannak elosztva, az AK-fürt képes elviselni az egyik zónában fellépő hibát. Az alkalmazások és a felügyeleti műveletek továbbra is elérhetők maradnak, még akkor is, ha az egyik teljes adatközpontnál probléma van.

Ez a cikk bemutatja, hogyan hozhat létre egy AK-fürtöt, és hogyan oszthatja szét a csomópont-összetevőket a rendelkezésre állási zónák között.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.76 vagy újabb verziójára, és konfigurálva van. A `az --version` verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="limitations-and-region-availability"></a>Korlátozások és régiók rendelkezésre állása

Az AK-fürtök jelenleg rendelkezésre állási zónák használatával hozhatók létre a következő régiókban:

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

A következő korlátozások érvényesek az AK-fürtök rendelkezésre állási zónák használatával történő létrehozásakor:

* A rendelkezésre állási zónák csak a fürt létrehozásakor engedélyezhetők.
* A rendelkezésre állási zóna beállításai nem frissíthetők a fürt létrehozása után. A rendelkezésre állási zónák használatához nem lehet frissíteni egy meglévő, nem rendelkezésre állási zóna fürtöt is.
* A rendelkezésre állási zónák nem tilthatók le egy AK-fürthöz a létrehozása után.
* A kiválasztott csomópont-méretnek (VM SKU) elérhetőnek kell lennie az összes rendelkezésre állási zónában.
* A rendelkezésre állási zónákat engedélyező fürtök esetében az Azure standard Load Balancer használata szükséges a zónák közötti elosztáshoz.
* Standard Load Balancer üzembe helyezéséhez a Kubernetes 1.13.5 vagy újabb verzióját kell használnia.

A rendelkezésre állási zónákat használó AK-fürtöknek az Azure Load Balancer *standard* SKU-t kell használniuk, amely a terheléselosztó típusának alapértelmezett értéke. Ezt a terheléselosztó-típust csak a fürt létrehozási idején lehet definiálni. További információ és a standard Load Balancer korlátai: az [Azure Load Balancer standard SKU-korlátai][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Azure-lemezek korlátozásai

Az Azure Managed Disks-t használó kötetek jelenleg nem a zónákhoz tartozó erőforrások. Az eredeti zónától eltérő zónában átütemezett hüvelyek nem csatolhatják újra az előző lemez (eke) t. Azt javasoljuk, hogy olyan állapot nélküli munkaterheléseket futtasson, amelyek nem igényelnek olyan állandó tárterületet, amely többek között a zónákra kiterjedő problémák esetén

Ha állapot-nyilvántartó munkaterhelést kell futtatnia, a saját Pod specifikációjában a szennyező adatok és a tolerálás használatával mondja el, hogy a Kubernetes Scheduler a lemezekkel megegyező zónában hozza létre a hüvelyeket. Azt is megteheti, hogy olyan hálózati tárterületet használ, mint például a Azure Files, amelyek a zónák közötti ütemezés szerint csatolhatók a hüvelyekhez.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Az AK-fürtök rendelkezésre állási zónáinak áttekintése

A rendelkezésre állási zónák egy magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. A zónák egy Azure-régióban található egyedi fizikai helyszínek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosításához legalább három különálló zónának kell lennie az összes engedélyezett régióban. A rendelkezésreállási zónák régión belüli fizikai elkülönítése védelmet biztosít az alkalmazások és az adatok számára az adatközpont meghibásodása esetén. A zóna-redundáns szolgáltatások a rendelkezésre állási zónák között replikálják az alkalmazásokat és az adataikat, így biztosítva az egypontos meghibásodás elleni védekezést.

További információ: [Mik a rendelkezésre állási zónák az Azure-ban?][az-overview].

A rendelkezésre állási zónák használatával üzembe helyezett AK-fürtök több, egyetlen régióban található zónában terjeszthetik a csomópontokat. Az *USA 2* . keleti régiójában található fürt például az *USA 2. keleti*régiójában mindhárom rendelkezésre állási zónában hozhat létre csomópontokat. Az AK-beli fürt erőforrásainak ezen eloszlása javítja a fürt rendelkezésre állását, mivel azok egy adott zóna meghibásodása esetén rugalmasak.

![AK-csomópont eloszlása a rendelkezésre állási zónák között](media/availability-zones/aks-availability-zones.png)

A zónák meghibásodása esetén a csomópontok manuálisan vagy a fürt automéretező használatával is kiegyensúlyozható. Ha egyetlen zóna elérhetetlenné válik, az alkalmazások továbbra is futnak.

## <a name="create-an-aks-cluster-across-availability-zones"></a>AK-fürt létrehozása rendelkezésre állási zónák között

Amikor az az [AK Create][az-aks-create] paranccsal hoz létre fürtöt, a paraméter `--zones` határozza meg, hogy a rendszer mely zónákat telepíti a-ben. A fürthöz tartozó AK vezérlőelem-sík összetevői a legmagasabb rendelkezésre állású konfigurációban is elterjednek, ha `--zones` a paramétert a fürt létrehozási ideje határozza meg.

Ha nem ad meg zónát az alapértelmezett ügynök készletéhez, amikor egy AK-fürtöt hoz létre, akkor a fürthöz tartozó AK vezérlőelem-sík összetevői nem fogják használni a rendelkezésre állási zónákat. Hozzáadhat további csomópont-készleteket az [az AK nodepool Add][az-aks-nodepool-add] paranccsal, és megadhatja `--zones` ezeket az új csomópontokat, azonban a vezérlési sík összetevői megmaradnak a rendelkezésre állási zóna ismerete nélkül. Az üzembe helyezésük után nem módosítható a zóna, illetve a csomópont-vagy az AK-vezérlő sík összetevőinek ismerete.

A következő példában létrehozunk egy *myAKSCluster* nevű AK-fürtöt az *myResourceGroup*nevű erőforráscsoport-csoportban. Összesen *3* csomópont jön létre – egy ügynök az *1*. zónában, egyet *2*-ban, majd egyet *3*-ban. Az AK vezérlőelem-sík összetevői a legmagasabb rendelkezésre állású konfigurációban található zónák között is el vannak osztva, mivel azok a fürt létrehozási folyamatának részeként vannak meghatározva.

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

Az AK-fürt létrehozása néhány percet vesz igénybe.

## <a name="verify-node-distribution-across-zones"></a>Csomópontok eloszlásának ellenőrzése a zónák között

Ha a fürt elkészült, sorolja fel a méretezési csoport ügynök-csomópontjait, hogy megtekintse, milyen rendelkezésre állási zónákat telepítenek a rendszerbe.

Először szerezze be az AK-fürt hitelesítő adatait az az az [AK Get-hitelesítőadats][az-aks-get-credentials] paranccsal:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Ezután használja a [kubectl leíró][kubectl-describe] parancsot a fürt csomópontjainak listázásához. Szűrje a *failure-domain.Beta.kubernetes.IO/Zone* értéket a következő példában látható módon:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

A következő példa kimenete a megadott régió és rendelkezésre állási zónák között elosztott három csomópontot mutatja be, például a *eastus2-1* értéket az első rendelkezésre állási zónához, és a *eastus2-2* a második rendelkezésre állási zónához:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Ha további csomópontokat ad hozzá egy ügynök-készlethez, az Azure platform automatikusan elosztja a mögöttes virtuális gépeket a megadott rendelkezésre állási zónák között.

Vegye figyelembe, hogy az újabb verziókban (1.17.0 és újabb verziók) az Kubernetes az újabb `topology.kubernetes.io/zone` címkét használja az elavult érték `failure-domain.beta.kubernetes.io/zone`mellett.

## <a name="verify-pod-distribution-across-zones"></a>A pod-eloszlás ellenőrzése a zónák között

Amint azt a [jól ismert címkék, jegyzetek és Adatszennyező objektumok][kubectl-well_known_labels]dokumentálják, a `failure-domain.beta.kubernetes.io/zone` Kubernetes a címkével automatikusan terjeszti a hüvelyeket egy replikációs vezérlőben vagy szolgáltatásban az elérhető különböző zónák között. Ennek teszteléséhez 3 – 5 csomópontra méretezheti a fürtöt a helyes Pod-terjesztés ellenőrzéséhez:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Ha a skálázási művelet néhány perc elteltével befejeződik, a parancsnak `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` a következőhöz hasonló kimenetet kell adnia:

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

Amint láthatja, már két további csomópont található az 1. és 2. zónában. Három replikából álló alkalmazást is telepíthet. Az NGINX-et példaként fogjuk használni:

```console
kubectl run nginx --image=nginx --replicas=3
```

Ha ellenőrzi, hogy a használt csomópontok hol futnak a hüvelyek, látni fogja, hogy a hüvelyek a három különböző rendelkezésre állási zónának megfelelő hüvelyeken futnak. A paranccsal `kubectl describe pod | grep -e "^Name:" -e "^Node:"` például a következőhöz hasonló kimenetet kaphat:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Ahogy az előző kimenetben látható, az első Pod a 0. csomóponton fut, amely a rendelkezésre állási zónában `eastus2-1`található. A második Pod a 2 `eastus2-3` `eastus2-2`. csomóponton fut, amely a (z) és a (z) 4. csomópontjában található. További konfiguráció nélkül a Kubernetes a három rendelkezésre állási zónában helyesen terjeszti a hüvelyeket.

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen ismerteti, hogyan hozhat létre rendelkezésre állási zónákat használó AK-fürtöt. A magasan elérhető fürtökkel kapcsolatos további szempontokat lásd: [ajánlott eljárások az üzletmenet folytonossága és a vész-helyreállítás az AK-ban][best-practices-bc-dr].

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
