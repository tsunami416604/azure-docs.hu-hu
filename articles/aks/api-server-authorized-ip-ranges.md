---
title: Az API-kiszolgáló által jóváhagyott IP-címtartományok az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan biztonságossá teheti a fürtöt IP-címtartomány használatával az Azure Kubernetes szolgáltatásban (ak) lévő API-kiszolgálóhoz való hozzáféréshez
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 357c8ea4da2a07864215225f7d618f9eb58b7e49
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266185"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Biztonságos hozzáférés az API-kiszolgálóhoz a jogosult IP-címtartományok használatával az Azure Kubernetes szolgáltatásban (ak)

A Kubernetes-ben az API-kiszolgáló kérelmeket fogad a fürt műveleteinek végrehajtásához, például erőforrások létrehozásához vagy a csomópontok számának skálázásához. Az API-kiszolgáló a fürttel való kommunikáció és felügyelet központi módja. A fürt biztonságának növelése és a támadások csökkentése érdekében az API-kiszolgálónak csak korlátozott IP-címtartományok közül kell elérhetőnek lennie.

Ebből a cikkből megtudhatja, hogyan használható az API-kiszolgáló által engedélyezett IP-címtartományok annak a korlátozására, hogy mely IP-címek és CIDRs férhetnek hozzá a vezérlő síkja.

> [!IMPORTANT]
> Az új fürtökön az API-kiszolgáló által engedélyezett IP-címtartományok csak a *standard* SKU Load Balancer esetében támogatottak. Az *alapszintű* SKU Load Balancer és a konfigurált API-kiszolgáló által jóváhagyott IP-címtartományok meglévő fürtök továbbra is ugyanúgy működnek, mint a *standard* SKU Load Balancer. Ezek a meglévő fürtök továbbra is működni fognak, ha a Kubernetes verziója vagy a vezérlő síkja frissül.

## <a name="before-you-begin"></a>Előkészületek

Az API-kiszolgáló által jóváhagyott IP-címtartományok csak a létrehozott új AK-fürtök esetében működnek. Ez a cikk bemutatja, hogyan hozhat létre egy AK-fürtöt az Azure CLI használatával.

Szüksége lesz az Azure CLI-verzió 2.0.76 vagy újabb verziójára, és konfigurálva van.  `az --version`A verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Az API-kiszolgáló által jóváhagyott IP-címtartományok áttekintése

A Kubernetes API-kiszolgáló az alapul szolgáló Kubernetes API-k számára elérhető. Ez az összetevő a felügyeleti eszközök, például `kubectl` a vagy a Kubernetes irányítópultjának interakcióját biztosítja. Az AK egybérlős fürtös főkiszolgálót biztosít dedikált API-kiszolgálóval. Alapértelmezés szerint az API-kiszolgáló nyilvános IP-címet kap, és a hozzáférés vezérlése szerepköralapú hozzáférés-vezérléssel (RBAC) történik.

Az egyébként nyilvánosan elérhető AK-vezérlési sík/API-kiszolgáló hozzáférésének biztonságossá tételéhez engedélyezheti és használhatja az engedélyezett IP-tartományokat. Ezek a megengedett IP-címtartományok csak a meghatározott IP-címtartományok használatát teszik lehetővé az API-kiszolgálóval való kommunikációhoz. Az API-kiszolgálónak egy olyan IP-címről érkező kérés le van tiltva, amely nem része ezeknek az engedélyezett IP-tartományoknak. Továbbra is használhatja a RBAC-t a felhasználók engedélyezéséhez és a kért műveletekhez.

