---
title: 'ExpressRoute-kapcsolat létrehozása és módosítása: Azure CLI'
description: Ez a cikk bemutatja, hogyan hozhat létre, kiépítése, ellenőrzése, frissítése, törlése és a CLI használatával expresszroute-kapcsolat.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: b967e1d8751a9c6a5214fef5241d57e954ad9f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476151"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>ExpressRoute-kapcsolatcsoport létrehozása és módosítása a CLI-vel


Ez a cikk azt ismerteti, hogyan hozhat létre Egy Azure ExpressRoute-áramkört a parancssori felület (CLI) használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az állapotát, frissítését vagy törlését és megszüntetését egy kapcsolatcsoport. Ha más módszert szeretne használni az ExpressRoute-áramkörök használatához, a cikket az alábbi listából választhatja ki:

> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Előkészületek

* A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kapcsolat létrehozása és kiépítése

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést

A konfiguráció megkezdéséhez jelentkezzen be az Azure-fiókjába. Ha a CloudShell "Try It", automatikusan be van jelentkezve. A csatlakozáshoz az alábbi példák segítségével segítséget nyújt a csatlakozáshoz:

```azurecli-interactive
az login
```

Keresse meg a fiókot az előfizetésekben.

```azurecli-interactive
az account list
```

Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört szeretne létrehozni.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. A támogatott szolgáltatók, helyek és sávszélességek listájának beszerezni

ExpressRoute-kapcsolat létrehozása előtt szüksége van a támogatott kapcsolatszolgáltatók, helyek és sávszélesség-beállítások listájára. A CLI `az network express-route list-service-providers` parancs ezt az információt adja vissza, amelyet a későbbi lépésekben fog használni:

```azurecli-interactive
az network express-route list-service-providers
```

A válasz a következő példához hasonló:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Ellenőrizze a választ, hogy szerepel-e a kapcsolódási szolgáltató a listában. Jegyezze fel a következő információkat, amelyekre szüksége lesz, amikor létrehoz egy áramkört:

* Név
* Társviszony-létesítési helyek
* Kínált sávszélességek

Most már készen áll egy ExpressRoute-kapcsolat létrehozására.

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute-kapcsolat létrehozása

> [!IMPORTANT]
> Az ExpressRoute-kapcsolat számlázása a szolgáltatáskulcs kiadásának pillanatától kezdve történik. Akkor hajtsa végre ezt a műveletet, ha a kapcsolatszolgáltató készen áll az áramkör kiépítésére.
>
>

Ha még nem rendelkezik erőforráscsoporttal, létre kell hoznia egyet az ExpressRoute-kapcsolat létrehozása előtt. Erőforráscsoportot a következő parancs futtatásával hozhat létre:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

A következő példa bemutatja, hogyan hozhat létre egy 200 Mbps-es ExpressRoute-áramkört az Equinixen keresztül a Szilícium-völgyben. Ha más szolgáltatót és más beállításokat használ, ezt az információt a kérés benyújtásakor helyettesítse.

Győződjön meg arról, hogy a megfelelő Termékváltozat-szintet és termékváltozat-családot adja meg:

