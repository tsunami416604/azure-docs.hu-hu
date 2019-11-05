---
title: Az API-kiszolgáló által jóváhagyott IP-címtartományok az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan biztonságossá teheti a fürtöt IP-címtartomány használatával az Azure Kubernetes szolgáltatásban (ak) lévő API-kiszolgálóhoz való hozzáféréshez
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472967"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Biztonságos hozzáférés az API-kiszolgálóhoz a jogosult IP-címtartományok használatával az Azure Kubernetes szolgáltatásban (ak)

A Kubernetes-ben az API-kiszolgáló kérelmeket fogad a fürt műveleteinek végrehajtásához, például erőforrások létrehozásához vagy a csomópontok számának skálázásához. Az API-kiszolgáló a fürttel való kommunikáció és felügyelet központi módja. A fürt biztonságának növelése és a támadások csökkentése érdekében az API-kiszolgálónak csak korlátozott IP-címtartományok közül kell elérhetőnek lennie.

Ebből a cikkből megtudhatja, hogyan használható az API-kiszolgáló által engedélyezett IP-címtartományok annak a korlátozására, hogy mely IP-címek és CIDRs férhetnek hozzá a vezérlő síkja.

> [!IMPORTANT]
> Az új fürtökön az API-kiszolgáló által engedélyezett IP-címtartományok csak a *standard* SKU Load Balancer esetében támogatottak. Az *alapszintű* SKU Load Balancer és a konfigurált API-kiszolgáló által jóváhagyott IP-címtartományok a meglévő fürtök továbbra is működni fognak. A meglévő clusers is frissíthetők, és továbbra is működni fognak.

## <a name="before-you-begin"></a>Előzetes teendők

Ez a cikk azt feltételezi, hogy a [kubenet][kubenet]-t használó fürtökkel dolgozik.  Az [Azure Container Network Interface (CNI)][cni-networking] alapú fürtök esetében nem lesz szükség a hozzáférés biztosításához szükséges útválasztási táblázatra.  Az útválasztási táblázatot manuálisan kell létrehoznia.  További információért lásd: [útválasztási táblázatok kezelése](https://docs.microsoft.com/azure/virtual-network/manage-route-table) .

Az API-kiszolgáló által jóváhagyott IP-címtartományok csak a létrehozott új AK-fürtök esetében működnek. Ez a cikk bemutatja, hogyan hozhat létre egy AK-fürtöt az Azure CLI használatával.

Szüksége lesz az Azure CLI-verzió 2.0.76 vagy újabb verziójára, és konfigurálva van. A verzió megkereséséhez futtassa a `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="limitations"></a>Korlátozások

Az API-kiszolgáló által jóváhagyott IP-címtartományok konfigurálásakor a következő korlátozások érvényesek:

* Jelenleg nem használható az Azure dev Spaces, mert az API-kiszolgálóval folytatott kommunikáció is le van tiltva.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Az API-kiszolgáló által jóváhagyott IP-címtartományok áttekintése

A Kubernetes API-kiszolgáló az alapul szolgáló Kubernetes API-k számára elérhető. Ez az összetevő a felügyeleti eszközök, például a `kubectl` vagy a Kubernetes-irányítópult interakcióját biztosítja. Az AK egybérlős fürtös főkiszolgálót biztosít dedikált API-kiszolgálóval. Alapértelmezés szerint az API-kiszolgáló nyilvános IP-címet kap, és a hozzáférés vezérlése szerepköralapú hozzáférés-vezérléssel (RBAC) történik.

Az egyébként nyilvánosan elérhető AK-vezérlési sík/API-kiszolgáló hozzáférésének biztonságossá tételéhez engedélyezheti és használhatja az engedélyezett IP-tartományokat. Ezek a megengedett IP-címtartományok csak a meghatározott IP-címtartományok használatát teszik lehetővé az API-kiszolgálóval való kommunikációhoz. Az API-kiszolgálónak egy olyan IP-címről érkező kérés le van tiltva, amely nem része ezeknek az engedélyezett IP-tartományoknak. Továbbra is a RBAC használatával engedélyezze a felhasználókat és az általuk kért műveleteket.

További információ az API-kiszolgálóról és az egyéb fürt-összetevőkről: [Kubernetes Core Concepts for AK][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az API-kiszolgáló által jóváhagyott IP-címtartományok csak az új AK-fürtök esetében működnek. A fürt létrehozási műveletének részeként nem engedélyezheti az engedélyezett IP-tartományokat. Ha a fürt létrehozási folyamatának részeként megpróbálja engedélyezni az engedélyezett IP-tartományokat, a fürtcsomópontok nem tudnak hozzáférni az API-kiszolgálóhoz az üzembe helyezés során, mert a kimenő IP-cím nincs definiálva ezen a ponton.

Először hozzon létre egy fürtöt az az [AK Create][az-aks-create] paranccsal. A következő példa egy *myAKSCluster* nevű egycsomópontos fürtöt hoz létre az *myResourceGroup*nevű erőforráscsoport-csoportban.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Fürt frissítése a jóváhagyott IP-tartományokkal

Alapértelmezés szerint a fürt a [standard SKU Load balancert][standard-sku-lb]használja, amely a kimenő átjáró konfigurálására használható. Használja az [az Network Public-IP List][az-network-public-ip-list] , és adja meg az AK-fürthöz tartozó erőforráscsoportot, amely általában a *MC_* -vel kezdődik. Ez a fürt nyilvános IP-címét jeleníti meg, amelyet engedélyezheti. A fürt IP-címének engedélyezéséhez használja az az [AK Update][az-aks-update] parancsot, és a *--API-Server-Allowed-IP-Ranges* paramétert kell megadnia. Példa:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Az API-kiszolgálók engedélyezett IP-címeinek engedélyezéséhez használja az [az AK Update][az-aks-update] parancsot, és adja meg az *--API-Server-engedélyezett-IP-címtartományok* paramétert, és adja meg az engedélyezett IP-címtartományok listáját. Ezek az IP-címtartományok általában a helyszíni hálózatok vagy a nyilvános IP-címek által használt címtartományok. CIDR-tartomány megadásakor a tartomány első IP-címével kezdjen el. Például a *137.117.106.90/29* egy érvényes tartomány, de győződjön meg arról, hogy az első IP-címet adta meg a tartományban, például *137.117.106.88/29*.

Az alábbi példa lehetővé teszi az API-kiszolgáló által a *myResourceGroup*nevű erőforráscsoport *myAKSCluster* nevű fürtön lévő, IP-címének engedélyezését. Az engedélyezett IP-címtartományok a következők: *172.0.0.0/16* (Pod/Nodes címtartomány) és *168.10.0.0/18* (ServiceCidr):

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Adja hozzá ezeket a tartományokat egy engedélyezési listához:
> - A tűzfal nyilvános IP-címe
> - A szolgáltatás CIDR
> - Az alhálózatok címtartománya, a csomópontok és a hüvelyek között
> - Bármely tartomány, amely azokat a hálózatokat képviseli, amelyekről a fürtöt felügyelni szeretné

## <a name="disable-authorized-ip-ranges"></a>A hitelesítő IP-címtartományok letiltása

Az engedélyezett IP-címtartományok letiltásához használja az [az AK Update][az-aks-update] parancsot, és az API-kiszolgáló által engedélyezett IP-címtartományok letiltásához használjon üres tartományt. Példa:

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
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
