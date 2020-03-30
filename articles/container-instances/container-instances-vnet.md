---
title: Tárolócsoport üzembe helyezése az Azure virtuális hálózatára
description: Ismerje meg, hogyan helyezhet üzembe tárolócsoportokat egy új vagy meglévő Azure virtuális hálózatra.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 318576e9b5c5b32bbc993ea16494c938b74bd2f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200061"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Tárolópéldányok üzembe helyezése Azure-beli virtuális hálózatba

[Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos, privát hálózatot biztosít az Azure-beli és helyszíni erőforrásokszámára. Tárolócsoportok üzembe helyezésével egy Azure virtuális hálózat, a tárolók biztonságosan kommunikálhat a virtuális hálózat más erőforrásaival.

Az Azure virtuális hálózatba telepített tárolócsoportok olyan forgatókönyveket engedélyeznek, mint például:

* Közvetlen kommunikáció az azonos alhálózatban lévő tárolócsoportok között
* [Feladatalapú](container-instances-restart-policy.md) számítási feladatok kimenetének küldése a tárolópéldányokból a virtuális hálózat adatbázisába
* Tárolópéldányok tartalmának lekérése a virtuális hálózat [egyik szolgáltatásvégpontjáról](../virtual-network/virtual-network-service-endpoints-overview.md)
* Konténeres kommunikáció a virtuális hálózatban lévő virtuális gépekkel
* Tárolókommunikáció a helyszíni erőforrásokkal [VPN-átjárón](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute-on](../expressroute/expressroute-introduction.md) keresztül

> [!IMPORTANT]
> A tárolócsoport-telepítések egy virtuális hálózatra általában csak az **USA keleti régióiban, usa keleti régióiban, usa déli régióiban és USA nyugati régiójában érhetőel el.** Más régiókban, ahol a szolgáltatás elérhető, a virtuális hálózati telepítések jelenleg előzetes verzióban, az általános rendelkezésre állás tervezett a közeljövőben. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Virtuális hálózatok telepítésének korlátozásai

Bizonyos korlátozások érvényesek, ha tárolócsoportokat telepít egy virtuális hálózatra.

* A tárolócsoportok alhálózatra való központi telepítéséhez az alhálózat nem tartalmazhat más erőforrástípusokat. Távolítsa el az összes meglévő erőforrást egy meglévő alhálózatból, mielőtt tárolócsoportokat telepítene hozzá, vagy hozzon létre egy új alhálózatot.
* Felügyelt [identitás](container-instances-managed-identity.md) nem használható egy virtuális hálózatra telepített tárolócsoportban.
* A virtuális hálózatra telepített tárolócsoportban nem engedélyezheti az [élőség-mintavételt](container-instances-liveness-probe.md) vagy [a készenléti mintavételt.](container-instances-readiness-probe.md)
* A további hálózati erőforrások miatt a tárolócsoport virtuális hálózatra való üzembe helyezése általában lassabb, mint egy szabványos tárolópéldány üzembe helyezése.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

A tároló erőforrás-korlátai eltérhetnek a nem hálózatba helyezett tárolópéldányok korlátaitól ezekben a régiókban. Jelenleg csak a Linux-tárolók támogatottak ehhez a funkcióhoz. A Windows-támogatás tervezett.

### <a name="unsupported-networking-scenarios"></a>Nem támogatott hálózati forgatókönyvek 

* **Azure Load Balancer** – Az Azure Load Balancer elhelyezése a tárolópéldányok előtt egy hálózati tárolócsoportban nem támogatott
* **Virtuális hálózati társviszony-létesítés**
  * A virtuális hálózatok társviszony-létesítése nem működik az ACI számára, ha az ACI virtuális hálózat által társviszonyba eső hálózat nyilvános IP-területet használ. A társviszony-létesített hálózatnak szüksége van egy RFC 1918 privát IP-terület a virtuális hálózatok létesítésének működéséhez. 
  * A virtuális hálózat csak egy másik virtuális hálózathoz társviszonyban
* **Virtuális hálózati forgalom útválasztása** – Egyéni útvonalak nem állíthatók be nyilvános IP-k köré. Az útvonalak beállíthatók annak a delegált alhálózatnak a privát IP-területén belül, amelyben az ACI-erőforrások at telepítik 
* **Hálózati biztonsági csoportok** – Az Azure Container Instances delegált alhálózatra alkalmazott kimenő biztonsági szabályok jelenleg nincsenek érvényben 
* **Nyilvános IP- vagy DNS-címke** – A virtuális hálózatra telepített tárolócsoportok jelenleg nem támogatják a tárolók közvetlenül az internetnek való kihelyezését nyilvános IP-címmel vagy teljesen minősített tartománynévvel
* **Belső névfeloldás** – A virtuális hálózatban lévő Azure-erőforrások névfeloldása a belső Azure DNS-en keresztül nem támogatott

**A hálózati erőforrások törlése** [további lépéseket](#delete-network-resources) igényel, miután telepítette a tárolócsoportokat a virtuális hálózatra.

## <a name="required-network-resources"></a>Szükséges hálózati erőforrások

Három Azure virtuális hálózati erőforrás szükséges a tárolócsoportok virtuális hálózatra történő üzembe helyezéséhez: maga a [virtuális hálózat,](#virtual-network) a virtuális hálózaton belüli [delegált alhálózat](#subnet-delegated) és egy [hálózati profil.](#network-profile) 

### <a name="virtual-network"></a>Virtuális hálózat

A virtuális hálózat határozza meg azt a címteret, amelyben egy vagy több alhálózatot hoz létre. Ezután üzembe helyezheti az Azure-erőforrásokat (például a tárolócsoportokat) a virtuális hálózat alhálózataiba.

### <a name="subnet-delegated"></a>Alhálózat (delegált)

Az alhálózatok a virtuális hálózatot külön címterekre bontják, amelyeket az Azure-erőforrások hoznak létre. Egy vagy több alhálózatot hoz létre egy virtuális hálózaton belül.

A tárolócsoportokhoz használt alhálózat csak tárolócsoportokat tartalmazhat. Amikor először telepít egy tárolócsoportot egy alhálózatra, az Azure delegálja az alhálózatot az Azure Container Instances számára. A delegálást követően az alhálózat csak tárolócsoportokhoz használható. Ha a tárolócsoportoktól eltérő erőforrásokat kísérel meg egy delegált alhálózatra telepíteni, a művelet sikertelen lesz.

### <a name="network-profile"></a>Hálózati profil

A hálózati profil az Azure-erőforrások hálózati konfigurációs sablonja. Meghatározza az erőforrás bizonyos hálózati tulajdonságait, például azt az alhálózatot, amelybe telepíteni kell. Amikor először használja az [az container create][az-container-create] parancsot egy tárolócsoport alhálózatra (és így egy virtuális hálózatra) üzembe helyezéséhez, az Azure létrehoz egy hálózati profilt. Ezután használhatja ezt a hálózati profilt az alhálózat jövőbeli központi telepítéseihez. 

Ha Erőforrás-kezelő sablont, YAML-fájlt vagy programozott módszert szeretne használni egy tárolócsoport alhálózatra történő központi telepítéséhez, meg kell adnia egy hálózati profil teljes Erőforrás-kezelő erőforrás-azonosítóját. Használhatja a korábban létrehozott profilt [az az container create][az-container-create]használatával, vagy létrehozhat egy profilt egy Erőforrás-kezelő sablon használatával (lásd a sablon [példáját](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) és [hivatkozását).](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles) Egy korábban létrehozott profil azonosítójának lekért, használja az [az hálózati profillista][az-network-profile-list] parancsot. 

A következő ábrán több tárolócsoport lett telepítve az Azure Container Instances delegált alhálózatra. Miután üzembe helyezett egy tárolócsoportot egy alhálózatba, további tárolócsoportokat telepíthet hozzá ugyanannak a hálózati profilnak a megadásával.

![Tárolócsoportok egy virtuális hálózaton belül][aci-vnet-01]

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

Az [container create][az-container-create] használatával tárolócsoportokat helyezhet üzembe egy új virtuális hálózatra, és engedélyezheti az Azure-nak, hogy létrehozza a szükséges hálózati erőforrásokat, vagy egy meglévő virtuális hálózatra telepítse. 

### <a name="new-virtual-network"></a>Új virtuális hálózat

Ha új virtuális hálózatra szeretne telepíteni, és az Azure automatikusan létrehozza a hálózati erőforrásokat, adja meg a következőket az [az container create][az-container-create]végrehajtásakor:

* Virtuális hálózat neve
* Virtuális hálózati címelőtag CIDR formátumban
* Alhálózat neve
* Alhálózati címelőtag CIDR formátumban

A virtuális hálózat és az alhálózati cím előtagok határozzák meg a virtuális hálózat és az alhálózat címterét. Ezek az értékek például `10.0.0.0/16`a Classless Inter-Domain Routing (CIDR) jelölésben jelennek meg. Az alhálózatok ról további információt a [Virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése](../virtual-network/virtual-network-manage-subnet.md)című témakörben talál.

Miután üzembe helyezte az első tárolócsoportot ezzel a módszerrel, üzembe helyezheti ugyanabba az alhálózatba a virtuális hálózat és az alhálózat nevének megadásával, vagy az Azure által automatikusan létrehozott hálózati profil megadásával. Mivel az Azure delegálja az alhálózatot az Azure Container Instances, *csak* tárolócsoportokat telepíthet az alhálózatba.

### <a name="existing-virtual-network"></a>Meglévő virtuális hálózat

Tárolócsoport üzembe helyezése meglévő virtuális hálózatra:

1. Hozzon létre egy alhálózatot a meglévő virtuális hálózaton belül, használjon egy meglévő alhálózatot, amelyben egy tárolócsoport már telepítve van, vagy használjon egy meglévő alhálózatot, amely kiürítette az *összes* többi erőforrást
1. Telepítsen egy tárolócsoportot [az az tároló létrehozása,][az-container-create] és adja meg az alábbiak egyikét:
   * Virtuális hálózat neve és alhálózatának neve
   * Virtuális hálózati erőforrás azonosítója és alhálózati erőforrásazonosítója, amely lehetővé teszi egy másik erőforráscsoportból származó virtuális hálózat használatát
   * Hálózati profil neve vagy azonosítója, amelyet az [az hálózati profillista][az-network-profile-list] használatával szerezhet be

Miután üzembe helyezte az első tárolócsoportot egy meglévő alhálózatra, az Azure delegálja az alhálózatot az Azure Container Instances számára. A tárolócsoportokon kívül más erőforrásokat nem telepíthet az adott alhálózatra.

## <a name="deployment-examples"></a>Példák telepítési példák

A következő szakaszok ismertetik, hogyan telepítheti a tárolócsoportokat egy virtuális hálózatra az Azure CLI-vel. A parancspéldák a **Bash** rendszerhéjhoz vannak formázva. Ha egy másik rendszerhéjat, például a PowerShellt vagy a parancssort szeretné előnyben részesíteni, ennek megfelelően módosítsa a sor folytatási karaktereit.

### <a name="deploy-to-a-new-virtual-network"></a>Üzembe helyezés új virtuális hálózatra

Először telepítsen egy tárolócsoportot, és adja meg egy új virtuális hálózat és alhálózat paramétereit. Ha megadja ezeket a paramétereket, az Azure létrehozza a virtuális hálózatot és az alhálózatot, delegálja az alhálózatot az Azure Container-példányokra, és létrehoz egy hálózati profilt is. Az erőforrások létrehozása után a tárolócsoport az alhálózatra lesz telepítve.

Futtassa a következő [az container create][az-container-create] parancsot, amely egy új virtuális hálózat és alhálózat beállításait adja meg. Meg kell adnia egy olyan régióban létrehozott erőforráscsoport nevét, ahol a tárolócsoport-telepítések [elérhetők](#virtual-network-deployment-limitations)egy virtuális hálózatban. Ez a parancs telepíti a nyilvános Microsoft [aci-helloworld][aci-helloworld] tárolót, amely egy statikus weblapot kiszolgáló kis Node.js webkiszolgálót futtat. A következő szakaszban egy második tárolócsoportot telepít ugyanarra az alhálózatra, és teszteli a két tárolópéldány közötti kommunikációt.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Ha ezzel a módszerrel telepíti az új virtuális hálózatot, a központi telepítés eltarthat néhány percig, amíg a hálózati erőforrások jönnek létre. A kezdeti üzembe helyezés után további tárolócsoport-telepítések gyorsabban befejeződnek.

### <a name="deploy-to-existing-virtual-network"></a>Üzembe helyezés meglévő virtuális hálózatra

Most, hogy üzembe helyezett egy tárolócsoportot egy új virtuális hálózatra, telepítsen egy második tárolócsoportot ugyanarra az alhálózatra, és ellenőrizze a két tárolópéldány közötti kommunikációt.

Először az első üzembe helyezett tárolócsoport IP-címét, az *appcontainert:*

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

A kimenetnek meg kell jelenítenie a tárolócsoport IP-címét a magánalhálózatban:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Most állítsa `CONTAINER_GROUP_IP` be a `az container show` parancsot beolvasott IP-re, és hajtsa végre a következő `az container create` parancsot. Ez a második tároló, *a commchecker,* egy `wget` alpesi Linux-alapú rendszerképet futtat, és végrehajtja az első tárolócsoport privát alhálózati IP-címével.

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

A második tároló központi telepítése után húzza le a naplókat, hogy láthassa a `wget` végrehajtott parancs kimenetét:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Ha a második tároló sikeresen kommunikált az elsővel, a kimenetnek hasonlónak kell lennie a következőhöz:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A naplókimenetnek `wget` azt kell mutatnia, hogy képes volt csatlakozni és letölteni az indexfájlt az első tárolóból a helyi alhálózaton lévő privát IP-cím használatával. A két tárolócsoport közötti hálózati forgalom a virtuális hálózaton belül maradt.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Üzembe helyezés meglévő virtuális hálózatra – YAML

Egy tárolócsoportot egy meglévő virtuális hálózatra is telepíthet YAML-fájl, Erőforrás-kezelő sablon vagy más programozott módszer, például a Python SDK használatával. Virtuális hálózat alhálózatára való telepítéshez adjon meg több további tulajdonságot a YAML-ben:

* `ipAddress`: A tárolócsoport IP-címbeállításai.
  * `ports`: A megnyitandó portok, ha vannak ilyenek.
  * `protocol`: A megnyitott port protokollja (TCP vagy UDP).
* `networkProfile`: Megadja a hálózati beállításokat, például egy Azure-erőforrás virtuális hálózatát és alhálózatát.
  * `id`: A teljes Erőforrás-kezelő `networkProfile`erőforrásazonosítója.

Ha egy YAML-fájllal rendelkező virtuális hálózatra szeretne telepíteni egy tárolócsoportot, először be kell szereznie a hálózati profil azonosítóját. Hajtsa végre az [az hálózati profillista][az-network-profile-list] parancsot, és adja meg a virtuális hálózatot és a delegált alhálózatot tartalmazó erőforráscsoport nevét.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

A parancs kimenete a hálózati profil teljes erőforrás-azonosítóját jeleníti meg:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Miután rendelkezik a hálózati profil azonosítójával, másolja a következő YAML-t egy *vnet-deploy-aci.yaml*nevű új fájlba. A `networkProfile`területen `id` cserélje le az értéket az imént beolvasott azonosítóra, majd mentse a fájlt. Ez a YAML létrehoz egy *appcontaineryaml* nevű tárolócsoportot a virtuális hálózatban.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Telepítse a tárolócsoportot az [az container create][az-container-create] paranccsal, és `--file` adja meg a paraméter YAML-fájlnevét:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Miután a központi telepítés befejeződött, futtassa az [az container show][az-container-show] parancsot az állapotának megjelenítéséhez:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="delete-container-instances"></a>Tárolópéldányok törlése

Ha végzett a létrehozott tárolópéldányokkal, törölje őket a következő parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Hálózati erőforrások törlése

Ez a szolgáltatás jelenleg több további parancsot igényel a korábban létrehozott hálózati erőforrások törléséhez. Ha a cikk előző szakaszaiban található példaparancsokat használta a virtuális hálózat és az alhálózat létrehozásához, akkor a következő parancsfájl segítségével törölheti ezeket a hálózati erőforrásokat. A parancsfájl feltételezi, hogy az erőforráscsoport egyetlen virtuális hálózatot tartalmaz egyetlen hálózati profillal.

A parancsfájl végrehajtása előtt `RES_GROUP` állítsa a változót a törölni a virtuális hálózatot és alhálózatot tartalmazó erőforráscsoport nevére. Frissítse a virtuális hálózat nevét, ha `aci-vnet` nem használta a korábban javasolt nevet. A parancsfájl a Bash rendszerhéjhoz van formázva. Ha egy másik rendszerhéjat, például a PowerShellt vagy a parancssort szeretné előnyben részesíteni, ennek megfelelően módosítania kell a változó-hozzárendelést és -elérőket.

> [!WARNING]
> Ez a parancsfájl törli az erőforrásokat! Törli a virtuális hálózatot és a benne található összes alhálózatot. Győződjön meg arról, hogy a parancsfájl futtatása előtt már nincs szüksége a virtuális hálózat *erőforrásaira,* beleértve a benne található alhálózatokat is. A törlést követően **ezek az erőforrások helyreállíthatatlanok.**

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>További lépések

Ha új virtuális hálózatot, alhálózatot, hálózati profilt és tárolócsoportot szeretne telepíteni egy Resource Manager-sablon használatával, olvassa el az [Azure-tárolócsoport létrehozása virtuális hálózattal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)című témakört.

Ebben a cikkben számos virtuális hálózati erőforrást és szolgáltatást tárgyaltunk, bár röviden. Az Azure Virtual Network dokumentációja részletesen ismerteti az alábbi témaköröket:

* [Virtuális hálózat](../virtual-network/manage-virtual-network.md)
* [Alhálózat](../virtual-network/virtual-network-manage-subnet.md)
* [Szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
