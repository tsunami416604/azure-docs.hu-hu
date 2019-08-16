---
title: Az API-kiszolgáló által jóváhagyott IP-címtartományok az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan biztonságossá teheti a fürtöt IP-címtartomány használatával az Azure Kubernetes szolgáltatásban (ak) lévő API-kiszolgálóhoz való hozzáféréshez
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: fe0c9d7e870b56bf83b70845af9159ea0703c4ab
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533626"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Előzetes verzió – biztonságos hozzáférés az API-kiszolgálóhoz az Azure Kubernetes szolgáltatásban (ak) lévő, jogosult IP-címtartományok használatával

A Kubernetes-ben az API-kiszolgáló kérelmeket fogad a fürt műveleteinek végrehajtásához, például erőforrások létrehozásához vagy a csomópontok számának skálázásához. Az API-kiszolgáló a fürttel való kommunikáció és felügyelet központi módja. A fürt biztonságának növelése és a támadások csökkentése érdekében az API-kiszolgálónak csak korlátozott IP-címtartományok közül kell elérhetőnek lennie.

Ez a cikk bemutatja, hogyan használható az API-kiszolgáló által engedélyezett IP-címtartományok a sík vezérlésére irányuló kérések korlátozására. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Az API-kiszolgáló által jóváhagyott IP-címtartományok csak a létrehozott új AK-fürtök esetében működnek. Ez a cikk bemutatja, hogyan hozhat létre egy AK-fürtöt az Azure CLI használatával.

Szüksége lesz az Azure CLI-verzió 2.0.61 vagy újabb verziójára, és konfigurálva van. A `az --version` verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

Az API-kiszolgáló által jóváhagyott IP-címtartományok konfigurálásához a CLI *-előnézet CLI-* bővítményének 0.4.1 vagy újabb verziójára van szükség. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Az előfizetéshez tartozó szolgáltatás-jelölő regisztrálása

Az API-kiszolgáló által engedélyezett IP-címtartományok használatához először engedélyezze a szolgáltatás jelölőjét az előfizetésében. A *APIServerSecurityPreview* szolgáltatás jelzőjét a következő példában látható módon regisztrálja az az [Feature Register][az-feature-register] parancs használatával:

> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Korlátozások

Az API-kiszolgáló által jóváhagyott IP-címtartományok konfigurálásakor a következő korlátozások érvényesek:

* Jelenleg nem használható az Azure dev Spaces, mert az API-kiszolgálóval folytatott kommunikáció is le van tiltva.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Az API-kiszolgáló által jóváhagyott IP-címtartományok áttekintése

A Kubernetes API-kiszolgáló az alapul szolgáló Kubernetes API-k számára elérhető. Ez az összetevő a felügyeleti eszközök, például `kubectl` a vagy a Kubernetes irányítópultjának interakcióját biztosítja. Az AK egybérlős fürtös főkiszolgálót biztosít dedikált API-kiszolgálóval. Alapértelmezés szerint az API-kiszolgáló nyilvános IP-címet kap, és a hozzáférés vezérlése szerepköralapú hozzáférés-vezérléssel (RBAC) történik.

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
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Kimenő átjáró létrehozása a tűzfalszabályok számára

Annak biztosítása érdekében, hogy a fürt csomópontjai megbízhatóan kommunikáljanak az API-kiszolgálóval, amikor engedélyezte az engedélyezett IP-tartományokat a következő szakaszban, hozzon létre egy Azure-tűzfalat a kimenő átjáróként való használatra. Az Azure tűzfal IP-címét a rendszer Ezután hozzáadja a hitelesítő API-kiszolgálók IP-címeinek listájához a következő szakaszban.

> [!WARNING]
> A Azure Firewall használata jelentős költségeket eredményezhet a havi elszámolási időszakra vonatkozóan. A Azure Firewall használatára vonatkozó követelmény csak ebben a kezdeti előnézeti időszakban szükséges. További információ és Cost-tervezés: [Azure Firewall díjszabása][azure-firewall-costs].
>
> Ha a fürt [szabványos SKU Load balancert][standard-sku-lb]használ, akkor nem kell konfigurálnia a Azure Firewall a kimenő átjáróként. Használja az [az Network Public-IP List][az-network-public-ip-list] , és adja meg az AK-fürthöz tartozó erőforráscsoportot, amely általában a *MC_* -vel kezdődik. Ez a fürt nyilvános IP-címét jeleníti meg, amelyet engedélyezheti. Példa:
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

