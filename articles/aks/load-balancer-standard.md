---
title: Standard SKU Load Balancer használata az Azure Kubernetes Service-ben (ak)
description: Megtudhatja, hogyan használhatja a Load balancert standard SKU-val, hogy szolgáltatásai elérhetők legyenek az Azure Kubernetes szolgáltatással (ak).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: c2d652b31c264d7b17fcf303564c327d09d416f9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929139"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Standard SKU Load Balancer használata az Azure Kubernetes Service-ben (ak)

Ha az Azure Kubernetes szolgáltatásban (ak) kíván hozzáférést biztosítani az alkalmazásaihoz, létrehozhat és használhat egy Azure Load Balancer. Az AK-on futó Load Balancer belső vagy külső terheléselosztóként is használható. A belső terheléselosztó csak az AK-fürttel azonos virtuális hálózaton futó alkalmazások számára teszi elérhetővé a Kubernetes szolgáltatást. A külső terheléselosztó egy vagy több nyilvános IP-címet kap a bejövő forgalom számára, és a nyilvános IP-címek használatával a Kubernetes szolgáltatás kívülről is elérhetővé válik.

Azure Load Balancer két SKU-ban érhető el – *Alapszintű* és *standard*. Alapértelmezés szerint a *standard* SKU-t használja a rendszer, amikor egy AK-fürtöt hoz létre. A *standard* SKU Load Balancer használata további funkciókat és funkciókat kínál, például nagyobb méretű háttérrendszer-készletet és Availability Zones. Fontos, hogy megértse a *standard* és az *alapszintű* terheléselosztó közötti különbségeket, mielőtt kiválasztja, hogy melyiket kívánja használni. Ha egy AK-fürtöt hoz létre, az adott fürthöz tartozó terheléselosztó SKU nem módosítható. Az *alapszintű* és a *standard* SKU-ról további információt az [Azure Load Balancer SKU-összehasonlítását][azure-lb-comparison]ismertető témakörben talál.

Ez a cikk a Kubernetes és a Azure Load Balancer fogalmak alapszintű megismerését feltételezi. További információ: a [Kubernetes alapvető fogalmai az Azure Kubernetes Service-ben (ak)][kubernetes-concepts] és [Mi az Azure Load Balancer?][azure-lb]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.74 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk azt feltételezi, hogy van egy AK-fürt a *standard* SKU Azure Load Balancer. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Ha meglévő alhálózatot vagy erőforráscsoportot használ, az AK-fürt egyszerű szolgáltatása a hálózati erőforrások kezeléséhez is szükséges. Általában rendelje hozzá a *hálózati közreműködő* szerepkört az egyszerű szolgáltatáshoz a delegált erőforrásokon. Az engedélyekkel kapcsolatos további információkért lásd: [AK-hozzáférés delegálása más Azure-erőforrásokhoz][aks-sp].

### <a name="limitations"></a>Korlátozások

A következő korlátozások érvényesek a terheléselosztó és a *szabványos* SKU-t támogató AK-fürtök létrehozásakor és kezelésekor:

* Legalább egy nyilvános IP-cím vagy IP-előtag szükséges ahhoz, hogy a kimenő forgalom az AK-fürtből legyen engedélyezve. A nyilvános IP-cím vagy IP-előtag szükséges a vezérlési sík és az ügynök csomópontjai közötti kapcsolat fenntartásához, valamint az AK korábbi verzióival való kompatibilitás fenntartásához is. A következő lehetőségek közül választhat a *szabványos* SKU Load balancerrel rendelkező nyilvános IP-címek vagy IP-előtagok megadásához:
    * Adja meg saját nyilvános IP-címeit.
    * Adja meg saját nyilvános IP-előtagjait.
    * 100-ig terjedő számot kell megadnia, amely lehetővé teszi, hogy az AK-fürt számos *szabványos* SKU-beli nyilvános IP-címet hozzon létre ugyanabban az erőforráscsoporthoz, amely az AK-fürthöz lett létrehozva, amelyet általában a *MC_* elején neveznek el. Az AK a nyilvános IP-címet a *standard* SKU Load Balancerhez rendeli. Alapértelmezés szerint a rendszer automatikusan létrehoz egy nyilvános IP-címet ugyanabban az erőforráscsoportban, mint az AK-fürtöt, ha nincs megadva nyilvános IP-cím, nyilvános IP-előtag vagy IP-címek száma. Emellett engedélyeznie kell a nyilvános címeket, és el kell kerülnie az IP-létrehozást megtiltó Azure Policy létrehozását.
* A terheléselosztó *szabványos* SKU-jának használatakor a 1,13-es vagy újabb Kubernetes-verziót kell használnia.
* A terheléselosztó SKU definiálása csak akkor hajtható végre, ha AK-fürtöt hoz létre. Egy AK-fürt létrehozása után nem módosíthatja a terheléselosztó SKU-t.
* Egyetlen fürtben csak egy terheléselosztó SKU-t használhat.

## <a name="configure-the-load-balancer-to-be-internal"></a>A terheléselosztó belső beállítása

Azt is beállíthatja, hogy a terheléselosztó belső legyen, és ne tegye közzé a nyilvános IP-címet. A terheléselosztó belsőként való konfigurálásához vegyen fel `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` jegyzetként a *terheléselosztó* szolgáltatásba. [Itt][internal-lb-yaml]láthat egy példát a YAML-jegyzékre, valamint további részleteket is megtudhat a belső terheléselosztó használatával kapcsolatban.

## <a name="scale-the-number-of-managed-public-ips"></a>A felügyelt nyilvános IP-címek számának méretezése

