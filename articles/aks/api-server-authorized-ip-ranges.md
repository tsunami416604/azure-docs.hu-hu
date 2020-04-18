---
title: AZ API-kiszolgáló által engedélyezett IP-tartományok az Azure Kubernetes szolgáltatásban (AKS)
description: Megtudhatja, hogyan biztosíthatja a fürt egy IP-címtartomány használatával az API-kiszolgálóhoz való hozzáféréshez az Azure Kubernetes szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 570d842409fc019d24446e091f83402f4c288d7c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640058"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Biztonságos hozzáférés az API-kiszolgálóhoz az Azure Kubernetes-szolgáltatás (AKS) engedélyezett IP-címtartományai használatával

A Kubernetes rendszerben az API-kiszolgáló a fürtben végrehajtott műveletek végrehajtására vonatkozó kéréseket kap, például erőforrásokat hoz létre, vagy a csomópontok számát méretezze. Az API-kiszolgáló a fürtekkel való kommunikáció és a fürt kezelésének központi módja. A fürtbiztonság növelése és a támadások minimalizálása érdekében az API-kiszolgáló csak korlátozott IP-címtartományokból érhető el.

Ez a cikk bemutatja, hogyan használhatja az API-kiszolgáló engedélyezett IP-címtartományait annak korlátozására, hogy mely IP-címek és CID-k férhetnek hozzá a vezérlősíkhoz.

> [!IMPORTANT]
> Az új fürtökön az API-kiszolgáló által engedélyezett IP-címtartományok csak a *szabványos* termékváltozat terheléselosztóján támogatottak. Az *alapszintű* termékváltozat terheléselosztóval és AZ API-kiszolgáló által engedélyezett IP-címtartományokkal rendelkező meglévő fürtök továbbra is úgy működnek, ahogy vannak, de nem telepíthetők át *egy szabványos* termékváltozat terheléselosztóba. Ezek a meglévő fürtök is továbbra is működni fog, ha a Kubernetes verzió vagy vezérlősík frissítése.

## <a name="before-you-begin"></a>Előkészületek

Az API-kiszolgáló által engedélyezett IP-tartományok csak az Ön által létrehozott új AKS-fürtöknél működnek. Ez a cikk bemutatja, hogyan hozhat létre egy AKS-fürt az Azure CLI használatával.

Az Azure CLI 2.0.76-os vagy újabb verziójára van szükség telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Az API-kiszolgáló engedélyezett IP-tartományainak áttekintése

A Kubernetes API-kiszolgáló az alapul szolgáló Kubernetes API-k elérhetővé teszi. Ez az összetevő biztosítja a felügyeleti `kubectl` eszközök, például a Kubernetes irányítópult kapcsolati kapcsolatát. Az AKS egy egybérlős fürtfőkiszolgálót biztosít egy dedikált API-kiszolgálóval. Alapértelmezés szerint az API-kiszolgálóhoz nyilvános IP-cím van rendelve, és a hozzáférést szerepköralapú hozzáférés-vezérléssel (RBAC) kell szabályozni.

Az egyébként nyilvánosan elérhető AKS vezérlősíkhoz / API-kiszolgálóhoz való hozzáférés biztosításához engedélyezheti és használhatja az engedélyezett IP-tartományokat. Ezek az engedélyezett IP-tartományok csak meghatározott IP-címtartományokat engedélyeznek az API-kiszolgálóval való kommunikációhoz. Az API-kiszolgálóra olyan IP-címről érkező kérelem, amely nem része ezeknek az engedélyezett IP-tartományoknak, le van tiltva. Továbbra is használja az RBAC-ot a felhasználók és az általuk kért műveletek engedélyezéséhez.

Az API-kiszolgálóról és más fürtösszetevőkről a [Kubernetes AKS alapfogalmai][concepts-clusters-workloads]című témakörben talál további információt.

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>AKS-fürt létrehozása API-kiszolgáló által engedélyezett IP-tartományokkal

Az API-kiszolgáló által engedélyezett IP-tartományok csak új AKS-fürtökesetén működnek. Hozzon létre egy fürtöt az [aks létrehozása][az-aks-create] és adja meg a *--api-server-authorized-ip-ranges* paraméter, hogy egy listát az engedélyezett IP-címtartományok. Ezek az IP-címtartományok általában a helyszíni hálózatok vagy nyilvános IP-k által használt címtartományok. Cidr-tartomány megadásakor kezdje a tartomány első IP-címével. Például a *137.117.106.90/29* egy érvényes tartomány, de győződjön meg arról, hogy megadja a tartomány első IP-címét, például *137.117.106.88/29*.

