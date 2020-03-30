---
title: Hozzáférés korlátozása virtuális hálózattal
description: Az Azure-tároló beállításjegyzékéhez csak az Azure virtuális hálózat erőforrásaiból vagy nyilvános IP-címtartományokból engedélyezheti a hozzáférést.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454328"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Azure-tároló beállításjegyzékhez való hozzáférés korlátozása Azure virtuális hálózati vagy tűzfalszabályok használatával

[Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos, privát hálózatot biztosít az Azure-beli és helyszíni erőforrásokszámára. A privát Azure-tároló beállításjegyzék-hozzáférés egy Azure virtuális hálózat, biztosítja, hogy csak a virtuális hálózat erőforrásait a beállításjegyzékben. A létesítmények közötti forgatókönyvek, tűzfalszabályok at is konfigurálhat, hogy a rendszerleíró adatbázis csak adott IP-címekről való hozzáférést.

Ez a cikk két forgatókönyvet jelenít meg a bejövő hálózati hozzáférési szabályok konfigurálásához egy tároló beállításjegyzékében: egy virtuális hálózatban telepített virtuális gépről vagy egy virtuális gép nyilvános IP-címéről.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

Ha ehelyett hozzáférési szabályokat kell beállítania az erőforrásokhoz, hogy egy tárolóbeállítás-jegyzéket tűzfal mögül érjenek el, olvassa el a [Szabályok konfigurálása az Azure-tároló beállításjegyzékének tűzfal mögötti eléréséhez](container-registry-firewall-access-rules.md)című témakört.


## <a name="preview-limitations"></a>Előnézeti korlátozások

* Csak egy **prémium szintű** tároló beállításjegyzék konfigurálható hálózati hozzáférési szabályokkal. A beállításjegyzék-szolgáltatási szintekről az [Azure Container Registry ska](container-registry-skus.md)című témakörben talál további információt. 

* Csak egy [Azure Kubernetes service-fürt](../aks/intro-kubernetes.md) vagy az Azure [virtuális gép](../virtual-machines/linux/overview.md) üzemelteti a tároló beállításjegyzék egy virtuális hálózatban. *Más Azure-szolgáltatások, beleértve az Azure Container-példányok jelenleg nem támogatottak.*

* [Az ACR-feladatok](container-registry-tasks-overview.md) műveletei jelenleg nem támogatottak a virtuális hálózatban elérhető tárolóbeállításjegyzékben.

* Minden beállításjegyzék legfeljebb 100 virtuális hálózati szabályt támogat.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI ebben a cikkben az Azure CLI 2.0.58-as vagy újabb verziója szükséges. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

* Ha még nem rendelkezik egy tároló beállításjegyzékkel, hozzon létre egyet (prémium termékváltozat szükséges), és leküldéses egy mintarendszerkép, például `hello-world` a Docker Hub. Például az [Azure Portalon][quickstart-portal] vagy az [Azure CLI-ben][quickstart-cli] hozzon létre egy beállításjegyzéket. 

* Ha azt szeretné, hogy korlátozza a beállításjegyzék-hozzáférés egy másik Azure-előfizetésvirtuális hálózat használatával, regisztrálnia kell az erőforrás-szolgáltató az Azure Container Registry az adott előfizetésben. Példa:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Tárolóbeállításjegyzék hálózati szabályai

Az Azure-tároló beállításjegyzék alapértelmezés szerint elfogadja a kapcsolatot az interneten keresztül a házigazdák bármely hálózaton. Egy virtuális hálózat, engedélyezheti, hogy csak az Azure-erőforrások, például egy AKS-fürt vagy az Azure virtuális gép biztonságosan hozzáférhet a beállításjegyzék, anélkül, hogy átlépi a hálózati határt. A hálózati tűzfalszabályokat úgy is beállíthatja, hogy csak bizonyos nyilvános internetes IP-címtartományokat engedélyezzenek. 

A beállításjegyzékhez való hozzáférés korlátozásához először módosítsa a beállításjegyzék alapértelmezett műveletét, hogy az megtagadja az összes hálózati kapcsolatot. Ezután adja hozzá a hálózati hozzáférési szabályokat. A hálózati szabályokon keresztül hozzáféréssel rendelkező ügyfeleknek továbbra is [hitelesíteniük kell magukat a tárolóbeállításjegyzékben,](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) és engedélyezniük kell az adatok elérését.

### <a name="service-endpoint-for-subnets"></a>Az alhálózatok szolgáltatásvégpontja

A virtuális hálózat alhálózatáról való hozzáférés engedélyezéséhez hozzá kell adnia egy [szolgáltatásvégpontot](../virtual-network/virtual-network-service-endpoints-overview.md) az Azure Container Registry szolgáltatáshoz. 

A több-bérlős szolgáltatások, például az Azure Container Registry, az összes ügyfél számára egyetlen IP-címkészletet használnak. A szolgáltatásvégpont végpontot rendel a beállításjegyzék eléréséhez. Ez a végpont biztosítja a forgalom optimális útvonalat az erőforráshoz az Azure gerinchálózaton keresztül. A virtuális hálózat és az alhálózat identitásait is továbbítja az egyes kérelmek.

### <a name="firewall-rules"></a>Tűzfalszabályok

Az IP-hálózati szabályok esetében adja meg az engedélyezett internetcímtartományokat CIDR jelöléssel, például *16.17.18.0/24* vagy egyéni IP-címekkel, például *16.17.18.19.* Az IP-hálózati szabályok csak *nyilvános* internetes IP-címek esetén engedélyezettek. A magánhálózatok számára fenntartott IP-címtartományok (az 1918-as RFC-ben meghatározottak szerint) nem engedélyezettek az IP-szabályokban.

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker-kompatibilis virtuális gép létrehozása

Ebben a cikkben egy Docker-kompatibilis Ubuntu virtuális gép használatával érheti el az Azure-tároló beállításjegyzék. Az Azure Active Directory-hitelesítés a beállításjegyzékben, telepítse az [Azure CLI][azure-cli] a virtuális gépre. Ha már rendelkezik egy Azure virtuális géppel, hagyja ki ezt a létrehozási lépést.

Használhatja ugyanazt az erőforráscsoportot a virtuális gép és a tároló beállításjegyzék. Ez a beállítás leegyszerűsíti a karbantartást a végén, de nem szükséges. Ha úgy dönt, hogy külön erőforráscsoportot hoz létre a virtuális géphez és a virtuális hálózathoz, futtassa [az az csoport létrehozása parancsot.][az-group-create] A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westcentralus* helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

Most üzembe helyezhet egy alapértelmezett Ubuntu Azure virtuális gépet [az az vm create][az-vm-create]segítségével. A következő példa létrehoz egy *myDockerVM*nevű virtuális gép:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Amikor a parancs befejeződik, `publicIpAddress` vegye figyelembe az Azure CLI által megjelenített. Ezzel a címmel SSH-kapcsolatokat létesítanek a virtuális géppel, és szükség esetén a tűzfalszabályok későbbi beállításához.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépre

A virtuális gép futtatása után, hogy egy SSH-kapcsolat a virtuális gép. Cserélje le *a nyilvános IpAddress-t* a virtuális gép nyilvános IP-címére.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsot a Docker telepítéséhez az Ubuntu VM-en:

```bash
sudo apt install docker.io -y
```

A telepítés után futtassa a következő parancsot annak ellenőrzéséhez, hogy a Docker megfelelően fut-e a virtuális számítógépen:

```bash
sudo docker run -it hello-world
```

Kimenet:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Kövesse az [Azure CLI telepítése apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) az Azure CLI telepítéséhez az Ubuntu virtuális gépen. Ebben a cikkben győződjön meg arról, hogy a 2.0.58-as vagy újabb verziót telepíti.

Lépjen ki az SSH-kapcsolatból.

## <a name="allow-access-from-a-virtual-network"></a>Hozzáférés engedélyezése virtuális hálózatról

Ebben a szakaszban konfigurálja a tároló beállításjegyzékét, hogy engedélyezze a hozzáférést egy Azure virtuális hálózat alhálózatáról. Az Azure CLI és az Azure Portal használatával egyenértékű lépések állnak rendelkezésre.

### <a name="allow-access-from-a-virtual-network---cli"></a>Hozzáférés engedélyezése virtuális hálózatról – CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Szolgáltatásvégpont hozzáadása alhálózathoz

Virtuális gép létrehozásakor az Azure alapértelmezés szerint létrehoz egy virtuális hálózatot ugyanabban az erőforráscsoportban. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gépet *myDockerVM-nek*nevezi el, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, *egy myDockerVMSubnet*nevű alhálózattal. Ellenőrizze ezt az Azure Portalon vagy az [az hálózati virtuális hálózat lista][az-network-vnet-list] parancshasználatával:

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Kimenet:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

A **Microsoft.ContainerRegistry** szolgáltatás végpontjának hozzáadásához használja az [az hálózati virtuálishálózat alhálózati frissítési][az-network-vnet-subnet-update] parancsát. Helyettesítse a virtuális hálózat és az alhálózat nevét a következő parancsban:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Használja az [az hálózati virtuálishálózat-megjelenítési][az-network-vnet-subnet-show] parancsot az alhálózat erőforrás-azonosítójának lekéréséhez. Erre egy későbbi lépésben van szükség a hálózati hozzáférési szabály konfigurálásához.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Kimenet:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>A rendszerleíró adatbázis alapértelmezett hálózati hozzáférésének módosítása

Alapértelmezés szerint az Azure-tároló beállításjegyzék lehetővé teszi a kapcsolatot a gazdagépek bármely hálózaton. A kijelölt hálózathoz való hozzáférés korlátozásához módosítsa az alapértelmezett műveletet a hozzáférés megtagadására. Helyettesítse a rendszerleíró adatbázis nevét a következő [az acr update][az-acr-update] parancsban:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

Az [az acr hálózati szabály hozzáadása][az-acr-network-rule-add] paranccsal hálózati szabályt adhat a rendszerleíró adatbázishoz, amely lehetővé teszi a hozzáférést a virtuális gép alhálózatáról. Helyettesítse a tárolóbeállítás-jegyzék nevét és az alhálózat erőforrás-azonosítóját a következő parancsban: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Folytassa [a rendszerleíró adatbázishoz való hozzáférés ellenőrzését.](#verify-access-to-the-registry)

### <a name="allow-access-from-a-virtual-network---portal"></a>Hozzáférés engedélyezése virtuális hálózatról - portál

#### <a name="add-service-endpoint-to-subnet"></a>Szolgáltatásvégpont hozzáadása az alhálózathoz

Virtuális gép létrehozásakor az Azure alapértelmezés szerint létrehoz egy virtuális hálózatot ugyanabban az erőforráscsoportban. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gépet *myDockerVM-nek*nevezi el, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, *egy myDockerVMSubnet*nevű alhálózattal.

Az Azure Container Registry szolgáltatásvégpontjának hozzáadása egy alhálózathoz:

1. Az [Azure Portal][azure-portal]tetején lévő keresőmezőbe írja be a *virtuális hálózatok*kifejezést. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények között, jelölje ki azt.
1. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amelyen a virtuális gép telepítve van, például *a myDockerVMVNET.*
1. A **Beállítások csoportban**válassza **az Alhálózatok**lehetőséget.
1. Válassza ki azt az alhálózatot, ahol a virtuális gép telepítve van, például *a myDockerVMSubnet.*
1. A **Szolgáltatásvégpontok csoportban**válassza a **Microsoft.ContainerRegistry**lehetőséget.
1. Kattintson a **Mentés** gombra.

![Szolgáltatásvégpont hozzáadása az alhálózathoz][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Hálózati hozzáférés konfigurálása a beállításjegyzékhez

Alapértelmezés szerint az Azure-tároló beállításjegyzék lehetővé teszi a kapcsolatot a gazdagépek bármely hálózaton. A virtuális hálózathoz való hozzáférés korlátozása:

1. A portálon keresse meg a tároló beállításjegyzékét.
1. A **Beállítások csoportban**válassza a **Tűzfal és a virtuális hálózatok**lehetőséget.
1. Ha alapértelmezés szerint meg szeretné tagadni a hozzáférést, válassza a **kijelölt hálózatokról**való hozzáférést. 
1. Válassza **a Meglévő virtuális hálózat hozzáadása**lehetőséget, és válassza ki a szolgáltatásvégpontdal konfigurált virtuális hálózatot és alhálózatot. Válassza a **Hozzáadás** lehetőséget.
1. Kattintson a **Mentés** gombra.

![Virtuális hálózat konfigurálása a tároló beállításjegyzékéhez][acr-vnet-portal]

Folytassa [a rendszerleíró adatbázishoz való hozzáférés ellenőrzését.](#verify-access-to-the-registry)

## <a name="allow-access-from-an-ip-address"></a>Hozzáférés engedélyezése IP-címről

Ebben a szakaszban konfigurálja a tároló beállításjegyzékét úgy, hogy egy adott IP-címről vagy tartományból való hozzáférés engedélyezése. Az Azure CLI és az Azure Portal használatával egyenértékű lépések állnak rendelkezésre.

### <a name="allow-access-from-an-ip-address---cli"></a>Hozzáférés engedélyezése IP-címről - CLI

#### <a name="change-default-network-access-to-registry"></a>A rendszerleíró adatbázis alapértelmezett hálózati hozzáférésének módosítása

Ha még nem tette meg, frissítse a beállításjegyzék konfigurációját, hogy alapértelmezés szerint megtagadja a hozzáférést. Helyettesítse a rendszerleíró adatbázis nevét a következő [az acr update][az-acr-update] parancsban:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Hálózati szabály eltávolítása a rendszerleíró adatbázisból

Ha korábban hálózati szabályt adott hozzá a virtuális gép alhálózatáról való hozzáférés engedélyezéséhez, távolítsa el az alhálózat szolgáltatásvégpontját és a hálózati szabályt. Helyettesítse a tárolóbeállítás-jegyzék nevét és az [az acr hálózati szabály eltávolítási][az-acr-network-rule-remove] parancs egy korábbi lépésében beolvasott alhálózat erőforrás-azonosítóját: 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

Az [az acr network-rule add][az-acr-network-rule-add] paranccsal hálózati szabályt adhat a rendszerleíró adatbázishoz, amely lehetővé teszi a hozzáférést a virtuális gép IP-címéről. Helyettesítse a tároló beállításjegyzék nevét és a virtuális gép nyilvános IP-címét a következő parancsban.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Folytassa [a rendszerleíró adatbázishoz való hozzáférés ellenőrzését.](#verify-access-to-the-registry)

### <a name="allow-access-from-an-ip-address---portal"></a>Hozzáférés engedélyezése IP-címről - portálról

#### <a name="remove-existing-network-rule-from-registry"></a>Meglévő hálózati szabály eltávolítása a rendszerleíró adatbázisból

Ha korábban hálózati szabályt adott hozzá a virtuális gép alhálózatáról való hozzáférés engedélyezéséhez, távolítsa el a meglévő szabályt. Hagyja ki ezt a szakaszt, ha egy másik virtuális gépről szeretné elérni a rendszerleíró adatbázist.

* Frissítse az alhálózati beállításokat az azure container registry szolgáltatásvégpontjának eltávolításához. 

  1. Az [Azure Portalon][azure-portal]keresse meg azt a virtuális hálózatot, ahol a virtuális gép telepítve van.
  1. A **Beállítások csoportban**válassza **az Alhálózatok**lehetőséget.
  1. Válassza ki azt az alhálózatot, ahol a virtuális gép telepítve van.
  1. A **Szolgáltatásvégpontok csoportban**távolítsa el a **Microsoft.ContainerRegistry**jelölőnégyzetét. 
  1. Kattintson a **Mentés** gombra.

* Távolítsa el azt a hálózati szabályt, amely lehetővé teszi, hogy az alhálózat hozzáférjen a rendszerleíró adatbázishoz.

  1. A portálon keresse meg a tároló beállításjegyzékét.
  1. A **Beállítások csoportban**válassza a **Tűzfal és a virtuális hálózatok**lehetőséget.
  1. A **Virtuális hálózatok**csoportban jelölje ki a virtuális hálózat nevét, majd válassza az **Eltávolítás**lehetőséget.
  1. Kattintson a **Mentés** gombra.

#### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

1. A portálon keresse meg a tároló beállításjegyzékét.
1. A **Beállítások csoportban**válassza a **Tűzfal és a virtuális hálózatok**lehetőséget.
1. Ha még nem tette meg, engedélyezze a hozzáférést a **Kiválasztott hálózatokról.** 
1. A **Virtuális hálózatok csoportban**győződjön meg arról, hogy nincs hálózat kijelölve.
1. A **Tűzfal**csoportban adja meg a virtuális gép nyilvános IP-címét. Vagy adjon meg egy címtartományt cidr jelöléssel, amely tartalmazza a virtuális gép IP-címét.
1. Kattintson a **Mentés** gombra.

![Tűzfalszabály konfigurálása a tárolóbeállításjegyzékhez][acr-vnet-firewall-portal]

Folytassa [a rendszerleíró adatbázishoz való hozzáférés ellenőrzését.](#verify-access-to-the-registry)

## <a name="verify-access-to-the-registry"></a>A beállításjegyzékhez való hozzáférés ellenőrzése

Várakozás után néhány percet a konfiguráció frissítése, ellenőrizze, hogy a virtuális gép hozzáférhet a tároló beállításjegyzék. SSH-kapcsolatot létesítsen a virtuális géppel, és futtassa az [acr bejelentkezési][az-acr-login] parancsot a rendszerleíró adatbázisba való bejelentkezéshez. 

```bash
az acr login --name mycontainerregistry
```

A beállításjegyzék-műveleteket, `docker pull` például a futtatást futtathatja, hogy lehúzzon egy mintalemezképet a rendszerleíró adatbázisból. A rendszerleíró adatbázisnak megfelelő lemezkép- és címkeérték helyettesítése, amely a rendszerleíró adatbázis bejelentkezési kiszolgálójának nevével (az összes kisbetűvel) van megjelölve:

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

A Docker sikeresen lekéri a lemezképet a virtuális gépre.

Ez a példa bemutatja, hogy a hálózati hozzáférési szabályon keresztül elérheti a magántároló beállításjegyzékét. A beállításjegyzék azonban nem érhető el egy másik bejelentkezési állomásról, amely nem rendelkezik konfigurált hálózati hozzáférési szabállyal. Ha egy másik állomásról próbál `az acr login` bejelentkezni `docker login` a paranccsal vagy paranccsal, a kimenet a következőhöz hasonló:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása

Ha vissza szeretné állítani a rendszerleíró adatbázist, hogy alapértelmezés szerint engedélyezze a hozzáférést, távolítsa el a konfigurált hálózati szabályokat. Ezután állítsa be az alapértelmezett műveletet a hozzáférés engedélyezéséhez. Az Azure CLI és az Azure Portal használatával egyenértékű lépések állnak rendelkezésre.

### <a name="restore-default-registry-access---cli"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása - CLI

#### <a name="remove-network-rules"></a>Hálózati szabályok eltávolítása

A rendszerleíró adatbázishoz konfigurált hálózati szabályok listájának megtekintéséhez futtassa a következő [az acr hálózatiszabály-lista][az-acr-network-rule-list] parancsot:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Minden egyes konfigurált szabályhoz futtassa az [az acr hálózati szabály eltávolítási][az-acr-network-rule-remove] parancsát annak eltávolításához. Példa:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Hozzáférés engedélyezése

Helyettesítse a rendszerleíró adatbázis nevét a következő [az acr update][az-acr-update] parancsban:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása - portál


1. A portálon keresse meg a tároló beállításjegyzékét, és válassza **a Tűzfal és a virtuális hálózatok**lehetőséget.
1. A **Virtuális hálózatok**csoportban jelölje ki az egyes virtuális hálózatokat, majd kattintson az Eltávolítás **gombra.**
1. A **Tűzfal**csoportban jelölje ki az egyes címtartományokat, majd kattintson a Törlés ikonra.
1. A **Hozzáférés engedélyezése a területen csoportban**válassza a Minden **hálózat**lehetőséget. 
1. Kattintson a **Mentés** gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az összes Azure-erőforrást ugyanabban az erőforráscsoportban hozta létre, és már nincs rájuk szüksége, szükség esetén törölheti az erőforrásokat egyetlen [az csoport törlési](/cli/azure/group) parancsával:

```azurecli
az group delete --name myResourceGroup
```

Az erőforrások portálon való karbantartásához keresse meg a myResourceGroup erőforráscsoportot. Az erőforráscsoport betöltése után kattintson az **Erőforráscsoport törlése gombra** az erőforráscsoport és az ott tárolt erőforrások eltávolításához.

## <a name="next-steps"></a>További lépések

Ebben a cikkben számos virtuális hálózati erőforrást és szolgáltatást tárgyaltunk, bár röviden. Az Azure Virtual Network dokumentációja részletesen ismerteti az alábbi témaköröket:

* [Virtuális hálózat](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Alhálózat](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Szolgáltatásvégpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