Először kérje le a *MC_* erőforráscsoport nevét az AK-fürthöz és a virtuális hálózathoz. Ezután hozzon létre egy alhálózatot az az [Network vnet subnet Create][az-network-vnet-subnet-create] paranccsal. A következő példában létrehozunk egy *AzureFirewallSubnet* nevű alhálózatot a *10.200.0.0/16*CIDR tartományba:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Azure Firewall létrehozásához telepítse az *Azure-Firewall* CLI bővítményt az az [Extension Add][az-extension-add] paranccsal. Ezután hozzon létre egy tűzfalat az az [Network Firewall Create][az-network-firewall-create] paranccsal. A következő példa egy *myAzureFirewall*nevű Azure-tűzfalat hoz létre:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Az Azure-beli tűzfal olyan nyilvános IP-címet kap, amely a forgalom felé áramlik. Hozzon létre egy nyilvános címet az az [Network Public-IP Create][az-network-public-ip-create] paranccsal, majd hozzon létre egy IP-konfigurációt a tűzfalon az az [Network Firewall IP-config Create][az-network-firewall-ip-config-create] parancs használatával, amely a nyilvános IP-címet alkalmazza:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Most hozza létre az Azure tűzfal hálózati szabályát az összes *TCP* -forgalom *engedélyezéséhez* az az [Network Firewall Network-Rule Create][az-network-firewall-network-rule-create] paranccsal. A következő példa egy *AllowTCPOutbound* nevű hálózati szabályt hoz létre bármilyen forrás-vagy célcím esetén:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Az Azure tűzfal hálózati útvonallal való hozzárendeléséhez szerezze be a meglévő útválasztási táblázat adatait, az Azure tűzfal belső IP-címét, majd az API-kiszolgáló IP-címét. Ezek az IP-címek a következő szakaszban vannak megadva annak szabályozásához, hogy a rendszer hogyan irányítsa át a forgalmat a fürt kommunikációjához.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Végül hozzon létre egy útvonalat a meglévő AK hálózati útválasztási táblában az az [Network Route-Table Route Create][az-network-route-table-route-create] paranccsal, amely lehetővé teszi, hogy a forgalom az Azure tűzfal berendezését használja az API Server-kommunikációhoz.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Jegyezze fel a Azure Firewall készülék nyilvános IP-címét. Ezt a címet a következő szakaszban található API-kiszolgáló által jóváhagyott IP-címtartományok listájához adja hozzá a rendszer.

## <a name="enable-authorized-ip-ranges"></a>Engedélyezett IP-címtartományok engedélyezése

Az API-kiszolgáló által engedélyezett IP-címtartományok engedélyezéséhez meg kell adnia az engedélyezett IP-címtartományok listáját. CIDR-tartomány megadásakor a tartomány első IP-címével kezdjen el. Például a *137.117.106.90/29* egy érvényes tartomány, de győződjön meg arról, hogy az első IP-címet adta meg a tartományban, például *137.117.106.88/29*.

Használja az [az AK Update][az-aks-update] parancsot, és határozza meg a *--API-Server-engedélyezett-IP-tartományokat* az engedélyezéshez. Ezek az IP-címtartományok általában a helyszíni hálózatok által használt címtartományok. Adja hozzá az előző lépésben beszerzett saját Azure-tűzfal nyilvános IP-címét, például *20.42.25.196/32*.

Az alábbi példa lehetővé teszi az API-kiszolgáló által a *myResourceGroup*nevű erőforráscsoport *myAKSCluster* nevű fürtön lévő, IP-címének engedélyezését. Az engedélyezett IP-címtartományok az *20.42.25.196/32* (az Azure tűzfal nyilvános IP-címe), majd a *172.0.0.0/16* és a *168.10.0.0/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>A hitelesítő IP-címtartományok frissítése vagy letiltása

A jóváhagyott IP-címtartományok frissítéséhez vagy letiltásához használja újra [az az AK Update][az-aks-update] parancsot. Válassza ki az engedélyezni kívánt frissített CIDR, vagy egy üres tartományt, amely letiltja az API-kiszolgáló által engedélyezett IP-tartományokat, az alábbi példában látható módon:

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
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md
