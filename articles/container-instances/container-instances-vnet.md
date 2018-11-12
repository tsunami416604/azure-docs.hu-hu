---
title: Egy Azure-beli virtuális hálózatban a tárolópéldányok üzembe helyezése
description: Ismerje meg, hogyan tárolócsoportok egy új vagy meglévő Azure virtuális hálózaton üzembe helyezni.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.openlocfilehash: e2f0d90a0a4384560c0a4126c028761765cb9e45
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288866"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Egy Azure-beli virtuális hálózatban a tárolópéldányok üzembe helyezése

[Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) nyújt biztonságos, privát hálózati, beleértve a szűrési, Útválasztás és társviszony-létesítést az Azure és helyszíni erőforrások. Tárolócsoportok üzembe egy Azure-beli virtuális hálózatban, a tárolók képesek kommunikálni biztonságosan a virtuális hálózatban lévő más erőforrásokra.

Egy Azure-beli virtuális hálózatban üzembe helyezett tárolócsoportok olyan szituációkra, mint engedélyezése:

* Az azonos alhálózaton található tárolócsoportok közötti közvetlen kapcsolat
* Küldés [feladatalapú](container-instances-restart-policy.md) számítási feladat kimenete container Instances szolgáltatásban a virtuális hálózatban lévő adatbázishoz
* A container Instances tartalom lekérése egy [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) a virtuális hálózatban
* Tároló kommunikál a virtuális hálózatban lévő virtuális gépek
* Tároló kommunikál a helyszíni erőforrásokhoz keresztül egy [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és néhány [korlátozások érvényesek a](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="virtual-network-deployment-limitations"></a>Virtuális hálózat üzembe helyezési korlátozásoknak

Bizonyos korlátozások érvényesek, amikor üzembe helyezi a tárolócsoportok egy virtuális hálózatot.

* Windows-tárolók nem támogatottak.
* Tárolócsoportok telepíteni egy alhálózathoz, az alhálózat nem tartalmazhat más erőforrástípusok. Az összes meglévő erőforrások eltávolítása előtt tárolócsoportok hozzá egy meglévő alhálózatot, vagy hozzon létre egy új alhálózatot.
* Tárolócsoportok üzembe helyezni egy virtuális hálózatban jelenleg nem támogatják nyilvános IP-címe vagy DNS-név címke.
* További hálózati erőforrás, mert egy tárolócsoport telepítése egy virtuális hálózathoz általában némileg lassabb, mint a standard szintű tárolópéldány üzembe helyezése.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Bár ez a funkció előzetes verzióban érhető el, az alábbi korlátozások érvényesek a példányok üzembe helyezésekor tárolót egy virtuális hálózathoz.

**Támogatott** régiók:

* Nyugat-Európa (westeurope)
* USA nyugati RÉGIÓJA (westus)

**Nem támogatott** hálózati erőforrások:

* Hálózati biztonsági csoport
* Azure Load Balancer

**Hálózati erőforrás törlése** igényel [további lépéseket](#delete-network-resources) után tárolócsoportok helyezte a virtuális hálózathoz.

## <a name="required-network-resources"></a>Szükséges hálózati erőforrások

Három Azure Virtual Network erőforrás tárolócsoportok üzembe helyezéséhez a virtual Network szükséges: a [virtuális hálózati](#virtual-network) , egy [alhálózati delegált](#subnet-delegated) belül a virtuális hálózat és a egy [hálózati profil](#network-profile). 

### <a name="virtual-network"></a>Virtuális hálózat

Virtuális hálózat határozza meg a címtartományt, amely egy vagy több alhálózatot hoz létre. Majd Azure-erőforrások (például a tárolócsoportok) üzembe helyezése az alhálózatok a virtuális hálózaton.

### <a name="subnet-delegated"></a>Alhálózat (delegált)

Alhálózatok, külön címterek használható a virtuális hálózat szegmentáljon az Azure-erőforrások bennük. Egy virtuális hálózaton belül egy vagy több alhálózatot hoz létre.

A tárolócsoportok használt alhálózat csak tárolócsoportok tartalmazhat. Első üzembe egy tárolócsoport egy alhálózathoz, az Azure ad az Azure Container Instances alhálózat. Meghatalmazott, miután az alhálózat csak a tárolócsoportok használható. Ha eltérő tárolócsoportok erőforrások üzembe helyezése a delegált alhálózathoz kísérli meg, a művelet sikertelen lesz.

### <a name="network-profile"></a>Hálózati profil

Hálózati profil a hálózati konfigurációs sablon az Azure-erőforrásokhoz. Azt adja meg az erőforrás, például az alhálózat, amelybe azt kell telepíteni, bizonyos hálózati tulajdonságok. Első használatakor a [az tároló létrehozása] [ az-container-create] parancsot egy alhálózat (és így a virtuális hálózat) üzembe helyezése egy tárolócsoportot, az Azure hálózati profilt hoz létre az Ön számára. Ezután használhatja a hálózati profilban a későbbiekben az alhálózathoz. 

Resource Manager-sablonnal, YAML-fájlt vagy egy programozható metódus használatával üzembe helyezése egy tárolócsoport egy alhálózathoz, a teljes erőforrás-kezelő erőforrás-azonosító egy hálózati profil van szükség. Használhat egy korábban létrehozott profil [az tároló létrehozása][az-container-create], vagy hozzon létre egy profilt, Resource Manager-sablon használatával (lásd: [referencia](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). A korábban létrehozott profil Azonosítójának lekéréséhez használja a [az hálózati profillista] [ az-network-profile-list] parancsot. 

Az alábbi ábrán több tárolóból álló csoportok az Azure Container Instances delegált alhálózathoz van telepítve. Miután üzembe helyezte egy tárolócsoport egy alhálózathoz, az azonos hálózati profil megadásával további tárolócsoportok telepítheti azt.

![Tárolócsoportok egy virtuális hálózaton belül][aci-vnet-01]

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

Használhat [az tároló létrehozása] [ az-container-create] tárolócsoportok új virtuális hálózat üzembe helyezni, és engedélyezni az Azure az Ön számára a szükséges hálózati erőforrások létrehozása, vagy egy meglévő virtuális hálózaton üzembe helyezni. 

### <a name="new-virtual-network"></a>Új virtuális hálózat

Egy új virtuális hálózaton üzembe helyezni, és automatikusan az Ön számára a hálózati erőforrások létrehozása az Azure rendelkezik, adja meg a következő végrehajtásakor [az tároló létrehozása][az-container-create]:

* Virtuális hálózat neve
* Virtuális hálózat címelőtagjához CIDR formátumban
* Alhálózat neve
* Alhálózat címelőtagot CIDR-formátumban

A virtuális hálózat és alhálózat címelőtagok a címterek a virtuális hálózatot és alhálózatot, illetve adja meg. Ezek az értékek szerepelnek az Classless Inter-Domain Routing (CIDR) formátumban, például `10.0.0.0/16`. Alhálózatok használatáról további információkért lásd: [hozzáadása, módosítása vagy törlése egy virtuális hálózat alhálózatához](../virtual-network/virtual-network-manage-subnet.md).

Miután üzembe helyezte az első tárolócsoport ezzel a módszerrel, telepíthet ugyanahhoz az alhálózathoz a virtuális hálózat és alhálózatok nevének, vagy az Azure automatikusan létrehozza az Ön számára a hálózati profil megadásával. Azure ad Azure Container Instances szolgáltatásban az alhálózaton, mert telepíthet *csak* tárolócsoportok az alhálózathoz.

### <a name="existing-virtual-network"></a>Meglévő virtuális hálózat

Az egy meglévő virtuális hálózaton üzembe helyezni egy tárolócsoport:

1. Hozzon létre egy alhálózatot a meglévő virtuális hálózaton belül, vagy egy meglévő alhálózat az üres *összes* más erőforrásokhoz
1. Üzembe helyezése egy tárolócsoportot [az tároló létrehozása] [ az-container-create] , és adja meg a következők egyikét:
   * Virtuális hálózat és alhálózat neve
   * Virtuális hálózati erőforrás-azonosító és az alhálózat erőforrás-azonosító, amely lehetővé teszi, hogy az egy másik erőforráscsoportban található virtuális hálózatok használatának
   * Hálózati profil neve vagy azonosítója, amelyet szerezhet használatával [az hálózati profil listája][az-network-profile-list]

Miután telepít egy meglévő alhálózat az első tárolócsoport, Azure ad az Azure Container Instances alhálózat. Már nem telepíthet tárolócsoportok naplóátvitelen kívüli egyéb erőforrásokra arra az alhálózatra.

## <a name="deployment-examples"></a>Üzembe helyezési példa

A következő szakaszok ismertetik, hogyan helyezhet üzembe egy virtuális hálózatot az Azure CLI-vel tárolócsoportok. A parancspéldákban vannak formázva a **Bash** rendszerhéjat. Ha inkább egy másik, például a PowerShell vagy az parancssor rendszerhéj, ennek megfelelően módosítsa a folytatási karakterek.

### <a name="deploy-to-a-new-virtual-network"></a>Új virtuális hálózat üzembe helyezése

Először üzembe helyezése egy tárolócsoportot, és adjon meg egy új virtuális hálózatot és alhálózatot a paramétereket. Amikor megadja ezeket a paramétereket, az Azure a virtuális hálózatot és alhálózatot hoz létre erőforrásához biztosít az Azure Container Instances szolgáltatásban az alhálózat és is létrehoz egy hálózati profilt. Ezek az erőforrások létrejönnek, miután a tárolócsoport helyezünk üzembe az alhálózathoz.

Futtassa a következő [az tároló létrehozása] [ az-container-create] parancsot, amely egy új virtuális hálózatot és alhálózatot beállítást határoz meg. Meg kell adnia egy adott régióban létrehozott erőforráscsoport nevét, amely [támogatja](#preview-limitations) tárolócsoportok egy virtuális hálózaton. Ez a parancs üzembe helyezi a [microsoft/aci-helloworld] [ aci-helloworld] tároló, amely egy statikus weblapot kiszolgáló kis Node.js-webkiszolgálót futtat. A következő szakaszban fog üzembe helyezése egy második tárolócsoport ugyanahhoz az alhálózathoz, és a két tárolót a példányok közötti kommunikáció tesztelése.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image microsoft/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Amikor telepít egy új virtuális hálózatra a metódus segítségével, az üzembe helyezés eltarthat néhány percig során jönnek létre a hálózati erőforrásokhoz. A kezdeti telepítés után további csoportban üzembe helyezett gyorsabban befejezéséhez.

### <a name="deploy-to-existing-virtual-network"></a>Meglévő virtuális hálózaton üzembe helyezni

Most, hogy telepítette a tárolócsoport egy új virtuális hálózatra, üzembe helyezése egy második tárolócsoport ugyanahhoz az alhálózathoz, és a két tárolót a példányok közötti kommunikáció ellenőrzéséhez.

Először kérje le az első tároló csoportot helyezett üzembe, IP-címét a *appcontainerben*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

A kimenet a privát alhálózatra kell megjelenítenie a tárolócsoport IP-címét:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Most állítsa `CONTAINER_GROUP_IP` az IP-címhez, akkor kérhető le a `az container show` parancsot, majd hajtsa végre az alábbiakat `az container create` parancsot. A második tárolót *commchecker*, Alpine Linux-alapú lemezkép fut, és végrehajtja a `wget` szemben az első csoport tároló privát alhálózati IP-címet.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

A második tárolót üzembe helyezés befejeztével lekéréses a naplókat, így láthatja a kimenetét a `wget` , a végrehajtott parancs:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Ha a második tárolót sikerült – az első, kimeneti hasonló lesz:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A kimenet kell megjelennie, amely `wget` tudta csatlakozhat, és töltse le az index fájlt az első tároló privát IP-címének használatával a helyi alhálózaton. A két tárolócsoportok közötti hálózati forgalmat a virtuális hálózaton belüli maradt.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Meglévő virtuális hálózat – YAML üzembe helyezése

Egy meglévő virtuális hálózatot egy YAML-fájl használatával is telepítheti egy tárolócsoport. Egy alhálózatot a virtuális hálózatban való üzembe helyezéséhez meg néhány további tulajdonságok a YAML:

* `ipAddress`: Az IP-cím beállításait, a tárolócsoport.
  * `ports`: A portok megnyitásához, ha van ilyen.
  * `protocol`: A protokollt (TCP vagy UDP) a megnyitott port.
* `networkProfile`: Itt adhatja meg a hálózati beállítások, például a virtuális hálózatot és alhálózatot az Azure-beli erőforráshoz.
  * `id`: A teljes erőforrás-kezelő erőforrás-Azonosítóját a `networkProfile`.

Virtuális hálózat egy tárolócsoport telepíteni egy YAML-fájllal, akkor először a hálózati profil Azonosítójának lekéréséhez. Hajtsa végre a [az hálózati profillista] [ az-network-profile-list] parancsot az erőforráscsoport, amely tartalmazza a virtuális hálózat és a delegált alhálózat nevét.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

A parancs kimenete megjeleníti a teljes erőforrás-azonosító, a hálózati profil:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Ha a hálózat profilazonosító, másolja a következő yaml-kódot egy új fájlt *virtuális hálózat üzembe helyezése aci.yaml*. A `networkProfile`, cserélje le a `id` érték csak azonosítójú beolvassa, majd mentse a fájlt. A YAML létrehoz egy tárolócsoport nevű *appcontaineryaml* a virtuális hálózaton.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: microsoft/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Üzembe helyezése az a tárolócsoportot a [az tároló létrehozása] [ az-container-create] a YAML-fájl nevét, a parancsot a `--file` paramétert:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Az üzembe helyezés befejezése után futtassa a [az container show] [ az-container-show] parancsot annak állapotának megjelenítése:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                     IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   microsoft/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="delete-container-instances"></a>Tárolópéldányok törlése

Ha elkészült a container instances használata létrehozott, törölheti őket az alábbi parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Hálózati erőforrások törlése

A kezdeti előzetes verzióját a funkció számos további parancsok a hálózati erőforrások törli a korábban létrehozott van szükség. Ha ez a cikk korábbi szakaszaiban a Példaparancsok használatban a virtuális hálózatot és alhálózatot létrehozni, majd használhatja a következő parancsfájl hálózati erőforrások törlése.

Parancsfájl végrehajtása előtt állítsa be a `RES_GROUP` változó neve lesz az erőforráscsoport, amely tartalmazza a virtuális hálózatot és alhálózatot, amelyet törölni kell. A szkript a Bash rendszerhéj van formázva. Ha inkább egy másik, például a PowerShell vagy az parancssor rendszerhéj, szüksége változó-hozzárendelés és leíró ennek megfelelően módosítsa.

> [!WARNING]
> Ez a parancsfájl törli az erőforrást! Törli a virtuális hálózat és az összes alhálózatot tartalmaz. Győződjön meg, hogy már nincs szüksége *bármely* az erőforrások a virtuális hálózatban, beleértve az esetleges olyan alhálózatokra, tartalmaz, ez a szkript futtatása előtt. Törölt, **ezeket az erőforrásokat is helyreállíthatatlan**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --remove delegations 0

# Delete the subnet
az network vnet subnet delete --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>További lépések

Több virtuális hálózati erőforrások és szolgáltatások ebben a cikkben aktorcsoportot tárgyalt, ha rövid időre. Az Azure Virtual Network dokumentációja nagymértékben mutatja be az alábbi témakörök:

* [Virtuális hálózat](../virtual-network/manage-virtual-network.md)
* [Alhálózat](../virtual-network/virtual-network-manage-subnet.md)
* [Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list