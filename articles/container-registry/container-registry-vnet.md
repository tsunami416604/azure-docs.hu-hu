---
title: Virtuális hálózattal való hozzáférés korlátozása
description: Csak Azure-beli virtuális hálózat vagy nyilvános IP-címtartományok erőforrásaihoz való hozzáférés engedélyezése az Azure Container registryben.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74454328"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Azure Container Registry-hozzáférés korlátozása Azure-beli virtuális hálózati vagy tűzfalszabályok használatával

Az [azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos, privát hálózatkezelést biztosít az Azure-hoz és a helyszíni erőforrásokhoz. Ha egy Azure-beli virtuális hálózatról korlátozza a privát Azure Container registryhez való hozzáférést, győződjön meg arról, hogy csak a virtuális hálózat erőforrásai férnek hozzá a beállításjegyzékhez. A létesítmények közötti forgatókönyvek esetében a tűzfalszabályok konfigurálásával is engedélyezhető, hogy a beállításjegyzék-hozzáférés csak adott IP-címekről legyen elérhető.

Ez a cikk két forgatókönyvet mutat be a bejövő hálózati hozzáférési szabályok konfigurálásához egy tároló-beállításjegyzékben: egy virtuális hálózaton üzembe helyezett virtuális gépről vagy egy virtuális gép nyilvános IP-címéről.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

Ha ehelyett olyan hozzáférési szabályokat kell beállítania az erőforrásokhoz, amelyek egy tároló-beállításjegyzéket érnek el a tűzfal mögött, tekintse meg a [szabályok konfigurálása az Azure Container Registry tűzfal mögötti eléréséhez](container-registry-firewall-access-rules.md)című témakört.


## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Csak a **prémium** szintű tároló-beállításjegyzék konfigurálható a hálózati hozzáférési szabályokkal. További információ a beállításjegyzék szolgáltatási szintjeiről: [Azure Container Registry SKU](container-registry-skus.md)-ban. 

* Csak egy [Azure Kubernetes Service](../aks/intro-kubernetes.md) -fürt vagy Azure-beli [virtuális gép](../virtual-machines/linux/overview.md) használható gazdagépként egy virtuális hálózatban lévő tároló-beállításjegyzék eléréséhez. *Más Azure-szolgáltatások, többek között a Azure Container Instances jelenleg nem támogatottak.*

* Az [ACR-feladatok](container-registry-tasks-overview.md) műveletei jelenleg nem támogatottak egy virtuális hálózaton keresztül elért tároló-beállításjegyzékben.

* Mindegyik beállításjegyzék legfeljebb 100 virtuális hálózati szabályt támogat.

## <a name="prerequisites"></a>Előfeltételek

* A cikkben szereplő Azure CLI-lépések használatához az Azure CLI 2.0.58 vagy újabb verziójára van szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

* Ha még nem rendelkezik tároló-beállításjegyzékkel, hozzon létre egyet (prémium SKU szükséges), és küldjön `hello-world` le egy mintát, például a Docker hub-ból. A beállításjegyzék létrehozásához például használja az [Azure Portal][quickstart-portal] vagy az [Azure CLI][quickstart-cli] -t. 

* Ha egy másik Azure-előfizetésben lévő virtuális hálózat használatával szeretné korlátozni a beállításjegyzék-hozzáférést, regisztrálnia kell az erőforrás-szolgáltatót az előfizetésben lévő Azure Container Registryhoz. Például:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>A tároló-beállításjegyzék hálózati szabályai

Az Azure Container Registry alapértelmezés szerint minden hálózaton fogadja az interneten keresztüli kapcsolatokat. A virtuális hálózat lehetővé teszi, hogy csak az Azure-erőforrások, például az AK-fürtök vagy az Azure-beli virtuális gépek számára engedélyezze a beállításjegyzék biztonságos elérését anélkül, hogy áthalad egy hálózati határt. A hálózati tűzfalszabályok úgy is konfigurálhatók, hogy csak bizonyos nyilvános internetes IP-címtartományt engedélyezzen. 

A beállításjegyzékhez való hozzáférés korlátozásához először módosítsa a beállításjegyzék alapértelmezett műveletét, hogy az megtagadja az összes hálózati kapcsolatot. Ezután adja hozzá a hálózati hozzáférési szabályokat. A hálózati szabályokon keresztül hozzáférési jogosultsággal rendelkező ügyfeleknek továbbra is [hitelesíteniük kell magukat a tároló-beállításjegyzékben](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) , és engedélyezni kell az adatelérést.

### <a name="service-endpoint-for-subnets"></a>Alhálózatok szolgáltatási végpontja

A virtuális hálózatban lévő alhálózatok elérésének engedélyezéséhez hozzá kell adnia egy [szolgáltatási végpontot](../virtual-network/virtual-network-service-endpoints-overview.md) a Azure Container Registry szolgáltatáshoz. 

Több-bérlős szolgáltatás, például Azure Container Registry, egyetlen IP-címet használhat az összes ügyfél számára. Egy szolgáltatási végpont hozzárendel egy végpontot a beállításjegyzék eléréséhez. Ez a végpont optimális útvonalat biztosít az erőforrásnak az Azure gerinc hálózaton keresztül. A virtuális hálózat és az alhálózat identitásait is továbbítjuk az egyes kérésekhez.

### <a name="firewall-rules"></a>Tűzfalszabályok

IP-hálózati szabályok esetén a CIDR-jelöléssel, például a *16.17.18.0/24* , vagy egy olyan egyedi IP-címmel adja meg az engedélyezett internetes címtartományt, mint a *16.17.18.19*. Az IP-hálózati szabályok csak a *nyilvános* internetes IP-címek esetében engedélyezettek. A magánhálózati hálózatok számára fenntartott IP-címtartományok (az RFC 1918-ben meghatározottak szerint) nem engedélyezettek az IP-szabályokban.

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker-kompatibilis virtuális gép létrehozása

Ehhez a cikkhez egy Docker-kompatibilis Ubuntu virtuális gépet használhat egy Azure Container Registry eléréséhez. Ha Azure Active Directory hitelesítést kíván használni a beállításjegyzékben, telepítse az [Azure CLI][azure-cli] -t is a virtuális gépre. Ha már rendelkezik Azure-beli virtuális géppel, ugorja át ezt a létrehozási lépést.

Használhatja ugyanazt az erőforráscsoportot a virtuális géphez és a tároló-beállításjegyzékhez is. Ez a beállítás leegyszerűsíti a tisztítást a végén, de nem kötelező. Ha úgy dönt, hogy külön erőforráscsoportot hoz létre a virtuális géphez és a virtuális hálózathoz, futtassa az [az Group Create][az-group-create]parancsot. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westcentralus* helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

Most helyezzen üzembe egy alapértelmezett Ubuntu Azure-beli virtuális gépet az [az VM Create][az-vm-create]paranccsal. A következő példa egy *myDockerVM*nevű virtuális gépet hoz létre:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Ha a parancs befejeződik, jegyezze fel az Azure `publicIpAddress` CLI által megjelenített adatmennyiséget. Ez a címe teszi elérhetővé SSH-kapcsolatokat a virtuális géppel, és opcionálisan a tűzfalszabályok későbbi beállításához.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépre

A virtuális gép futása után létesítsen SSH-kapcsolatokat a virtuális géppel. Cserélje le a *publicIpAddress* -t a virtuális gép nyilvános IP-címére.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsot a Docker telepítéséhez az Ubuntu virtuális gépen:

```bash
sudo apt install docker.io -y
```

A telepítés után futtassa a következő parancsot annak ellenőrzéséhez, hogy a Docker megfelelően fut-e a virtuális gépen:

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

Az Azure CLI az Ubuntu rendszerű virtuális gépen való telepítéséhez kövesse az Azure CLI az [apt-vel](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) való telepítésének lépéseit. Ehhez a cikkhez győződjön meg arról, hogy a 2.0.58 vagy újabb verzióját telepíti.

Lépjen ki az SSH-kapcsolatban.

## <a name="allow-access-from-a-virtual-network"></a>Virtuális hálózatról való hozzáférés engedélyezése

Ebben a szakaszban úgy konfigurálja a tároló-beállításjegyzéket, hogy engedélyezze a hozzáférést egy Azure-beli virtuális hálózat alhálózatán. Az Azure CLI és a Azure Portal használatával egyenértékű lépések vannak megadva.

### <a name="allow-access-from-a-virtual-network---cli"></a>Virtuális hálózatról való hozzáférés engedélyezése – parancssori felület

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Szolgáltatási végpont hozzáadása egy alhálózathoz

Amikor létrehoz egy virtuális GÉPET, az Azure alapértelmezés szerint ugyanahhoz az erőforráscsoporthoz hoz létre egy virtuális hálózatot. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gép *myDockerVM*nevezi el, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, és egy *myDockerVMSubnet*nevű alhálózattal rendelkezik. Ellenőrizze ezt a Azure Portalban vagy az az [Network vnet List][az-network-vnet-list] parancs használatával:

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

Az az [Network vnet subnet Update][az-network-vnet-subnet-update] paranccsal adhat hozzá **Microsoft. ContainerRegistry** szolgáltatási végpontot az alhálózathoz. Helyettesítse be a virtuális hálózat és az alhálózat nevét a következő parancsban:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Az alhálózat erőforrás-AZONOSÍTÓjának lekéréséhez használja az az [Network vnet subnet show][az-network-vnet-subnet-show] parancsot. Ezt egy későbbi lépésben kell konfigurálnia egy hálózati hozzáférési szabály konfigurálásához.

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

#### <a name="change-default-network-access-to-registry"></a>A beállításjegyzék alapértelmezett hálózati hozzáférésének módosítása

Alapértelmezés szerint az Azure Container Registry lehetővé teszi, hogy bármely hálózaton a gazdagépek kapcsolatai legyenek. A kiválasztott hálózathoz való hozzáférés korlátozásához módosítsa az alapértelmezett műveletet, hogy megtagadja a hozzáférést. Helyettesítse be a beállításjegyzék nevét a következő az [ACR Update][az-acr-update] paranccsal:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

Az az [ACR Network-Rule Add][az-acr-network-rule-add] paranccsal adhat hozzá egy hálózati szabályt a beállításjegyzékhez, amely lehetővé teszi a virtuális gép alhálózatának elérését. Helyettesítse be a tároló beállításjegyzékének nevét és az alhálózat erőforrás-AZONOSÍTÓját a következő parancsban: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Folytassa [a beállításjegyzékhez való hozzáférés ellenőrzésével](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Virtuális hálózatról való hozzáférés engedélyezése – portál

#### <a name="add-service-endpoint-to-subnet"></a>Szolgáltatási végpont hozzáadása az alhálózathoz

Amikor létrehoz egy virtuális GÉPET, az Azure alapértelmezés szerint ugyanahhoz az erőforráscsoporthoz hoz létre egy virtuális hálózatot. A virtuális hálózat neve a virtuális gép nevén alapul. Ha például a virtuális gép *myDockerVM*nevezi el, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, és egy *myDockerVMSubnet*nevű alhálózattal rendelkezik.

Szolgáltatási végpont hozzáadása Azure Container Registry alhálózathoz:

1. A [Azure Portal][azure-portal]tetején található keresőmezőbe írja be a *virtuális hálózatok*kifejezést. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
1. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amelyben a virtuális gép telepítve van, például *myDockerVMVNET*.
1. A **Beállítások**területen válassza az **alhálózatok**lehetőséget.
1. Válassza ki azt az alhálózatot, amelyben a virtuális gép telepítve van, például *myDockerVMSubnet*.
1. A **szolgáltatási végpontok**területen válassza a **Microsoft. ContainerRegistry**elemet.
1. Kattintson a **Mentés** gombra.

![Szolgáltatási végpont hozzáadása az alhálózathoz][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Hálózati hozzáférés konfigurálása a beállításjegyzékhez

Alapértelmezés szerint az Azure Container Registry lehetővé teszi, hogy bármely hálózaton a gazdagépek kapcsolatai legyenek. A virtuális hálózathoz való hozzáférés korlátozása:

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **Beállítások**területen válassza a **tűzfal és a virtuális hálózatok**lehetőséget.
1. Ha alapértelmezés szerint szeretné megtagadni a hozzáférést, válassza a **kijelölt hálózatokból**való hozzáférés engedélyezése lehetőséget. 
1. Válassza a **meglévő virtuális hálózat hozzáadása**lehetőséget, majd válassza ki azt a virtuális hálózatot és alhálózatot, amelyet szolgáltatási végponttal konfigurált. Válassza a **Hozzáadás** lehetőséget.
1. Kattintson a **Mentés** gombra.

![Virtuális hálózat konfigurálása a tároló-beállításjegyzékhez][acr-vnet-portal]

Folytassa [a beállításjegyzékhez való hozzáférés ellenőrzésével](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>IP-címről való hozzáférés engedélyezése

Ebben a szakaszban úgy konfigurálja a tároló-beállításjegyzéket, hogy egy adott IP-címről vagy-tartományból engedélyezze a hozzáférést. Az Azure CLI és a Azure Portal használatával egyenértékű lépések vannak megadva.

### <a name="allow-access-from-an-ip-address---cli"></a>Hozzáférés engedélyezése IP-címről – parancssori felület

#### <a name="change-default-network-access-to-registry"></a>A beállításjegyzék alapértelmezett hálózati hozzáférésének módosítása

Ha még nem tette meg, frissítse a beállításjegyzék konfigurációját, hogy alapértelmezés szerint megtagadja a hozzáférést. Helyettesítse be a beállításjegyzék nevét a következő az [ACR Update][az-acr-update] paranccsal:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Hálózati szabály eltávolítása a beállításjegyzékből

Ha korábban olyan hálózati szabályt adott hozzá, amely engedélyezi a hozzáférést a virtuális gép alhálózatáról, távolítsa el az alhálózat szolgáltatási végpontját és a hálózati szabályt. Helyettesítse be a tároló beállításjegyzékének nevét és annak az alhálózatnak az erőforrás-AZONOSÍTÓját, amelyet az az [ACR Network-Rule Remove][az-acr-network-rule-remove] paranccsal egy korábbi lépésben kapott le: 

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

Az az [ACR Network-Rule Add][az-acr-network-rule-add] paranccsal adhat hozzá egy hálózati szabályt a beállításjegyzékhez, amely lehetővé teszi a virtuális gép IP-címének elérését. Helyettesítse be a tároló beállításjegyzékének nevét és a virtuális gép nyilvános IP-címét a következő parancsban.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Folytassa [a beállításjegyzékhez való hozzáférés ellenőrzésével](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Hozzáférés engedélyezése IP-címről – portál

#### <a name="remove-existing-network-rule-from-registry"></a>Meglévő hálózati szabály eltávolítása a beállításjegyzékből

Ha korábban olyan hálózati szabályt adott hozzá, amely engedélyezi a hozzáférést a virtuális gép alhálózatáról, távolítsa el a meglévő szabályt. Ugorja át ezt a szakaszt, ha egy másik virtuális gépről szeretné elérni a beállításjegyzéket.

* Frissítse az alhálózat beállításait az alhálózat szolgáltatási végpontjának Azure Container Registry való eltávolításához. 

  1. A [Azure Portal][azure-portal]navigáljon ahhoz a virtuális hálózathoz, amelyen a virtuális gép telepítve van.
  1. A **Beállítások**területen válassza az **alhálózatok**lehetőséget.
  1. Válassza ki azt az alhálózatot, amelybe a virtuális gép telepítve van.
  1. A **szolgáltatási végpontok**területen távolítsa el a **Microsoft. ContainerRegistry**jelölőnégyzetét. 
  1. Kattintson a **Mentés** gombra.

* Távolítsa el azt a hálózati szabályt, amely lehetővé teszi az alhálózat számára a beállításjegyzék elérését.

  1. A portálon navigáljon a tároló-beállításjegyzékhez.
  1. A **Beállítások**területen válassza a **tűzfal és a virtuális hálózatok**lehetőséget.
  1. A **virtuális hálózatok**területen válassza ki a virtuális hálózat nevét, majd válassza az **Eltávolítás**lehetőséget.
  1. Kattintson a **Mentés** gombra.

#### <a name="add-network-rule-to-registry"></a>Hálózati szabály hozzáadása a beállításjegyzékhez

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **Beállítások**területen válassza a **tűzfal és a virtuális hálózatok**lehetőséget.
1. Ha még nem tette meg, válassza a hozzáférés engedélyezése a **kiválasztott hálózatokból**lehetőséget. 
1. A **virtuális hálózatok**területen győződjön meg arról, hogy nincs kiválasztva hálózat.
1. A **tűzfal**területen adja meg egy virtuális gép nyilvános IP-címét. Vagy adjon meg egy címtartományt a CIDR-jelölésben, amely a virtuális gép IP-címét tartalmazza.
1. Kattintson a **Mentés** gombra.

![Tűzfalszabály konfigurálása a tároló-beállításjegyzékhez][acr-vnet-firewall-portal]

Folytassa [a beállításjegyzékhez való hozzáférés ellenőrzésével](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>A beállításjegyzékhez való hozzáférés ellenőrzése

Néhány perc várakozás után a konfiguráció frissítését követően ellenőrizze, hogy a virtuális gép hozzáférhet-e a tároló-beállításjegyzékhez. Létesítsen SSH-kapcsolatokat a virtuális géppel, és futtassa az az [ACR login][az-acr-login] parancsot a beállításjegyzékbe való bejelentkezéshez. 

```bash
az acr login --name mycontainerregistry
```

A beállításjegyzékből olyan rendszerleíróadatbázis-műveleteket hajthat végre, mint például a Futtatás `docker pull` . Helyettesítse be a beállításjegyzékbe a rendszerkép és a címke értékét, a beállításjegyzék bejelentkezési kiszolgálójának nevével (az összes kisbetűs) együtt:

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

A Docker sikeresen lekéri a rendszerképet a virtuális gépre.

Ez a példa azt mutatja be, hogy a magánhálózati hozzáférési szabály használatával férhet hozzá a Private Container registryhez. A beállításjegyzék azonban nem érhető el egy másik bejelentkezési gazdagépről, amelyhez nincs konfigurálva hálózati hozzáférési szabály. Ha a `az acr login` parancs vagy `docker login` parancs használatával próbál bejelentkezni egy másik gazdagépről, a kimenet a következőhöz hasonló:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása

Ha a beállításjegyzéket úgy szeretné visszaállítani, hogy alapértelmezés szerint engedélyezze a hozzáférést, távolítsa el a konfigurált hálózati szabályokat. Ezután állítsa be az alapértelmezett műveletet a hozzáférés engedélyezéséhez. Az Azure CLI és a Azure Portal használatával egyenértékű lépések vannak megadva.

### <a name="restore-default-registry-access---cli"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása – parancssori felület

#### <a name="remove-network-rules"></a>Hálózati szabályok eltávolítása

A beállításjegyzékhez konfigurált hálózati szabályok listájának megtekintéséhez futtassa a következőt az [ACR Network-Rule List][az-acr-network-rule-list] paranccsal:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Minden konfigurált szabályhoz futtassa az az [ACR Network-Rule Remove][az-acr-network-rule-remove] parancsot az eltávolításához. Például:

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

Helyettesítse be a beállításjegyzék nevét a következő az [ACR Update][az-acr-update] paranccsal:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Alapértelmezett beállításjegyzék-hozzáférés visszaállítása – portál


1. A portálon navigáljon a tároló beállításjegyzékéhez, és válassza a **tűzfal és a virtuális hálózatok**lehetőséget.
1. A **virtuális hálózatok**területen válassza ki az egyes virtuális hálózatokat, majd válassza az **Eltávolítás**lehetőséget.
1. A **tűzfal**területen válassza ki az egyes címtartományt, majd kattintson a Törlés ikonra.
1. **A hozzáférés engedélyezése lehetőségnél**válassza a **minden hálózat**lehetőséget. 
1. Kattintson a **Mentés** gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az összes Azure-erőforrást ugyanabban az erőforráscsoporthoz hozta létre, és már nincs rájuk szükség, akkor az erőforrásokat egyetlen [az Group delete](/cli/azure/group) paranccsal törölheti:

```azurecli
az group delete --name myResourceGroup
```

A portálon található erőforrások törléséhez navigáljon a myResourceGroup erőforráscsoporthoz. Miután betöltötte az erőforráscsoportot, kattintson az **erőforráscsoport törlése** elemre az erőforráscsoport és az ott tárolt erőforrások eltávolításához.

## <a name="next-steps"></a>További lépések

Ebben a cikkben több virtuális hálózati erőforrást és szolgáltatást is tárgyaltak, de röviden. Az Azure Virtual Network dokumentációja részletesen ismerteti ezeket a témaköröket:

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
