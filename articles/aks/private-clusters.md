---
title: Privát Azure Kubernetes Service-fürt
description: Ismerje meg, hogyan hozhat létre egy privát Azure Kubernetes Service-(ak-) fürtöt
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 8af0f998df2a92e51078a2e23806cca07ff08ca3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480085"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Nyilvános előzetes verzió – privát Azure Kubernetes Service-fürt

Egy privát fürtben a vezérlő síkja/API-kiszolgáló belső IP-címmel fog rendelkezni a [RFC1918](https://tools.ietf.org/html/rfc1918)-ben.  Privát fürt használatával biztosíthatja, hogy az API-kiszolgáló és a csomópont-készletek közötti hálózati forgalom csak a magánhálózaton maradjon.

A vezérlési sík/API-kiszolgáló, amely egy AK által felügyelt Azure-előfizetésben található, és az ügyfél-előfizetésben lévő ügyfelek fürt/csomópont-készlete képes kommunikálni egymással az API-kiszolgáló VNET található [privát kapcsolati szolgáltatáson][private-link-service] keresztül, valamint az ügyfél AK-fürt alhálózatában elérhető privát végpontok közötti kommunikációt.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok](support-policies.md)
> * [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előzetes teendők

* Szüksége lesz az Azure CLI 2.0.77 vagy újabb verziójára, valamint az AK-előnézet 0.4.18-bővítményre

## <a name="current-supported-regions"></a>Jelenleg támogatott régiók
* USA nyugati régiója
* USA 2. nyugati régiója
* USA 2. keleti régiója
* Közép-Kanada
* Észak-Európa
* Nyugat-Európa
* Ausztrália keleti régiója

## <a name="install-latest-aks-cli-preview-extension"></a>A legújabb AK CLI előnézet bővítmény telepítése

Privát fürtök használatához szüksége van a CLI *-előnézeti parancssori* felület 0.4.18 vagy újabb verziójára. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg az elérhető frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

A *regisztrált*állapot megjelenítéséhez több percet is igénybe vehet. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva van, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Privát AK-fürt létrehozása

#### <a name="default-basic-networking"></a>Alapértelmezett alapszintű hálózatkezelés 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Where--enable-Private-cluster kötelező jelző egy privát fürthöz 

#### <a name="advanced-networking"></a>Speciális hálózatkezelés  

```azurecli-interactive
az aks create \ 
    --resource-group <private-cluster-resource-group>\ 
    --name <private-cluster-name> \ 
    --load-balancer-sku standard
    --enable-private-cluster 
    --network-plugin azure \ 
    --vnet-subnet-id <subnet-id> \ 
    --docker-bridge-address 172.17.0.1/16 \ 
    --dns-service-ip 10.2.0.10 \ 
    --service-cidr 10.2.0.0/24 \ 
```
Where--enable-Private-cluster kötelező jelző egy privát fürthöz 

## <a name="steps-to-connect-to-the-private-cluster"></a>A privát fürthöz való kapcsolódás lépései
Az API-kiszolgáló végpontjának nincs nyilvános IP-címe. Ennek következtében a felhasználóknak létre kell hozniuk egy Azure-beli virtuális gépet egy virtuális hálózatban, és csatlakozniuk kell az API-kiszolgálóhoz. A következő lépései:

* Hitelesítő adatok beszerzése a fürthöz való kapcsolódáshoz

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Hozzon létre egy virtuális gépet ugyanabban a VNET, mint az AK-fürtöt, vagy hozzon létre egy virtuális gépet egy másik VNET, és egyenrangúan ez a VNET az AK-fürt VNET
* Ha egy másik VNET hoz létre virtuális gépet, be kell állítania egy hivatkozást a VNET és a saját DNS zóna között
    * Ugrás a MC_ * erőforráscsoporthoz a portálon 
    * kattintson a saját DNS zónára 
    * Válassza ki a virtuális hálózat hivatkozást a bal oldali panelen
    * hozzon létre egy új hivatkozást, amely hozzáadja a virtuális gép VNET a saját DNS zónához *(néhány percet vesz igénybe, amíg a DNS-zóna hivatkozása elérhetővé válik)*
* SSH a virtuális géphez
* A Kubectl eszköz telepítése és a Kubectl parancsok futtatása

## <a name="dependencies"></a>Függőségek  
* Csak standard LB – az alapszintű Load Balancer támogatása nem támogatott  

## <a name="limitations"></a>Korlátozások 
* Ugyanaz az [Azure Private link Service-korlátozás][private-link-service] vonatkozik a privát fürtökre, az Azure Private-végpontokra és a Virtual Network szolgáltatás-végpontokra jelenleg nem támogatott ugyanazon a VNET
* Egy privát fürtben lévő virtuális csomópontok nem támogatják a privát ACI-példányok kipörgetését egy privát Azure-VNET
* Nem támogatott az Azure DevOps integrációja a privát fürtökkel rendelkező dobozból
* Ha az ügyfeleknek engedélyezniük kell az ACR-t a privát AK-k használatával való együttműködéshez, az ACR VNET az ügynök fürt VNET kell összekapcsolni.
* Nincs aktuális támogatás az Azure dev Spaces szolgáltatáshoz
* Nem támogatott a meglévő AK-fürtök privát fürtökre való konvertálása  
* Ha törli vagy módosítja a magánhálózati végpontot az ügyfél alhálózatán, a fürt működése leáll. 
* A tárolók élő adatAzure Monitor jelenleg nem támogatottak
* Saját DNS használata jelenleg nem támogatott


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
