---
title: Az Azure Kubernetes Service (AKS) rendelkezésre állási zónák használata
description: Ismerje meg, hogyan hozhat létre egy fürtöt, amely elosztja a csomópont a rendelkezésre állási zónában az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840679"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Előzetes verzió – egy rendelkezésre állási zónák használó Azure Kubernetes Service (AKS)-fürt létrehozása

Az Azure Kubernetes Service (AKS)-fürt osztja el erőforrások, például a csomópontok és a különböző logikai szakaszok az alapul szolgáló Azure storage számítási infrastruktúra. Ez a telepítési modell gondoskodik arról, hogy a csomópontok futnak, egyetlen Azure-adatközpontban külön frissítési és tartalék tartományok között. Ez az alapértelmezett viselkedés telepített AKS-fürtök magas fokú rendelkezésre állás, a hardver meghibásodása elleni védelem érdekében, vagy tervezett karbantartási esemény.

Ahhoz, hogy egy magasabb szintű rendelkezésre állást, az alkalmazások, az AKS-fürt is elosztott rendelkezésre állási zónák között. A zónák fizikailag különálló adatközpontok, egy adott régión belül. Amikor a kiszolgálófürt-összetevők több zónában, az AKS-fürt is képes elviselni e zónák telefonszámaira hiba. Az alkalmazások és a felügyeleti műveletek továbbra is elérhetők, még akkor is, ha egy teljes adatközpontot problémát észlelt.

