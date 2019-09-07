---
title: Availability Zones használata az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan hozhat létre olyan fürtöt, amely a csomópontokat a rendelkezésre állási zónák között osztja el az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: 690d22eadf37a24b4679ce10838074533ac65fcb
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390069"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Előzetes verzió – az Availability Zonest használó Azure Kubernetes Service (ak) fürt létrehozása

Az Azure Kubernetes Service (ak)-fürt erőforrásokat, például a csomópontokat és a tárolókat a mögöttes Azure számítási infrastruktúra logikai fejezetei között osztja el. Ez a telepítési modell gondoskodik arról, hogy a csomópontok külön frissítési és tartalék tartományokon futnak egyetlen Azure-adatközpontban. Az ezzel az alapértelmezett viselkedéssel üzembe helyezett AK-fürtök magas szintű rendelkezésre állást biztosítanak a hardverhiba vagy tervezett karbantartási események elleni védelemhez.

Az alkalmazások magasabb szintű rendelkezésre állásának biztosítása érdekében az AK-fürtök a rendelkezésre állási zónák között terjeszthetők. Ezek a zónák fizikailag különálló adatközpontok az adott régión belül. Ha a fürt összetevői több zónában vannak elosztva, az AK-fürt képes elviselni az egyik zónában fellépő hibát. Az alkalmazások és a felügyeleti műveletek továbbra is elérhetők maradnak, még akkor is, ha az egyik teljes adatközpontnál probléma van.

Ez a cikk bemutatja, hogyan hozhat létre egy AK-fürtöt, és hogyan oszthatja szét a csomópont-összetevőket a rendelkezésre állási zónák között. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.66 vagy újabb verziójára, és konfigurálva van. A `az --version` verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

A rendelkezésre állási zónákat használó AK *-alapú* fürtök létrehozásához a CLI-0.4.1 vagy újabb verziójára van szükség. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-availabilityzonepreview-feature-flag-for-your-subscription"></a>Regisztrálja az előfizetéshez tartozó AvailabilityZonePreview-szolgáltatás jelölőjét

Ha egy AK-fürtöt szeretne létrehozni a rendelkezésre állási zónákhoz, először engedélyezze a *AvailabilityZonePreview* szolgáltatás jelölőjét az előfizetésében. Regisztrálja a *AvailabilityZonePreview* szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Korlátozások és régiók rendelkezésre állása

Az AK-fürtök jelenleg rendelkezésre állási zónák használatával hozhatók létre a következő régiókban:

* USA 2. keleti régiója
* Észak-Európa
* Délkelet-Ázsia
* Nyugat-Európa
* USA nyugati régiója, 2.

A következő korlátozások érvényesek az AK-fürtök rendelkezésre állási zónák használatával történő létrehozásakor:

* A rendelkezésre állási zónák csak a fürt létrehozásakor engedélyezhetők.
* A rendelkezésre állási zóna beállításai nem frissíthetők a fürt létrehozása után. A rendelkezésre állási zónák használatához nem lehet frissíteni egy meglévő, nem rendelkezésre állási zóna fürtöt is.
* A rendelkezésre állási zónák nem tilthatók le egy AK-fürthöz a létrehozása után.
* A kiválasztott csomópont-méretnek (VM SKU) elérhetőnek kell lennie az összes rendelkezésre állási zónában.
* A rendelkezésre állási zónákat engedélyező fürtök esetében az Azure standard Load Balancer használata szükséges a zónák közötti elosztáshoz.
* Standard Load Balancer üzembe helyezéséhez a Kubernetes 1.13.5 vagy újabb verzióját kell használnia.

