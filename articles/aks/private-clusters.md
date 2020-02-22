---
title: Privát Azure Kubernetes Service-fürt létrehozása
description: Ismerje meg, hogyan hozhat létre egy privát Azure Kubernetes Service-(ak-) fürtöt
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 2/21/2020
ms.author: mlearned
ms.openlocfilehash: 08929d5ec05fbeb80eddebfd667fe1e0fde9bff7
ms.sourcegitcommit: 78f367310e243380b591ff10f2500feca93f5d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77544232"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Privát Azure Kubernetes Service-fürt létrehozása (előzetes verzió)

Egy privát fürtben a vezérlő síkja vagy az API-kiszolgáló belső IP-címekkel rendelkezik, amelyek a [magánhálózati internetes dokumentumok RFC1918](https://tools.ietf.org/html/rfc1918) vannak meghatározva. Privát fürt használatával biztosíthatja, hogy az API-kiszolgáló és a csomópont-készletek közötti hálózati forgalom csak a magánhálózaton maradjon.

A vezérlő síkja vagy az API-kiszolgáló egy Azure Kubernetes szolgáltatásban (ak) felügyelt Azure-előfizetésben található. Az ügyfél fürtje vagy csomópont-készlete az ügyfél előfizetésében található. A kiszolgáló és a fürt vagy a csomópont-készlet képes kommunikálni egymással az API-kiszolgáló virtuális hálózatának [Azure Private link szolgáltatásával][private-link-service] , valamint egy olyan privát végponttal, amely az ügyfél AK-fürt alhálózatán van kitéve.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló szolgáltatás, és a rendszer opt-alapon is elérhető. Az előzetes verziók az elérhető *módon* és a *rendelkezésre álló módon érhetők el* , és ki vannak zárva a szolgáltatói szerződéssel (SLA) és a korlátozott jótállással. A kétrészes előzetes verziókat az ügyfélszolgálat a *lehető legalkalmasabb* módon kezeli. A funkciók ezért nem használhatók éles környezetben. További információkért lásd a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok](support-policies.md)
> * [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI 2.0.77-es vagy újabb verziója, valamint az Azure CLI-bővítmény előzetes verziója 0.4.18

## <a name="currently-supported-regions"></a>Jelenleg támogatott régiók

* Kelet-Ausztrália
* Délkelet-Ausztrália
* Dél-Brazília
* Közép-Kanada
* Kelet-Kanada
* Cenral minket
* Kelet-Ázsia
* USA keleti régiója
* USA 2. keleti régiója
* USA 2. keleti – EUAP
* Közép-Franciaország
* Észak-Németország
* Kelet-Japán
* Nyugat-Japán
* Dél-Korea középső régiója
* Dél-Korea déli régiója
* USA északi középső régiója
* Észak-Európa
* Észak-Európa
* USA déli középső régiója
* Az Egyesült Királyság déli régiója
* Nyugat-Európa
* USA nyugati régiója
* USA nyugati régiója, 2.
* USA 2. keleti régiója


## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Az Azure CLI legújabb előzetes verziójának telepítése

A privát fürtök használatához szüksége lesz az Azure CLI-bővítmény előzetes verziójának 0.4.18 vagy újabb verziójára. Telepítse az Azure CLI AK előzetes verzióját az az [Extension Add][az-extension-add] paranccsal, majd a következő az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az előfizetésben létrehozott összes AK-fürthöz alapértelmezett beállításokat használhat. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg a regisztrációs állapot *regisztrálva*jelenik meg. Az állapotot a következő az [Feature List][az-feature-list] parancs használatával végezheti el:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva van, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját a következő az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Privát AK-fürt létrehozása

### <a name="default-basic-networking"></a>Alapértelmezett alapszintű hálózatkezelés 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Where *--enable-Private-cluster* kötelező jelző egy privát fürthöz. 

### <a name="advanced-networking"></a>Speciális hálózatkezelés  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Where *--enable-Private-cluster* kötelező jelző egy privát fürthöz. 

> [!NOTE]
> Ha a Docker-híd CIDR (172.17.0.1/16) ütközne az alhálózati CIDR, módosítsa a Docker-híd megfelelőjét.

## <a name="connect-to-the-private-cluster"></a>Kapcsolódás a privát fürthöz
Az API-kiszolgáló végpontjának nincs nyilvános IP-címe. Ennek következtében létre kell hoznia egy Azure-beli virtuális gépet (VM) egy virtuális hálózatban, és kapcsolódnia kell az API-kiszolgálóhoz. Ehhez tegye a következőket:

1. Hitelesítő adatok beszerzése a fürthöz való kapcsolódáshoz.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. A következő lehetőségek közül választhat:
   * Hozzon létre egy virtuális GÉPET ugyanabban a virtuális hálózatban, mint az AK-fürtöt.  
   * Hozzon létre egy virtuális GÉPET egy másik virtuális hálózatban, és a virtuális hálózatot az AK-fürt virtuális hálózatával.

     Ha egy másik virtuális hálózatban hoz létre egy virtuális GÉPET, állítson be egy hivatkozást a virtuális hálózat és a magánhálózati DNS-zóna között. Ehhez tegye a következőket:
    
     a. Lépjen a Azure Portal MC_ * erőforráscsoporthoz.  
     b. Válassza ki a magánhálózati DNS-zónát.   
     c. A bal oldali ablaktáblán válassza ki a **virtuális hálózati** kapcsolatot.  
     d. Hozzon létre egy új hivatkozást, amely hozzáadja a virtuális gép virtuális hálózatát a magánhálózati DNS-zónához. Néhány percet vesz igénybe, amíg a DNS-zóna hivatkozása elérhetővé válik.  
     e. Térjen vissza a Azure Portal MC_ * erőforráscsoporthoz.  
     f. A jobb oldali ablaktáblában válassza ki a virtuális hálózatot. A virtuális hálózat neve: *AK-vnet-\** .  
     g. A bal oldali ablaktáblán válassza **a**társítások lehetőséget.  
     h. Válassza a **Hozzáadás**lehetőséget, adja hozzá a virtuális gép virtuális hálózatát, majd hozza létre a társítást.  
     i. Nyissa meg a virtuális hálózatot, ahol a virtuális gép rendelkezik **, válassza a társítások lehetőséget,** válassza ki az AK-beli virtuális hálózatot, majd hozza létre a társítást. Ha a címtartomány az AK-beli virtuális hálózaton és a virtuális gép virtuális hálózatának összevonásán alapul, a társítás sikertelen lesz. További információ: [Virtual Network peering][virtual-network-peering].

1. A virtuális gép a Secure Shell (SSH) használatával érhető el.
1. Telepítse a Kubectl eszközt, és futtassa a Kubectl-parancsokat.


## <a name="dependencies"></a>Függőségek  
* A Private link Service csak a standard Azure Load Balancer esetén támogatott. Az alapszintű Azure Load Balancer nem támogatott.  
* Ha egyéni DNS-kiszolgálót szeretne használni, telepítsen egy DNS-t használó AD-kiszolgálót az IP-168.63.129.16 való továbbításhoz

## <a name="limitations"></a>Korlátozások 
* Availability Zones jelenleg csak az USA 2. keleti régiójában és az USA 2. nyugati régiójában támogatott
* Az [Azure Private link Service korlátozásai][private-link-service] a privát fürtökre, az Azure privát végpontokra és a virtuális hálózati szolgáltatás-végpontokra vonatkoznak, amelyek jelenleg nem támogatottak ugyanabban a virtuális hálózatban.
* A privát fürtben lévő virtuális csomópontok nem támogatják a privát Azure-beli virtuális hálózatokon lévő Azure Container Instances (ACI) használatát
* Nem támogatott az Azure DevOps integrációja a privát fürtökkel rendelkező dobozból
* Azon ügyfelek számára, akik számára engedélyezni kell a Azure Container Registryt a privát AK-val való munkavégzéshez, az Container Registry virtuális hálózatot az ügynök-fürt virtuális hálózatának kell megadnia.
* Nincs aktuális támogatás az Azure dev Spaces szolgáltatáshoz
* Meglévő AK-fürtök privát fürtökre való konvertálása nem támogatott
* Ha törli vagy módosítja a magánhálózati végpontot az ügyfél alhálózatán, a fürt működése leáll. 
* A tárolók élő adatAzure Monitor jelenleg nem támogatottak.
* *A saját DNS* használata jelenleg nem támogatott.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