* A Termékváltozat-szint határozza meg, hogy az ExpressRoute-áramkör helyi, standard vagy prémium szintű.SKU tier determines whether an ExpressRoute circuit is [Local,](expressroute-faqs.md#expressroute-local)Standard or [Premium.](expressroute-faqs.md#expressroute-premium) Megadhatja *a helyi*, *standard* vagy *prémium*értéket.
* Termékváltozat család határozza meg a számlázási típus. Megadhatja *metereddata* egy forgalmi díjas adatcsomag és *Unlimiteddata* korlátlan adatcsomag. A számlázási típust *Metereddata-ról* *Unlimiteddata-ra módosíthatja,* de a típust nem módosíthatja *Korlátlan adatról* *Metereddata-ra.* A *helyi* áramkör csak *Korlátlan adat.*


Az ExpressRoute-kapcsolat számlázása a szolgáltatáskulcs kiadásának pillanatától kezdve történik. A következő példa egy új szolgáltatáskulcsra vonatkozó kérelem:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A válasz tartalmazza a szolgáltatáskulcsot.

### <a name="4-list-all-expressroute-circuits"></a>4. Az összes ExpressRoute-kapcsolat listázása

A létrehozott ExpressRoute-áramkörök listájának leválasztásához `az network express-route list` futtassa a parancsot. Ezt az információt bármikor bekérheti ezzel a paranccsal. Az összes kapcsolat listázásához kezdeményezze paraméterek nélküli hívást.

```azurecli-interactive
az network express-route list
```

A szolgáltatáskulcs a válasz *ServiceKey* mezőjében található.

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Az összes paraméter részletes leírását a parancs "-h" paraméter rel történő futtatásával kaphatja meg.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Küldje el a szolgáltatáskulcsot a kapcsolódási szolgáltatónak a kiépítéshez

A "ServiceProviderProvisioningState" a szolgáltatói oldalon a kiépítés aktuális állapotáról nyújt tájékoztatást. Az állapot biztosítja az állapotot a Microsoft oldalán. További információt a [Munkafolyamatok című cikkben](expressroute-workflows.md#expressroute-circuit-provisioning-states)talál.

Új ExpressRoute-kapcsolat létrehozásakor az áramkör a következő állapotban van:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Az áramkör a következő állapotra változik, amikor a kapcsolatszolgáltató az Ön számára engedélyezi azt:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Ahhoz, hogy egy ExpressRoute-áramkört használhasson, a következő állapotban kell lennie:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Rendszeresen ellenőrizze az áramköri kulcs állapotát és állapotát

Az áramköri kulcs állapotának és állapotának ellenőrzése jelzi, ha a szolgáltató engedélyezte a kapcsolatcsoport használatát. Az áramkör konfigurálása után a "ServiceProviderProvisioningState" "Kiépített" formában jelenik meg, ahogy az a következő példában látható:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

A válasz a következő példához hasonló:

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Az útválasztási konfiguráció létrehozása

Lépésenkénti útmutatást az [ExpressRoute-áramkör-útválasztási konfigurációs](howto-routing-cli.md) cikkben talál a kapcsolatlétesítések létrehozásáról és módosításáról.

> [!IMPORTANT]
> Ezek az utasítások csak a 2. Ha olyan szolgáltatót használ, amely 3.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Virtuális hálózat összekapcsolása ExpressRoute-áramkörrel

Ezután kapcsolja össze a virtuális hálózatot az ExpressRoute-kapcsolattal. Használja a [virtuális hálózatok összekapcsolása ExpressRoute-áramkörök cikket.](howto-linkvnet-cli.md)

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute-kapcsolat módosítása

Az ExpressRoute-áramkör bizonyos tulajdonságait a kapcsolat befolyásolása nélkül módosíthatja. A következő módosításokat állásidő nélkül hajthatja végre:

* Az ExpressRoute-alapú prémium szintű bővítményt engedélyezheti vagy letilthatja az ExpressRoute-kapcsolathoz.
* Növelheti az ExpressRoute-kapcsolat sávszélességét, feltéve, hogy a porton rendelkezésre áll kapacitás. Az áramkör sávszélességének visszaminősítése azonban nem támogatott.
* A mérési terv forgalmi díjas adatokról korlátlan adatra módosítható. Azonban a mérési terv módosítása korlátlan adat forgalmi díjas adatok nem támogatott.
* Engedélyezheti és letilthatja *a Klasszikus műveletek engedélyezése .*

A korlátozásokról és korlátozásokról az [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakörben talál további információt.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény engedélyezése

Az ExpressRoute prémium szintű bővítményt a meglévő áramkörhöz a következő paranccsal engedélyezheti:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

A kapcsolatcsoport mostantól engedélyezve van az ExpressRoute prémium szintű bővítményfunkcióival. A mint a parancs sikeresen futott, megkezdjük a prémium szintű bővítményfunkció számlázását.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény letiltása

> [!IMPORTANT]
> Ez a művelet sikertelen lehet, ha olyan erőforrásokat használ, amelyek nagyobbak, mint a szabványos kapcsolatcsoport megengedett.
>
>

Az ExpressRoute prémium bővítmény letiltása előtt ismerje meg az alábbi feltételeket:

* Mielőtt prémiumról normálra minősítene, győződjön meg arról, hogy kevesebb, mint 10 virtuális hálózat kapcsolódik a kapcsolatcsoporthoz. Ha 10-nél több, a frissítési kérelem sikertelen lesz, és prémium díjakat számlázunk.
* Le kell kapcsolnia a többi geopolitikai régió összes virtuális hálózatát. Ha nem oldja meg az összes virtuális hálózat összekapcsolást, a frissítési kérelem sikertelen lesz, és prémium díjakat számlázunk.
* Az útvonaltáblának 4000-nél kisebb útvonalnak kell lennie a privát társviszony-létesítéshez. Ha az útvonaltábla mérete nagyobb, mint 4000 útvonal, a BGP-munkamenet csökken. A munkamenet csak akkor lesz újra engedélyezve, ha a hirdetett előtagok száma 4000 alá csökken.

A meglévő kapcsolatcsoport ExpressRoute prémium szintű bővítményét a következő példával tilthatja le:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-áramkör sávszélességének frissítése

A szolgáltató számára támogatott sávszélesség-beállításokat az [ExpressRoute gyakori kérdések című területen](expressroute-faqs.md)talál. A meglévő áramkör méreténél nagyobb méretet választhat.

> [!IMPORTANT]
> Ha a meglévő porton nincs megfelelő kapacitás, előfordulhat, hogy újra létre kell hoznia az ExpressRoute-áramkört. Nem frissítheti a kapcsolatcsoport, ha nincs további kapacitás áll rendelkezésre az adott helyen.
>
> Az ExpressRoute-áramkörök sávszélessége megszakítás nélkül nem csökkenthető. A sávszélesség visszaminősítéséhez az ExpressRoute-kapcsolat megszüntetéséhez, majd egy új ExpressRoute-kapcsolat újbóli kiépítéséhez.
>

Miután eldöntötte a szükséges méretet, a következő paranccsal méretezze át az áramkört:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Az áramkör mérete a Microsoft oldalán van. Ezután vegye fel a kapcsolatot a kapcsolatszolgáltatóval, hogy frissítse az ő oldalukon lévő konfigurációkat, hogy megfeleljenek ennek a változásnak. Az értesítés után megkezdjük a frissített sávszélesség-beállítás számlázását.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>A termékváltozat áthelyezése forgalmi díjasról korlátlanra

Az ExpressRoute-kapcsolat termékváltozatát a következő példával módosíthatja:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és az Erőforrás-kezelő környezethez való hozzáférés szabályozása

Tekintse át a [Move ExpressRoute-áramkörök utasításait a klasszikustól az Erőforrás-kezelő telepítési modelljéig.](expressroute-howto-move-arm.md)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolatcsoport megszüntetése és törlése

ExpressRoute-kapcsolat megszüntetéséhez és törléséhez győződjön meg arról, hogy megértette az alábbi feltételeket:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet sikertelen, ellenőrizze, hogy vannak-e virtuális hálózatok a kapcsolatcsoporthoz.
* Ha az ExpressRoute-kapcsolatszolgáltató létesítési állapota **Kiépítés** vagy **Kiépítve,** együtt kell működnie a szolgáltatóval az adott kapcsolat i. Továbbra is fenntartjuk az erőforrásokat, és kiszámlázunk Önnek, amíg a szolgáltató be nem fejezi a kapcsolatcsoport kiürítését, és nem értesíti kedélyünket.
* Törölheti a csoportot, ha a szolgáltató megszüntette a csoportot. Ha egy kapcsolatcsoport ki van építve, a szolgáltató létesítési állapota **Nincs kiépítve.** Ez leállítja a kapcsolatcsoport számlázását.

Az ExpressRoute-áramköra a következő parancs futtatásával törölhető:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>További lépések

A kapcsolatcsoport létrehozása után győződjön meg arról, hogy a következő feladatokat követte el:

* [Útválasztás létrehozása és módosítása az ExpressRoute-kapcsolathoz](howto-routing-cli.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-áramkörrel](howto-linkvnet-cli.md)