A rendelkezésre állási zónákat használó AK-fürtöknek az Azure Load Balancer *standard* SKU-t kell használniuk. Az Azure Load Balancer alapértelmezett *alapszintű* SKU-jának nem támogatja a rendelkezésre állási zónák közötti eloszlást. További információ és a standard Load Balancer korlátai: az [Azure Load Balancer standard SKU-korlátai][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Azure-lemezek korlátozásai

Az Azure Managed Disks-t használó kötetek jelenleg nem a zónákhoz tartozó erőforrások. Az eredeti zónától eltérő zónában átütemezett hüvelyek nem csatolhatják újra az előző lemez (eke) t. Azt javasoljuk, hogy olyan állapot nélküli munkaterheléseket futtasson, amelyek nem igényelnek olyan állandó tárterületet, amely többek között a zónákra kiterjedő problémák esetén

Ha állapot-nyilvántartó munkaterhelést kell futtatnia, a saját Pod specifikációjában a szennyező adatok és a tolerálás használatával mondja el, hogy a Kubernetes Scheduler a lemezekkel megegyező zónában hozza létre a hüvelyeket. Azt is megteheti, hogy olyan hálózati tárterületet használ, mint például a Azure Files, amelyek a zónák közötti ütemezés szerint csatolhatók a hüvelyekhez.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Az AK-fürtök Availability Zones áttekintése

A Availability Zones egy magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. A zónák egy Azure-régióban található egyedi fizikai helyszínek. Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. A rugalmasság biztosításához legalább három különálló zónának kell lennie az összes engedélyezett régióban. Egy régión belüli Availability Zones fizikai elkülönítése megvédi az alkalmazásokat és az adatközpontok meghibásodását. Zóna – a redundáns szolgáltatások az alkalmazások és az adatok replikálását Availability Zones az egypontos meghibásodások elleni védelem érdekében.

További információ: [Mi a Availability Zones az Azure-ban?][az-overview].

A rendelkezésre állási zónák használatával üzembe helyezett AK-fürtök több, egyetlen régióban található zónában terjeszthetik a csomópontokat. Az *USA 2* . keleti régiójában található fürt például az *USA 2. keleti*régiójában mindhárom rendelkezésre állási zónában hozhat létre csomópontokat. Az AK-beli fürt erőforrásainak ezen eloszlása javítja a fürt rendelkezésre állását, mivel azok egy adott zóna meghibásodása esetén rugalmasak.

![AK-csomópont eloszlása a rendelkezésre állási zónák között](media/availability-zones/aks-availability-zones.png)

A zónák meghibásodása esetén a csomópontok manuálisan vagy a fürt automéretező használatával is kiegyensúlyozható. Ha egyetlen zóna elérhetetlenné válik, az alkalmazások továbbra is futnak.

## <a name="create-an-aks-cluster-across-availability-zones"></a>AK-fürt létrehozása rendelkezésre állási zónák között

Amikor az az [AK Create][az-aks-create] paranccsal hoz létre fürtöt, a paraméter `--node-zones` határozza meg, hogy a rendszer mely zónákat telepíti a-ben. A fürthöz tartozó AK vezérlőelem-sík összetevői a legmagasabb rendelkezésre állási konfigurációban is elterjednek, ha a `--node-zones` paramétert megadó fürtöt hoz létre.

Ha nem ad meg zónát az alapértelmezett ügynök készletéhez, amikor egy AK-fürtöt hoz létre, akkor a fürthöz tartozó AK vezérlőelem-sík összetevői nem fogják használni a rendelkezésre állási zónákat. Hozzáadhat további Node-készleteket (jelenleg előzetes verzióban az AK-ban) az az [AK nodepool Add][az-aks-nodepool-add] paranccsal `--node-zones` , és megadhatja az új ügynök-csomópontokhoz, azonban a vezérlési sík összetevői továbbra is rendelkezésre állási zónák ismerete nélkül maradhatnak. Az üzembe helyezésük után nem módosítható a zóna, illetve a csomópont-vagy az AK-vezérlő sík összetevőinek ismerete.

A következő példában létrehozunk egy *myAKSCluster* nevű AK-fürtöt az *myResourceGroup*nevű erőforráscsoport-csoportban. Összesen *3* csomópont jön létre – egy ügynök az *1*. zónában, egyet *2*-ban, majd egyet *3*-ban. Az AK vezérlőelem-sík összetevői a legmagasabb rendelkezésre állású konfigurációban található zónák között is el vannak osztva, mivel azok a fürt létrehozási folyamatának részeként vannak meghatározva.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
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