> [!IMPORTANT]
> Alapértelmezés szerint a fürt a [szabványos termékváltozat terheléselosztót][standard-sku-lb] használja, amellyel konfigurálhatja a kimenő átjárót. Ha engedélyezi az API-kiszolgáló által engedélyezett IP-tartományokat a fürt létrehozása során, a fürt nyilvános IP-címe is alapértelmezés szerint engedélyezett a megadott tartományokon kívül. Ha a *--api-server-authorized-ip-ranges*értéket adja meg, az API-kiszolgáló által engedélyezett IP-tartományok le lesznek tiltva. *""* Vegye figyelembe, hogy ha a PowerShell használata, *használja --api-server-authorized-ip-ranges=""* (egyenlőségjellel) az elemzési problémák elkerülése érdekében.

A következő példa létrehoz egy egycsomópontos fürt nevű *myAKSCluster* az erőforráscsoport nevű *myResourceGroup* API-kiszolgáló engedélyezett IP-tartományok engedélyezve van. A megengedett IP-címtartományok: *73.140.245.0/24*:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Ezeket a tartományokat hozzá kell adni a következő engedélyezési listához:
> - A tűzfal nyilvános IP-címe
> - Minden olyan tartomány, amely a fürt felügyeletére a fürtöt
> - Ha az AKS-fürtön azure dev spaces-t használ, [a régióalapján további tartományokat kell engedélyeznie.][dev-spaces-ranges]

> A megadható IP-tartományok számának felső határa 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Adja meg a standard termékváltozat terheléselosztó kimenő IP-jének megadását

AKS-fürt létrehozásakor, ha megadja a fürt kimenő IP-címeit vagy előtagjait, akkor ezek a címek vagy előtagok is engedélyezettek. Például:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

A fenti példában a *--load-balancer-out-ip-prefixes* paraméterben megadott összes IP-cím engedélyezett a *--api-server-authorized-ip-ranges* paraméter BEN az IP-címekkel együtt.

Másik lehetőségként megadhatja a *--load-balancer-out-ip-prefixes* paramétert a kimenő terheléselosztó IP-előtagok engedélyezéséhez.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Csak a szabványos termékváltozat terheléselosztó kimenő nyilvános IP-címének engedélyezése

Ha engedélyezi az API-kiszolgáló által engedélyezett IP-tartományokat a fürt létrehozása során, a fürt szabványos termékváltozatának terheléselosztójának kimenő nyilvános IP-címe is alapértelmezés szerint engedélyezett a megadott tartományokon kívül. Ha csak a standard termékváltozat terheléselosztó kimenő nyilvános IP-címének engedélyezéséhez használja a *0.0.0.0/32* értéket a *--api-server-authorized-ip-ranges* paraméter megadásakor.

A következő példában csak a standard termékváltozat terheléselosztó kimenő nyilvános IP-címe engedélyezett, és csak a fürt csomópontjaiból érheti el az API-kiszolgálót.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Fürt API-kiszolgálójának engedélyezett IP-tartományai frissítése

Az API-kiszolgáló engedélyezett IP-tartományainak frissítéséhez használja az [aks update][az-aks-update] parancsot, és használja a *--api-server-authorized-ip-ranges*, *--load-balancer-outbound-ip-prefixes*, *--load-balancer-outbound-ips*, vagy *--load-balancer-out-ip-prefixes* paramétereket.

A következő példa frissíti az API-kiszolgáló által engedélyezett IP-tartományokat a *myResourceGroup*nevű erőforráscsoportban a *myResourceGroup* nevű fürtön. Az engedélyezésre engedélyezett *73.140.245.0/24*IP-címtartomány:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

*A 0.0.0.0/32* értéket is használhatja a *--api-server-authorized-ip-ranges* paraméter megadásakor, hogy csak a standard termékváltozat terheléselosztó nyilvános IP-címét engedélyezze.

## <a name="disable-authorized-ip-ranges"></a>Engedélyezett IP-tartományok letiltása

Az engedélyezett IP-tartományok letiltásához használja [az aks update-et,][az-aks-update] és adjon meg egy üres tartományt az API-kiszolgáló által engedélyezett IP-tartományok letiltásához. Például:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben engedélyezte az API-kiszolgáló engedélyezett IP-tartományait. Ez a megközelítés egy része, hogyan futtathat egy biztonságos AKS-fürt.

További információt az [AKS-ben az alkalmazások és fürtök biztonsági fogalmai,][concepts-security] valamint [a fürtbiztonsággal és az AKS-frissítésektel kapcsolatos gyakorlati tanácsok című témakörben][operator-best-practices-cluster-security]talál.

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
