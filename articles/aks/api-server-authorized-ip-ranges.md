---
title: API-kiszolgálóhoz engedélyezett IP-tartományokat az Azure Kubernetes Service (AKS)
description: A fürt IP-címtartomány használatával az Azure Kubernetes Service (AKS) API-kiszolgálóhoz való hozzáférés biztonságossá tételének ismertetése
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 9ec48c8ed924293a5ffea903fe03a9830dcd1184
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329420"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Előzetes verzió – az API kiszolgálóra történő biztonságos hozzáférés engedélyezett IP-címtartományok Azure Kubernetes Service (AKS)

A Kubernetes az API-kiszolgáló fogadja a műveleteket, mint például a fürt létre erőforrásokat, vagy méretezheti a csomópontok számát a kérések. Az API-kiszolgáló központi módja a kezelhetik és-fürtök kezeléséhez. Fürt biztonság növelése és minimalizálja a támadások, az API-kiszolgáló csak elérhetőnek kell lenniük az IP-címtartományok korlátozott számú.

Ez a cikk bemutatja, hogyan API jogosult kiszolgáló IP-címtartományok használatával korlátozza a vezérlősík kérelmeket. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók önkiszolgáló, a rendszer. A biztosított gyűjthet visszajelzéseket és a hibák kapcsolódóan a Közösség részéről. Előzetes verzióban elérhető ezeket a funkciókat nem üzemi használat céljára. Nyilvános előzetes verzióban érhető el "ajánlott beavatkozást" támogatás keretében tartoznak. Az AKS technikai támogatási csapat segítségét munkaidőben csendes-óceáni időzóna (PST) csak alatt érhető el. További információkért tekintse meg a következő cikkek támogatja:
>
> * [Az AKS támogatási házirendek][aks-support-policies]
> * [Az Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

API-kiszolgálóhoz engedélyezett IP-címtartományok csak az Ön által létrehozott új AKS-fürtök esetén működik. Ez a cikk bemutatja, hogyan hozhat létre egy AKS-fürtöt az Azure CLI használatával.

Az Azure CLI 2.0.61 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Az aks előzetes CLI-bővítmény telepítése

API jogosult kiszolgáló IP-címtartományok konfigurálásához lesz szüksége a *aks előzetes* CLI bővítmény verziója 0.4.1 vagy újabb verziója. Telepítse a *aks előzetes* Azure CLI-bővítmény használata a [az bővítmény hozzáadása][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] parancsot:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Funkció jelölője az előfizetés regisztrálása

API-t kiszolgáló engedélyezett IP-címtartományok használja, először engedélyeznie kell az előfizetés szolgáltatásjelzőre. Regisztrálja a *APIServerSecurityPreview* jelző funkciót, használja a [az a funkció regisztrálása][az-feature-register] parancsot az alábbi példában látható módon:

> [!CAUTION]
> A funkció egy adott előfizetés regisztrálásakor nem jelenleg regisztrációjának ezt a funkciót. Miután engedélyezte az egyes előzetes verziójú funkciók, alapértelmezett érték az összes AKS-fürt, majd az előfizetésben létrehozott használható. Nem engedélyezi az előzetes verziójú funkciók az éles üzemű előfizetéseket. Használjon különálló előfizetést előzetes verziójú funkciók teszteléséhez, és visszajelzést.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot használatával ellenőrizheti a [az szolgáltatáslistát][az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register][az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Korlátozások

A következő korlátozások vonatkoznak az API-t kiszolgáló engedélyezett IP-címtartományok konfigurálásakor:

* Az API-kiszolgálóhoz való kommunikáció is le van tiltva, az Azure fejlesztési tárolóhelyek jelenleg nem használható.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API-kiszolgáló áttekintése engedélyezett IP-címtartományok

A Kubernetes API-t kiszolgáló, hogyan érhetők el az alapul szolgáló Kubernetes API-kat. Ez az összetevő kommunikációt tesz lehetővé a felügyeleti eszközök, például `kubectl` vagy a Kubernetes irányítópultot. Az AKS egy egybérlős fürt főkiszolgáló, egy dedikált kiszolgálóval API-t biztosít. Alapértelmezés szerint az API-kiszolgáló nyilvános IP-cím van hozzárendelve, és a szerepköralapú hozzáférés-vezérlést (RBAC) hozzáférés felett Önnek kell rendelkeznie.

Az AKS ellenkező esetben a nyilvánosan elérhető vezérlősík való biztonságos hozzáférés / API-kiszolgálóhoz, engedélyezése és használata engedélyezett IP-címtartományok. Ezek engedélyezett IP-címtartományok engedélyezése csak az API-kiszolgálóval való kommunikációhoz megadott IP-címtartományok. Az API-kiszolgálóhoz nem tartozik ezek engedélyezett IP-címtartományok IP-címről érkező kérelem le van tiltva. Ön továbbra is az RBAC használatával, majd engedélyezze a felhasználók és az általuk kért műveleteket.

Az API-kiszolgáló és a fürt más összetevői kapcsolatos további információkért lásd: [Kubernetes alapvető fogalmait az aks-ben][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

API-t kiszolgáló engedélyezett IP-címtartományok csak munkahelyi új AKS-fürtök esetén. Engedélyezett IP-címtartományok nem engedélyezhető, mint a fürt létrehozásának műveletét. Ha megpróbálja engedélyezni a fürt részeként engedélyezett IP-címtartományok létrehozni a folyamatot, a fürtcsomópontok nem fér hozzá az API-kiszolgáló üzembe helyezése során a kimenő IP-cím ezen a ponton nem meghatározott csatlakoznak.

Először is hozzon létre egy fürtöt a [az aks létrehozása][az-aks-create] parancsot. A következő példában létrehozunk egy egy csomópontos fürtre nevű *myAKSCluster* az erőforráscsoport neve *myResourceGroup*.

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

## <a name="create-outbound-gateway-for-firewall-rules"></a>Átjáró kimenő tűzfalszabályok létrehozása

Győződjön meg arról, hogy a fürt csomópontjai megbízhatóan kommunikál az API-kiszolgáló a következő szakaszban engedélyezett IP-címtartományok engedélyezésekor, hozzon létre egy Azure tűzfal a kimenő átjáró használható. Az Azure-tűzfalon IP-címét a következő szakaszban a jogosult API-t kiszolgáló IP-címek listájára kerül.

> [!WARNING]
> Azure-tűzfal használatát is költségekkel jelentős feletti havi elszámolási időszakra. Azure tűzfalat használ, hogy csak erre szükség van az kezdeti előzetes verzió időszaka kell lennie. További információ és költség tervezési, [Azure tűzfal díjszabás][azure-firewall-costs].

Először kérje le a *MC_* az AKS-fürtöt, és a virtuális hálózatot az erőforráscsoport neve. Ezután hozzon létre egy alhálózatot a [az alhálózaton virtuális hálózat létrehozása][az-network-vnet-subnet-create] parancsot. A következő példában létrehozunk egy nevű alhálózatot *AzureFirewallSubnet* a CIDR-tartományt, a *10.200.0.0/16*:

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

Hozzon létre egy Azure-tűzfal, telepítse a *azure-tűzfal* CLI-bővítmény használata a [az bővítmény hozzáadása][az-extension-add] command. Then, create a firewall using the [az network firewall create][az-network-firewall-create] parancsot. Az alábbi példa létrehoz egy Azure-tűzfalon nevű *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Egy Azure-tűzfalon áthaladó adatátvitelen kimenő forgalomért nyilvános IP-cím van hozzárendelve. Hozzon létre egy nyilvános cím a [az network public-ip létrehozása][az-network-public-ip-create] command, then create an IP configuration on the firewall using the [az network firewall ip-config create][az-network-firewall-ip-config-create] , amely a nyilvános IP-cím vonatkozik:

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

Most már az Azure hálózati tűzfalszabály létrehozása az *engedélyezése* összes *TCP* használatával forgalmat a [az hálózati hálózati-tűzfalszabály létrehozása][az-network-firewall-network-rule-create] parancsot. A következő példában létrehozunk egy hálózati nevű szabályt *AllowTCPOutbound* a forgalmat bármilyen forrás vagy cél-címmel:

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

Az Azure-tűzfalon társítani a hálózati útvonal, szerezze be a meglévő útválasztási tábla adatai, az Azure-tűzfalon belső IP-címét, majd az API-kiszolgáló IP-címét. Ezen IP-címek vannak megadva a következő szakaszban szabályozhatja, hogyan kell-e a forgalom átirányítani a fürtkommunikációhoz.

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

Végül hozzon létre egy útvonalat a meglévő AKS hálózati útvonal tábla használatával a [az network route-table route létrehozása][az-network-route-table-route-create] parancsot, amely engedélyezi a forgalmat az Azure tűzfalkészülék használata az API-kiszolgáló kommunikációhoz.

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

Jegyezze fel az Azure-tűzfal berendezés nyilvános IP-címét. Ez a cím hozzáadódik API jogosult kiszolgáló IP-címtartományok a következő szakaszban a listában.

## <a name="enable-authorized-ip-ranges"></a>Engedélyezett IP-címtartományok engedélyezése

Ahhoz, hogy az API-t kiszolgáló engedélyezett IP-címtartományok, adja meg az engedélyezett IP-címtartományok listáját. Ha megad egy CIDR-tartományt, indítsa el a tartomány első IP-címét. Ha például *137.117.106.90/29* érvényes tartomány, de győződjön meg arról, hogy meg a tartomány első IP-címe például *137.117.106.88/29*.

Használat [az aks update][az-aks-update] parancsot, majd adja meg a *– api-server-engedélyezett – ip-címtartományok* engedélyezéséhez. Ezek az IP-címtartományok általában olyan címtartományt a helyszíni hálózat által használt. Adja hozzá a nyilvános IP-címét a saját Azure tűzfal, mint például az előző lépésben beszerzett *20.42.25.196/32*.

Az alábbi példa lehetővé teszi, hogy az API-t kiszolgáló engedélyezett IP-címtartományok nevű fürtöt *myAKSCluster* az erőforráscsoport neve *myResourceGroup*. Az IP-címtartományok engedélyezésére vannak *20.42.25.196/32* (az Azure-tűzfalon nyilvános IP-cím), majd *172.0.0.10/16* és *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Frissítse, vagy tiltsa le az engedélyezett IP-címtartományok

Frissítés, vagy tiltsa le az engedélyezett IP-címtartományok, újra használhatja [az aks update][az-aks-update] parancsot. Adja meg a frissített CIDR-tartományt szeretné engedélyezi, vagy adjon meg egy üres tartomány letiltásához API-kiszolgáló IP-címtartományok, jogosult, az alábbi példában látható módon:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben engedélyezte az API-t kiszolgáló engedélyezett IP-címtartományok. Ez a módszer hogyan futtathat egy biztonságos AKS-fürtöt egy részét képezi.

További információkért lásd: [biztonsággal kapcsolatos fogalmait, alkalmazások és-fürtök az aks-ben][concepts-security] and [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security].

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-list]: /cli/azure/extension#az-extension-list
[az-extension-update]: /cli/azure/extension#az-extension-update