További információ az API-kiszolgálóról és az egyéb fürt-összetevőkről: [Kubernetes Core Concepts for AK][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>AK-fürt létrehozása az API-kiszolgáló által engedélyezett IP-címtartományok engedélyezve

Az API-kiszolgáló által jóváhagyott IP-címtartományok csak az új AK-fürtök esetében működnek. Hozzon létre egy fürtöt az az [AK Create][az-aks-create] paranccsal, és adja meg a *--API-Server-engedélyezve-IP-címtartományok* paramétert a jóváhagyott IP-címtartományok listájának megadásához. Ezek az IP-címtartományok általában a helyszíni hálózatok vagy a nyilvános IP-címek által használt címtartományok. CIDR-tartomány megadásakor a tartomány első IP-címével kezdjen el. Például a *137.117.106.90/29* egy érvényes tartomány, de győződjön meg arról, hogy az első IP-címet adta meg a tartományban, például *137.117.106.88/29*.

> [!IMPORTANT]
> Alapértelmezés szerint a fürt a [standard SKU Load balancert][standard-sku-lb] használja, amelyet a kimenő átjáró konfigurálására használhat. Ha engedélyezi az API-kiszolgálók engedélyezett IP-tartományait a fürt létrehozása során, a fürt nyilvános IP-címe alapértelmezés szerint is engedélyezett a megadott tartományokon kívül. Ha *""* értéket ad meg, vagy nincs megadva a *--API-Server-engedélyezett-IP-címtartományok*, az API-kiszolgáló által engedélyezett IP-címtartományok le lesznek tiltva. Vegye figyelembe, hogy ha a PowerShellt használja, használja a *--API-Server-engedélyezve-IP-tartományok = ""* (egyenlő aláírással) lehetőséget az elemzési problémák elkerüléséhez.

A következő példa egy *myAKSCluster* nevű egycsomópontos fürtöt hoz létre az *myResourceGroup* nevű erőforráscsoport és az API-kiszolgáló által engedélyezett IP-címtartományok használatával. Az IP-címtartományok megengedett *73.140.245.0/24*:

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
> Adja hozzá ezeket a tartományokat egy engedélyezési listához:
> - A tűzfal nyilvános IP-címe
> - Bármely tartomány, amely azokat a hálózatokat képviseli, amelyekről a fürtöt felügyelni szeretné
> - Ha az AK-fürtön az Azure dev Spaces szolgáltatást használja, akkor a [régión alapuló további tartományokat][dev-spaces-ranges]is engedélyeznie kell.

> A megadható IP-címtartományok felső határa 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>A standard SKU Load Balancer kimenő IP-címeinek megadása

AK-fürt létrehozásakor, ha megadja a fürt kimenő IP-címeit vagy előtagjait, akkor ezek a címek vagy előtagok is engedélyezettek. Például:

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

A fenti példában a-- *Load-Balancer-kimenő-IP-előtag* paraméterben megadott összes IP-cím engedélyezett a *--API-Server-winuel-IP-Ranges* paraméterben található IP-címekkel együtt.

Másik lehetőségként megadhatja a *--Load-Balancer-kimenő-IP-előtag* paramétert is a kimenő terheléselosztó IP-előtagjainak engedélyezéséhez.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Csak a standard SKU Load Balancer kimenő nyilvános IP-címének engedélyezése

Ha engedélyezi az API-kiszolgáló engedélyezett IP-tartományait a fürt létrehozása során, akkor a fürt szabványos SKU-Load Balancer kimenő nyilvános IP-címe is alapértelmezés szerint engedélyezett a megadott tartományokon kívül. Ha csak a standard SKU Load Balancer kimenő nyilvános IP-címét szeretné engedélyezni, használja a *0.0.0.0/32* -et a *--API-Server-Allowed-IP-címtartományok* paraméter megadásakor.

A következő példában csak a standard SKU Load Balancer kimenő nyilvános IP-címe engedélyezett, és csak a fürt csomópontjain található API-kiszolgáló férhet hozzá.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Fürt API-kiszolgálójának IP-tartományának frissítése

Ha egy meglévő fürtön szeretné frissíteni az API-kiszolgáló által jóváhagyott IP-tartományokat, használja az [az az AK Update][az-aks-update] parancsot, és használja a *--API-Server-* Reporting-IP-tartományok, *--Load-Balancer-kimenő-IP-előtagok*, *--Load-Balancer-kimenő-* IP-címek, vagy *--Load-Balancer-kimenő-IP-előtag*

Az alábbi példa frissíti az API-kiszolgáló által jóváhagyott IP-tartományokat a *myAKSCluster* nevű fürtön a *myResourceGroup*nevű erőforráscsoport alatt. Az engedélyezni kívánt IP-címtartomány a *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

A *0.0.0.0/32* -et is használhatja a *--API-Server-Allowed-IP-Ranges* paraméter megadásával, hogy csak a standard SKU Load Balancer nyilvános IP-címét engedélyezze.

## <a name="disable-authorized-ip-ranges"></a>A hitelesítő IP-címtartományok letiltása

Az engedélyezett IP-címtartományok letiltásához használja az [az AK Update][az-aks-update] parancsot, és az API-kiszolgáló által engedélyezett IP-címtartományok letiltásához használjon üres tartományt. Például:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben engedélyezte az API-kiszolgáló engedélyezett IP-tartományait. Ez a megközelítés a biztonságos AK-fürtök futtatásának egyik része.

További információ: az [AK-ban található alkalmazásokhoz és fürtökhöz tartozó biztonsági fogalmak][concepts-security] , valamint a [fürt biztonságára és az AK-ban való frissítésére vonatkozó ajánlott eljárások][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
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