Ez a cikk bemutatja, hogyan AKS-fürt létrehozása és terjesztése a csomópont-összetevőinek rendelkezésre állási zónák között. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók önkiszolgáló, a rendszer. A biztosított gyűjthet visszajelzéseket és a hibák kapcsolódóan a Közösség részéről. Előzetes verzióban elérhető ezeket a funkciókat nem üzemi használat céljára. Nyilvános előzetes verzióban érhető el "ajánlott beavatkozást" támogatás keretében tartoznak. Az AKS technikai támogatási csapat segítségét munkaidőben csendes-óceáni időzóna (PST) csak alatt érhető el. További információkért tekintse meg a következő cikkek támogatja:
>
> * [Az AKS támogatási házirendek][aks-support-policies]
> * [Az Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.66 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Az aks előzetes CLI-bővítmény telepítése

A rendelkezésre állási zónák használata AKS-fürt létrehozásához, a *aks előzetes* CLI bővítmény verziója 0.4.1 vagy újabb verziója. Telepítse a *aks előzetes* Azure CLI-bővítmény használata a [az bővítmény hozzáadása][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] parancs::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Regisztrálja az előfizetéshez tartozó szolgáltatás jelzők

AKS-fürt létrehozása a rendelkezésre állási zónák, először engedélyeznie kell az előfizetéshez, néhány funkció jelzők. Fürtök kezelése, telepítését és konfigurálását a Kubernetes-csomópontok beállítása egy virtuálisgép-méretezési csoport használja. A *standard* Termékváltozat az Azure load Balancer is szükséges rugalmasságot biztosítanak a hálózati összetevők irányíthatja a forgalmat a fürtbe. Regisztrálja a *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*, és *VMSSPreview* funkció jelzők segítségével a [az a funkció regisztrálása][az-feature-register] parancsot az alábbi példában látható módon:

> [!CAUTION]
> A funkció egy adott előfizetés regisztrálásakor nem jelenleg regisztrációjának ezt a funkciót. Miután engedélyezte az egyes előzetes verziójú funkciók, alapértelmezett érték az összes AKS-fürt, majd az előfizetésben létrehozott használható. Nem engedélyezi az előzetes verziójú funkciók az éles üzemű előfizetéseket. Használjon különálló előfizetést előzetes verziójú funkciók teszteléséhez, és visszajelzést.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot használatával ellenőrizheti a [az szolgáltatáslistát][az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register][az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Korlátozások és régiók rendelkezésre állása

AKS-fürtök jelenleg lehet létrehozni a rendelkezésre állási zónák használatával a következő régiókban:

* USA 2. keleti régiója
* Észak-Európa
* Délkelet-Ázsia
* Nyugat-Európa
* USA nyugati régiója, 2.

Az alábbi korlátozások érvényesek a rendelkezésre állási zónák használata AKS-fürt létrehozásakor:

* Csak akkor tudja engedélyezni a rendelkezésre állási zónák, a fürt létrehozásakor.
* A fürt létrehozása után nem lehet frissíteni a rendelkezésre állási zóna beállításait. Egy meglévő, nem rendelkezésre állási zóna fürt rendelkezésre állási zónák használatára is nem frissíthető.
* Miután létrejött, az AKS-fürt rendelkezésre állási zónák nem tiltható le.
* A csomópont méretét (a VM-Termékváltozatok) kiválasztott elérhetőnek kell lennie az összes rendelkezésre állási zónák között.
* Rendelkezésre állási zónák engedélyezve van szükség a fürtök használata az Azure Standard Load Balancer Terheléselosztók terjesztési zónák között.
* Kubernetes 1.13.5 verziót kell használnia, vagy nagyobb a Standard Load Balancer Terheléselosztók üzembe helyezéséhez.

AKS-fürt rendelkezésre állási zónák használatára kell használni az Azure load balancer *standard* Termékváltozat. Az alapértelmezett *alapszintű* az Azure load Balancer Termékváltozat nem támogatja a terjesztési rendelkezésre állási zónák között. További információkat és a standard vonatkozó korlátozások terheléselosztó, lásd: [Azure load balancer standard Termékváltozat előzetes verzió korlátozásai][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Az Azure disks korlátozások

Kötetek, amelyek használják az Azure managed disks jelenleg nem zónaszintű erőforrásokban. Egy másik zónában az eredeti zónából újraütemezte podok nem csatlakoztassa újból az előző (eke) t. Azt javasoljuk, hogy a nem állandó tároló, amely olyan zónaszintű hibák igénylő állapot nélküli számítási feladatok futtatásához.

Állapotalapú alkalmazások és szolgáltatások kell futtatásakor, használatával elkerülésére, valamint tolerations a pod vonatkozó műszaki adatok a ossza meg a Kubernetes-ütemező podok ugyanabban a zónában, mint a lemezek létrehozásához. Másik megoldásként használhatja a neurálishálózat-alapú tárolási például az Azure Files, amely a podok is csatolhat, mivel azok zónák között van ütemezve.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Fürtök a rendelkezésre állási zónák az aks-ben – áttekintés

A rendelkezésre állási zónák a magas rendelkezésre állást kínál, amely megvédi alkalmazásait és adatait az adatközpontok meghibásodásai. Zónák egyedi fizikai helyszínek az Azure-régióban. Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. Rugalmasság biztosítása érdekében legalább három különálló zónát, az összes engedélyezett régióban van. Egy adott régión belül a rendelkezésre állási zónák fizikai elválasztása adatközpont meghibásodása ellen védi az alkalmazásokat és adatokat. Zónaredundáns szolgáltatásokat az alkalmazások és adatok replikálása single-pontok-az-hibákkal szembeni rendelkezésre állási zónák között.

További információkért lásd: [Mik az Azure-beli rendelkezésre állási zónák?][az-overview].

A rendelkezésre állási zónák használatával helyezi üzembe az AKS-fürt csomópontjai juttathatja el egy adott régión belül több zónában. Ha például egy fürtöt az a *USA keleti RÉGIÓJA 2* régióban hozhat létre a csomópontok minden három rendelkezésre állási zónában található *USA keleti RÉGIÓJA 2*. Az AKS-fürt erőforrásainak eloszlása növeli a fürt rendelkezésre állási, rugalmas, hogy egy adott zónához sikertelen zajlik.

![Az AKS csomópont elosztása a rendelkezésre állási zónák](media/availability-zones/aks-availability-zones.png)

Egy zóna szolgáltatáskimaradás, a csomópontok is manuálisan rebalanced vagy a fürt méretező használatával. Ha egy zóna nem érhető el, az alkalmazások továbbra is futtassa.

## <a name="create-an-aks-cluster-across-availability-zones"></a>AKS-fürt létrehozása rendelkezésre állási zónák között

Amikor hoz létre egy fürtöt az a [az aks létrehozása][az-aks-create] parancs, a `--node-zones` paraméter határozza meg, melyik zónák ügynökcsomópontok vannak üzembe helyezve. Az AKS vezérlési sík összetevők a fürt számára vannak is elosztva a legmagasabb szintű rendelkezésre állású konfigurációban zónák megadása a fürt létrehozásakor a `--node-zones` paraméter.

Ha nem ad meg az alapértelmezett ügynökkészlet számára zónák AKS-fürt létrehozásakor, az AKS vezérlési sík összetevők a fürt számára ne használja a rendelkezésre állási zónák. Hozzáadhat további csomópontkészletek (jelenleg előzetes verzióban érhető el az aks-ben) használatával a [az aks nodepool hozzáadása][az-aks-nodepool-add] parancsot, majd adja meg `--node-zones` ezen új ügynök csomópontok számára azonban a vezérlési sík összetevők továbbra is a rendelkezésre állási zóna nélkül tájékoztatás. A zónák figyelése nem módosítható a csomópontkészletek vagy az AKS szabályozhatja adatsík-összetevők, hogy üzembe helyezésük után.

A következő példában létrehozunk egy AKS-fürt nevű *myAKSCluster* az erőforráscsoport neve *myResourceGroup*. Összesen *3* csomópontjainak létrehozása – zónában egy ügynök *1*egy, a *2*, és ezután egy-egy *3*. Az AKS vezérlési sík összetevőket is vannak elosztva a legmagasabb szintű rendelkezésre állású konfigurációban zónák, mivel azok a fürt létrehozása folyamatban van megadva.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

Az AKS-fürt létrehozása néhány percet vesz igénybe.

## <a name="verify-node-distribution-across-zones"></a>Ellenőrizze a csomópont terjesztési zónák között

Ha a fürt készen áll, a méretezési csoport milyen rendelkezésre állási zónában, hogy az üzembe helyezésük hogy ügynökcsomópontjára listája.

Először kérje le az AKS fürt hitelesítő adatainak használata a [az aks get-credentials][az-aks-get-credentials] parancsot:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Ezután a [írja le a kubectl][kubectl-describe] paranccsal listát készíthet a csomópontok a fürtben. Szűrés a *failure-domain.beta.kubernetes.io/zone* értékét az alábbi példában látható módon:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Az alábbi példa kimenetében látható a három csomóponttal, a megadott régió és a rendelkezésre állási zónák, mint például elosztva *eastus2-1* az első rendelkezésre állási zóna és *eastus2-2* a második rendelkezésre állási zóna:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

További csomópontokat ad hozzá egy ügynök-készletet, mert az Azure platform automatikusan elosztja a mögöttes virtuális gépeket a megadott rendelkezésre állási zónák között.

## <a name="next-steps"></a>További lépések

Ez a cikk részletes rendelkezésre állási zónák használó egy AKS-fürt létrehozása. Magas rendelkezésre állású fürtökön további szempontokért lásd: [ajánlott eljárások az üzleti folytonossági és vészhelyreállítási helyreállítási az aks-ben][best-practices-bc-dr].

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