Ha egy *szabványos* SKU-Load balancert használ a felügyelt kimenő nyilvános IP-címekkel, amelyek alapértelmezés szerint jönnek létre, akkor a felügyelt kimenő nyilvános IP-címeket a *terheléselosztó – felügyelt-IP-Count* paraméter használatával méretezheti.

Meglévő fürt frissítéséhez futtassa a következő parancsot. Ez a paraméter a fürt létrehozási ideje beállításnál is beállítható, hogy több felügyelt kimenő nyilvános IP-cím legyen.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

A fenti példa a felügyelt kimenő nyilvános IP-címek számát állítja be a *myResourceGroup*-ben található *myAKSCluster* - *fürt esetében.* 

A *terheléselosztás – felügyelt IP-Count* paraméterrel is beállíthatja a felügyelt kimenő nyilvános IP-címek kezdeti számát a fürt létrehozásakor, ha hozzáfűzi a `--load-balancer-managed-outbound-ip-count` paramétert, és beállítja a kívánt értékre. A felügyelt kimenő nyilvános IP-címek alapértelmezett száma 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Saját nyilvános IP-címek vagy előtagok megadása a kimenő forgalomhoz

*Standard* SKU Load Balancer használatakor az AK-fürt automatikusan létrehoz egy nyilvános IP-címet ugyanabban az erőforráscsoporthoz, amely az AK-fürthöz lett létrehozva, és hozzárendeli a nyilvános IP-címet a *standard* SKU Load Balancerhez. Azt is megteheti, hogy a fürt létrehozási idején saját nyilvános IP-címet rendel hozzá, vagy frissítheti a meglévő fürt terheléselosztó-tulajdonságait.

Több IP-cím vagy előtag megadásával több háttér-szolgáltatást is meghatározhat, ha az IP-címet egyetlen terheléselosztó objektum mögött definiálja. Az adott csomópontok kimenő végpontja attól függ, hogy milyen szolgáltatáshoz vannak társítva.

> [!IMPORTANT]
> *Szabványos* SKU nyilvános IP-címeket kell használnia a kimenő forgalomhoz a Load Balancer *standard* SKU-jának megfelelően. A nyilvános IP-címek SKU-jának ellenőrzéséhez használja az az [Network Public-IP show][az-network-public-ip-show] parancsot:
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

A nyilvános IP-címek azonosítóinak listázásához használja az az [Network Public-IP show][az-network-public-ip-show] parancsot.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

A fenti parancs a *myPublicIP* nyilvános IP-CÍMÉnek azonosítóját jeleníti meg a *myResourceGroup* -erőforráscsoporthoz.

Használja az az *AK Update* parancsot a *Load-Balancer-kimenő-IPS* paraméterrel, és frissítse a fürtöt a nyilvános IP-címekkel.

A következő példa a *Load-Balancer-kimenő-IPS* paramétert használja az előző parancs azonosítói alapján.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Nyilvános IP-előtagokat is használhat a *standard* SKU Load balancerrel való kimenő forgalomhoz. Az alábbi példa az az [Network Public-IP előtag show][az-network-public-ip-prefix-show] parancsot használja a nyilvános IP-előtagok azonosítóinak listázásához:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

A fenti parancs a *myPublicIPPrefix* nyilvános IP-előtag azonosítóját jeleníti meg a *myResourceGroup* erőforráscsoporthoz.

Az alábbi példa a *Load-Balancer-kimenő-IP-előtag* paramétert használja az előző parancs azonosítói alapján.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> A nyilvános IP-címeknek és az IP-ELŐTAGOKNAK ugyanabban a régióban kell lenniük, és ugyanannak az előfizetésnek kell szerepelniük, mint az AK-fürt. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Saját nyilvános IP-cím vagy előtag definiálása a fürt létrehozási idején

Előfordulhat, hogy saját IP-címeket vagy IP-előtagokat kíván létrehozni a kimenő forgalomhoz a fürt létrehozási ideje alatt, hogy támogassa a kimenő végpontok engedélyezési forgatókönyveit. Fűzze hozzá ugyanezeket a paramétereket a fürt létrehozási lépéseként, hogy meghatározza a saját nyilvános IP-címeit és az IP-előtagokat a fürt életciklusának elején.

*A* *Load-Balancer-kimenő-IPS* paraméterrel hozzon létre egy új fürtöt a nyilvános IP-címekkel az elején.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Használja az az *AK Create* parancsot a *Load-Balancer-kimenő-IP-előtag* paraméterrel egy új fürt létrehozásához a nyilvános IP-előtagokkal az elején.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="show-the-outbound-rule-for-your-load-balancer"></a>A terheléselosztó kimenő szabályának megjelenítése

A terheléselosztó által létrehozott Kimenő szabály megjelenítéséhez használja az [az Network LB kimenő-Rule List][az-network-lb-outbound-rule-list] lehetőséget, és adja meg az AK-fürt Node erőforráscsoportot:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Az előző parancs a terheléselosztó kimenő szabályát fogja kilistázni, például:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

A példában a kimenetben a *AllocatedOutboundPorts* 0. A *AllocatedOutboundPorts* értéke azt jelenti, hogy a SNAT-portok kiosztása a háttérbeli készlet méretétől függően automatikus hozzárendelésre áll. További részletekért tekintse [meg az Azure-ban][azure-lb-outbound-connections] [Load Balancer kimenő][azure-lb-outbound-rules] és kimenő kapcsolatokat.

## <a name="next-steps"></a>Következő lépések

További információ a Kubernetes Services szolgáltatásról a [Kubernetes Services dokumentációjában][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
