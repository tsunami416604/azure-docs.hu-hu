---
title: Egy virtuális hálózatban az Azure container registry üzembe helyezése
description: Hozzáférés engedélyezése egy Azure container registrybe, csak egy Azure virtuális hálózatban lévő erőforrásokra vagy nyilvános IP-címtartományok.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/14/2019
ms.author: danlep
ms.openlocfilehash: 044ff823e34f51257cb6186417535b9b542bc289
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136983"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Azure-beli virtuális hálózathoz vagy a tűzfal-szabályok használata Azure container registry való hozzáférés korlátozása

[Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztosít az Azure biztonságos, privát hálózati és helyi erőforrásokhoz. A privát Azure container registry egy Azure-beli virtuális hálózatban üzembe, biztosíthatja, hogy csak a virtuális hálózatban lévő erőforrásokra hozzáférni a beállításjegyzékhez. Létesítmények közötti forgatókönyvek esetén tűzfalszabályokat, hogy engedélyezze a beállításjegyzék hozzáférést csak meghatározott IP-címek is konfigurálhatja.

Ez a cikk bemutatja a hálózati hozzáférési szabályok az férjenek hozzá az Azure container registry létrehozása két esetben: ugyanazon a hálózaton üzembe helyezett virtuális gépet, vagy a virtuális gép nyilvános IP-címet.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és néhány [korlátozások érvényesek a](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Csak egy **prémium** tároló-beállításjegyzék hálózati hozzáférési szabályok is konfigurálhatók. Beállításjegyzék szolgáltatásszintek kapcsolatos információkért lásd: [Azure Container Registry Termékváltozatai](container-registry-skus.md). 

* Csak egy [Azure Kubernetes Service](../aks/intro-kubernetes.md) fürt vagy az Azure [virtuális gép](../virtual-machines/linux/overview.md) egy tároló-beállításjegyzéket egy virtuális hálózat eléréséhez használható gazdagépként. *Más Azure-szolgáltatások többek között az Azure Container Instances szolgáltatásban jelenleg nem támogatottak.*

* Minden egyes registry támogatja a legfeljebb 100 virtuális hálózati szabályok.

## <a name="prerequisites"></a>Előfeltételek

* Használata az Azure CLI lépéseket ebben a cikkben az Azure CLI 2.0.58 verzió vagy újabb verzió szükséges. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

* Ha még nem rendelkezik egy tároló-beállításjegyzéket, hozzon létre egyet (prémium szintű Termékváltozat szükséges), és a egy képet például ügyfélleküldéses `hello-world` a Docker Hubból. Például a [az Azure portal] [ quickstart-portal] vagy a [Azure CLI-vel] [ quickstart-cli] hozhat létre egy beállításjegyzéket. 

## <a name="about-network-rules-for-a-container-registry"></a>Egy tároló-beállításjegyzék hálózati szabályokkal kapcsolatos

Alapértelmezés szerint egy Azure container registryt az interneten, a gazdagépeket a hálózaton keresztül fogad kapcsolatokat. A virtual network révén engedélyezheti csak az Azure erőforrások, például egy AKS-fürtöt vagy az Azure-beli virtuális gép hozzá biztonságosan a beállításjegyzék egy hálózathatárt határainak átlépése nélkül. Beállíthatja úgy is, amelyeket engedélyezni kell a hálózati tűzfalszabályt adott nyilvános internetes IP-címtartományok. 

A beállításjegyzék való hozzáférés korlátozására, először módosítsa az alapértelmezett művelet a beállításjegyzék, hogy, letiltja az összes hálózati kapcsolatot. Adja hozzá a hálózati hozzáférési szabályok. Ügyfelek számára nyújtott elérését a hálózati szabályok továbbra is kell [hitelesítik magukat a tároló-beállításjegyzék](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) és engedélyezni kell, hogy az adatok eléréséhez.

### <a name="service-endpoint-for-subnets"></a>Az alhálózat szolgáltatásvégpont

Engedélyezze a hozzáférést egy alhálózatot a virtuális hálózatban, hogy hozzá kell egy [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) az Azure Container Registry szolgáltatás. 

Több-bérlős szolgáltatások, például az Azure Container Registry használatával egy egyetlen IP-címek minden ügyfelünk esetében. Egy szolgáltatásvégpont hozzáférni a beállításjegyzékhez egy végpontot rendel. Ezt a végpontot biztosít a forgalmat az optimális útvonal az erőforrást az Azure gerinchálózatán keresztül. Minden egyes kérelemmel is továbbítja az identitások, a virtuális hálózatot és alhálózatot.

### <a name="firewall-rules"></a>Tűzfalszabályok

IP-hálózati szabályok, adja meg az internet engedélyezett a címtartomány CIDR-jelölés használatával például *16.17.18.0/24* vagy egy egyedi IP-címeket, például *16.17.18.19*. Csak engedélyezett IP-hálózati szabályok *nyilvános* IP-címek. Magánhálózatok számára fenntartott (RFC 1918-ban meghatározott) IP-címtartományok az IP-szabályok nem engedélyezett.

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker-kompatibilis virtuális gép létrehozása

Ebben a cikkben egy Docker-kompatibilis Ubuntu rendszerű virtuális gép eléréséhez használja az Azure container registrybe. Az Azure Active Directory-hitelesítés használatához a beállításjegyzék is telepítse a [Azure CLI-vel] [ azure-cli] a virtuális gépen. Ha már rendelkezik egy Azure virtuális gépen, hagyja ki a létrehozási lépés.

Használhatja ugyanazt az erőforráscsoportot a virtuális gép és a tároló-beállításjegyzék. A telepítő egyszerűbbé teszi a karbantartási végén, de nincs szükség. Ha egy külön erőforráscsoportot a virtuális gép és a virtuális hálózat létrehozása, futtatása [az csoport létrehozása][az-group-create]. A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* a a *westcentralus* helye:

```azurecli
az group create --name myResourceGroup --location westus
```

Most már üzembe egy alapértelmezett Ubuntu Azure virtuális gép [az virtuális gép létrehozása][az-vm-create]. A következő példában létrehozunk egy nevű virtuális Gépet *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Ha a parancs végrehajtása befejeződött, jegyezze fel a `publicIpAddress` az Azure CLI által megjelenített. Ez a cím segítségével győződjön meg arról, SSH-kapcsolatokat a virtuális géphez, és szükség esetén a tűzfalszabályok újabb telepítő.

### <a name="install-docker-on-the-vm"></a>A gépen telepítheti a Dockert

Miután a virtuális gép fut, győződjön meg arról, az SSH-kapcsolatot a virtuális géphez. Cserélje le *publicIpAddress* a virtuális gép nyilvános IP-címét.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsot az Ubuntu virtuális gépen telepítheti a Dockert:

```bash
sudo apt install docker.io -y
```

A telepítés után futtassa a következő paranccsal ellenőrizze, hogy a Docker a virtuális gép megfelelően fut:

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

Kövesse a [Azure CLI telepítése az Apt használatával](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) az Azure CLI telepítése az Ubuntus virtuális gép. Ebben a cikkben győződjön meg arról, hogy 2.0.58 verzió telepítése vagy újabb.

Lépjen ki az SSH-kapcsolat.

## <a name="allow-access-from-a-virtual-network"></a>Engedélyezze a hozzáférést egy virtuális hálózatot

Ebben a szakaszban konfigurálhatja a tárolóregisztrációs adatbázisba, engedélyezze a hozzáférést egy alhálózatot az Azure-beli virtuális hálózathoz. Az Azure CLI-vel és az Azure portal használatával ugyanazokból a lépésekből állnak rendelkezésre.

### <a name="allow-access-from-a-virtual-network---cli"></a>Engedélyezze a hozzáférést egy virtuális hálózat – CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Adjon hozzá egy végpontot egy alhálózathoz

Amikor egy virtuális Gépet hoz létre, alapértelmezés szerint az Azure létrehoz egy virtuális hálózatot az ugyanabban az erőforráscsoportban. A virtuális hálózat neve a virtuális gép neve alapján. Például, ha ezt a virtuális gép *myDockerVM*, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, nevű alhálózattal *myDockerVMSubnet*. Az Azure Portalon vagy a használatával ellenőrizheti a [az network vnet list] [ az-network-vnet-list] parancsot:

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

Használja a [az hálózati virtuális hálózat alhálózati frissítés] [ az-network-vnet-subnet-update] parancs használatával adja hozzá egy **Microsoft.ContainerRegistry** szolgáltatásvégpontot az alhálózathoz. Helyettesítse be a virtuális hálózatot és alhálózatot az alábbi parancsban nevei:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Használja a [az hálózati virtuális hálózat alhálózati show] [ az-network-vnet-subnet-show] parancs használatával kérje le az alhálózat erőforrás-Azonosítóját. Ez egy hálózati hozzáférési szabály konfigurálása egy későbbi lépésben kell.

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

#### <a name="change-default-network-access-to-registry"></a>Alapértelmezett hálózati hozzáférés módosítása a jegyzék

Alapértelmezés szerint az Azure container registry engedélyezi a kapcsolatok gazdagépekről a hálózaton. A kiválasztott hálózathoz való hozzáférés korlátozására, módosítsa az alapértelmezett műveletet, hogy megtagadja a hozzáférést. Helyettesítse be a következő a tárolójegyzék nevére [az acr update] [ az-acr-update] parancsot:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>A beállításjegyzék hálózati szabály hozzáadása

Használja a [az acr hálózati-szabály hozzáadása] [ az-acr-network-rule-add] parancs használatával adja hozzá a hálózati szabályt, amely lehetővé teszi, hogy a Virtuálisgép-alhálózat hozzáférését a tárolójegyzékbe. Helyettesítse be a tároló-beállításjegyzék nevét és az alábbi parancsban az alhálózat erőforrás-Azonosítóját: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Továbbra is [ellenőrizze a beállításjegyzék elérését](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Engedélyezze a hozzáférést egy virtuális hálózat – portál

#### <a name="add-service-endpoint-to-subnet"></a>Alhálózati szolgáltatásvégpont hozzáadása

Amikor egy virtuális Gépet hoz létre, alapértelmezés szerint az Azure létrehoz egy virtuális hálózatot az ugyanabban az erőforráscsoportban. A virtuális hálózat neve a virtuális gép neve alapján. Például, ha ezt a virtuális gép *myDockerVM*, az alapértelmezett virtuális hálózat neve *myDockerVMVNET*, nevű alhálózattal *myDockerVMSubnet*.

Az Azure Container Registry egy szolgáltatásvégpont hozzáadása egy alhálózathoz:

1. Tetején található keresőmezőbe a [az Azure portal][azure-portal], adja meg *virtuális hálózatok*. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
1. Virtuális hálózatok listájából válassza ki a virtuális hálózat, ahol a virtuális gép telepítve van, mint például *myDockerVMVNET*.
1. A **beállítások**válassza **alhálózatok**.
1. Válassza ki az alhálózatot, ahol a virtuális gép telepítve van, mint például *myDockerVMSubnet*.
1. A **Szolgáltatásvégpontokat**válassza **Microsoft.ContainerRegistry**.
1. Kattintson a **Mentés** gombra.

![Alhálózati szolgáltatásvégpont hozzáadása][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Hálózati hozzáférés beállításjegyzék konfigurálása

Alapértelmezés szerint az Azure container registry engedélyezi a kapcsolatok gazdagépekről a hálózaton. A virtuális hálózathoz való hozzáférés korlátozása:

1. A portálon lépjen a tárolóregisztrációs adatbázisba.
1. A **beállítások**válassza **tűzfal és virtuális hálózatok**.
1. Alapértelmezés szerint engedélyezi a hozzáférést, válassza a hozzáférés engedélyezéséhez **kiválasztott hálózatok**. 
1. Válassza ki **meglévő virtuális hálózat hozzáadása**, és válassza ki a virtuális hálózatot és alhálózatot konfigurált szolgáltatásvégponttal. Válassza a **Hozzáadás** lehetőséget.
1. Kattintson a **Mentés** gombra.

![Virtuális hálózat, tároló-beállításjegyzék konfigurálása][acr-vnet-portal]

Továbbra is [ellenőrizze a beállításjegyzék elérését](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Engedélyezze a hozzáférést egy IP-cím

Ebben a szakaszban konfigurálhatja a tárolóregisztrációs adatbázisba, engedélyezze a hozzáférést egy alhálózatot az Azure-beli virtuális hálózathoz. Az Azure CLI-vel és az Azure portal használatával ugyanazokból a lépésekből állnak rendelkezésre.

### <a name="allow-access-from-an-ip-address---cli"></a>Engedélyezze a hozzáférést egy IP-cím – CLI

#### <a name="change-default-network-access-to-registry"></a>Alapértelmezett hálózati hozzáférés módosítása a jegyzék

Ha ezt még nem tette meg, frissítse a beállításjegyzék konfigurációját, hogy megtagadja a hozzáférést alapértelmezés szerint. Helyettesítse be a következő a tárolójegyzék nevére [az acr update] [ az-acr-update] parancsot:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>A beállításjegyzék hálózati szabály eltávolítása

Ha korábban hozzáadott egy hálózati szabályt, amely engedélyezi a hozzáférést a Virtuálisgép-alhálózatból, távolítsa el az alhálózat szolgáltatásvégpont és a hálózati szabály. Helyettesítse be a tároló-beállításjegyzék nevét és az alhálózat, a korábbi lépésben lekért erőforrás-Azonosítóját a [az acr hálózati-szabály eltávolítása] [ az-acr-network-rule-remove] parancsot: 

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

#### <a name="add-network-rule-to-registry"></a>A beállításjegyzék hálózati szabály hozzáadása

Használja a [az acr hálózati-szabály hozzáadása] [ az-acr-network-rule-add] parancs használatával adja hozzá a hálózati szabályt a beállításjegyzékbe, amely lehetővé teszi a hozzáférést a virtuális gép IP-címről. Cserélje le a tárolójegyzék neve és a következő parancsot a virtuális gép nyilvános IP-címét.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Továbbra is [ellenőrizze a beállításjegyzék elérését](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Engedélyezze a hozzáférést egy IP-cím – portál

#### <a name="remove-existing-network-rule-from-registry"></a>Távolítsa el a meglévő hálózati szabály beállításjegyzékből

Ha korábban hozzáadott egy hálózati szabályt, amely engedélyezi a hozzáférést a Virtuálisgép-alhálózatból, távolítsa el a meglévő szabályt. Kihagyhatja ezt a szakaszt, ha szeretne hozzáférni a beállításjegyzékhez egy másik virtuális gépről.

* Az alhálózat szolgáltatásvégpont eltávolítása az Azure Container Registry alhálózat-beállításainak frissítése. 

  1. Az a [az Azure portal][azure-portal], keresse meg a virtuális hálózathoz, ahol a virtuális gép üzemel.
  1. A **beállítások**válassza **alhálózatok**.
  1. Válassza ki az alhálózatot, ahol a virtuális gép üzemel.
  1. A **Szolgáltatásvégpontokat**, távolítsa el a jelölőnégyzetet a **Microsoft.ContainerRegistry**. 
  1. Kattintson a **Mentés** gombra.

* Távolítsa el a hálózati szabály, amely lehetővé teszi, hogy az alhálózat számára a beállításjegyzék elérését.

  1. A portálon lépjen a tárolóregisztrációs adatbázisba.
  1. A **beállítások**válassza **tűzfal és virtuális hálózatok**.
  1. A **virtuális hálózatok**, a virtuális hálózat nevét, majd válassza ki és **eltávolítása**.
  1. Kattintson a **Mentés** gombra.

#### <a name="add-network-rule-to-registry"></a>A beállításjegyzék hálózati szabály hozzáadása

1. A portálon lépjen a tárolóregisztrációs adatbázisba.
1. A **beállítások**válassza **tűzfal és virtuális hálózatok**.
1. Ha ezt még nem tette meg, válassza a hozzáférés engedélyezése a következőtől **kiválasztott hálózatok**. 
1. A **virtuális hálózatok**, győződjön meg arról, nem a hálózat van kiválasztva.
1. A **tűzfal**, adja meg a virtuális gép nyilvános IP-címét. Vagy adjon meg egy címtartományt a CIDR-jelölésrendszerben, amely tartalmazza a virtuális IP-címét.
1. Kattintson a **Mentés** gombra.

![Tároló-beállításjegyzék tűzfalszabály konfigurálása][acr-vnet-firewall-portal]

Továbbra is [ellenőrizze a beállításjegyzék elérését](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>A beállításjegyzék hozzáférés ellenőrzése

A konfiguráció frissítése, ellenőrizze, hogy néhány perc várakozás után a virtuális gép hozzáférhet a tároló-beállításjegyzékbe. Létesítsen SSH-kapcsolatot a virtuális gépre, és futtassa a [az acr bejelentkezési] [ az-acr-login] parancsot a bejelentkezés a tárolójegyzékbe. 

```bash
az acr login --name mycontainerregistry
```

Beállításjegyzék műveleteket hajthat végre, mint például a Futtatás `docker pull` egy minta rendszerkép lekéréshez a beállításjegyzékben. Helyettesítse be a rendszerleíró adatbázis bejelentkezési kiszolgálójának nevét (csak kisbetűkkel) előtaggal van ellátva, a beállításjegyzék megfelelő egy rendszerképet, és felcímkézik érték:

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker sikeresen lekéri a rendszerképet a virtuális géphez.

Ez a példa bemutatja, hogy a megadott hálózati hozzáférési szabály a privát tárolóregisztrációs adatbázis elérhető. Azonban a beállításjegyzék nem érhető el egy másik bejelentkezési azonosító gazdagépről, amely nem rendelkezik konfigurált hálózati hozzáférési szabályt. Ha egy másik gazdagépre használja a bejelentkezés a `az acr login` parancs vagy `docker login` parancs kimenete az alábbihoz hasonló lesz:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Állítsa vissza az alapértelmezett adatbázis eléréséhez

Állítsa vissza a beállításjegyzék alapértelmezés szerint engedélyezi a hozzáférést, távolítsa el a konfigurált hálózati szabályokat. Ezután állítsa be az alapértelmezett műveletet, hogy engedélyezze a hozzáférést. Az Azure CLI-vel és az Azure portal használatával ugyanazokból a lépésekből állnak rendelkezésre.

### <a name="restore-default-registry-access---cli"></a>Állítsa vissza az alapértelmezett beállításjegyzék elérését – CLI

#### <a name="remove-network-rules"></a>Távolítsa el a hálózati szabályok

A beállításjegyzék konfigurált hálózati szabályok listájának megtekintéséhez futtassa a következő [az acr hálózati-rule list] [ az-acr-network-rule-list] parancsot:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Minden szabály, amely konfigurálva van, futtassa az alábbi parancsot a [az acr hálózati-szabály eltávolítása] [ az-acr-network-rule-remove] távolítsa el a parancsot. Példa:

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

Helyettesítse be a következő a tárolójegyzék nevére [az acr update] [ az-acr-update] parancsot:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Visszaállítás alapértelmezett beállításjegyzék-hozzáférés – portál


1. A portálon keresse meg a tárolóregisztrációs adatbázisba, és válassza **tűzfal és virtuális hálózatok**.
1. A **virtuális hálózatok**, minden virtuális hálózatnak, majd válassza ki és **eltávolítása**.
1. A **tűzfal**, minden címtartományt, majd válassza ki és a Törlés ikonra.
1. A **engedélyezze a hozzáférést**válassza **minden hálózatból elérhető**. 
1. Kattintson a **Mentés** gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott összes Azure-erőforrások ugyanabban az erőforrásban csoportból, és már nincs szüksége rájuk, igény szerint az erőforrásokat törölheti egyetlen [az csoport törlése](/cli/azure/group#az_group_delete) parancsot:

```azurecli
az group delete --name myResourceGroup
```

A portálon az erőforrások törléséhez keresse meg a myResourceGroup erőforráscsoportot. Miután az erőforráscsoport betöltött, kattintson a **erőforráscsoport törlése** eltávolítható az erőforráscsoport és az ott tárolt erőforrások.

## <a name="next-steps"></a>További lépések

Több virtuális hálózati erőforrások és szolgáltatások ebben a cikkben aktorcsoportot tárgyalt, ha rövid időre. Az Azure Virtual Network dokumentációja nagymértékben mutatja be az alábbi témakörök:

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
[az-group-create]: /cli/azure/group#az_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com