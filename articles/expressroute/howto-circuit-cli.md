---
title: 'Egy Azure ExpressRoute-kör létrehozása és módosítása: parancssori felület |} A Microsoft Docs'
description: Ez a cikk bemutatja, hogyan hozhat létre, üzembe helyezése, győződjön meg arról, frissítése, törlése és parancssori felület használatával egy ExpressRoute-kapcsolatcsoport megszüntetése.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: a53fe43365100c6d71fcc2b9e0944a221adf188d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249234"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport parancssori felület használatával


Ez a cikk ismerteti az Azure ExpressRoute-kapcsolatcsoport létrehozása a parancssori felület (CLI) használatával. Ez a cikk emellett bemutatja, hogyan ellenőrizze az állapotot, update vagy delete és a egy kapcsolatcsoport megszüntetése. Ha szeretne egy másik módszer használható az ExpressRoute-Kapcsolatcsoportok, kiválaszthatja a cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Előkészületek

* A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).
* Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.

## <a name="create"></a>Létrehozása és kiépítése az ExpressRoute-kapcsolatcsoport

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Jelentkezzen be az Azure-fiókjával, és válassza ki az előfizetését

A konfiguráció első lépésként jelentkezzen be az Azure-fiókjával. Az alábbi példák segítségével segíthet a kapcsolódásban:

```azurecli
az login
```

Keresse meg a fiókot az előfizetésekben.

```azurecli
az account list
```

Válassza ki az előfizetést, amelynek meg szeretné ExpressRoute-kapcsolatcsoport létrehozása.

```azurecli
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. A támogatott szolgáltatók, a helyek és a sávszélesség-lista lekérése

ExpressRoute-kapcsolatcsoport létrehozása, birtokában támogatott kapcsolatszolgáltatók, helyek és sávszélesség-lehetőségek listája. A CLI parancs "az network express-route lista szolgáltatók" értéket ad vissza, ezt az információt fogja használni a későbbi lépésekben:

```azurecli
az network express-route list-service-providers
```

A válasz a következő példához hasonló:

```azurecli
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

Ellenőrizze a válasz megtekintéséhez, hogy a kapcsolatszolgáltató szerepel-e. Jegyezze fel a kapcsolatcsoport létrehozásakor kell a következő információkat:

* Name (Név)
* PeeringLocations
* BandwidthsOffered

Most már készen áll az ExpressRoute-kapcsolatcsoport létrehozása.

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute-kapcsolatcsoport létrehozása

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoport számlázása a szolgáltatáskulcs pillanatától kezdve. Hajtsa végre ezt a műveletet, amikor üzembe helyezi a kapcsolatcsoportot készen áll-e a kapcsolat szolgáltatóját.
> 
> 

Ha még nem rendelkezik egy erőforráscsoport, egy az ExpressRoute-kapcsolatcsoport létrehozása előtt kell létrehoznia. Létrehozhat egy erőforráscsoportot a következő parancs futtatásával:

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

Az alábbi példa bemutatja, hogyan hozhat létre egy 200 Mbps ExpressRoute-kapcsolatcsoporton keresztül Equinix szilícium-völgy. Másik szolgáltatóhoz, és különböző beállításokat használja, ha helyettesítse be ezt az információt, amikor a kérést. 

Ügyeljen arra, hogy megadja a helyes Termékváltozat-szint és a Termékváltozat-család:

* Termékváltozat-szint határozza meg, hogy egy ExpressRoute-standard vagy az ExpressRoute prémium bővítmény engedélyezve van. Megadhatja a "Standard", a standard Termékváltozat vagy "Prémium" beolvasni a premium bővítményt.
* Termékváltozat-család határozza meg a számlázási. Megadhat "Metereddata" a forgalmi díjas csomag és a "Unlimiteddata" korlátlan adatforgalmú. Módosíthatja a számlázási típusát, a "Metereddata", "Unlimiteddata", de nem módosíthatja a típusát, a 'Unlimiteddata', "Metereddata".


Az ExpressRoute-kapcsolatcsoport számlázása a szolgáltatáskulcs pillanatától kezdve. Az alábbi példa tulajdonképpen egy kérelem egy új kulcsot:

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A válasz tartalmazza a kulcsot.

### <a name="4-list-all-expressroute-circuits"></a>4. Minden ExpressRoute-kapcsolatcsoport listázása

Az összes Ön által létrehozott ExpressRoute-Kapcsolatcsoportok listájának lekéréséhez futtassa a "az network express-route list" parancsot. Bármikor ezt az információt a következő paranccsal kérheti le. Minden kapcsolatcsoportra listázásához, a hívást nélkül.

```azurecli
az network express-route list
```

A szolgáltatás kulcs szerepel az *ServiceKey* mezőt, a válasz.

```azurecli
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

Megjelenik a részletes leírását az összes paramétert, a parancs használatával futtassa a "-h" paraméter.

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. A kulcs küldése a kapcsolatszolgáltató a kiépítés

"ServiceProviderProvisioningState" provisioning service-szolgáltató oldalán aktuális állapotát ismerteti. Az állapot az állapot biztosít a Microsoft oldalán. További információkért lásd: a [munkafolyamatok cikk](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolatcsoport a következő állapotban van:

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

A kapcsolatcsoport változik a következő állapotot, amikor a kapcsolatszolgáltató van folyamatban, amely lehetővé teszi az Ön számára:

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

ExpressRoute-kapcsolatcsoport segítségével tudja meg a következő állapotban kell lennie:

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Rendszeresen ellenőrizze a kapcsolatcsoport kulcs állapotát és az állapot

A kapcsolatcsoport kulcs állapotát és az állapot ellenőrzése jelzi, ha a szolgáltató engedélyezve van a kapcsolatcsoport. A kapcsolatcsoport konfigurálása után a "ServiceProviderProvisioningState" jelenik meg "Kiépítve", az alábbi példában látható módon:

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

A válasz a következő példához hasonló:

```azurecli
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

Részletes útmutatásért tekintse meg a [ExpressRoute-Kapcsolatcsoportok útválasztási konfigurációja](howto-routing-cli.md) cikk létrehozásához és módosításához a kapcsolatcsoport társviszony-létesítéseket.

> [!IMPORTANT]
> Ezek az utasítások csak 2 réteg szolgáltatás kínáló szolgáltatóknál létrehozott Kapcsolatcsoportok vonatkoznak. Ha használja a szolgáltató által kínált felügyelt réteg (általában egy IP VPN, mint az MPLS) 3 szolgáltatások, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást Ön helyett.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal

Ezután egy virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoportot. Használja a [virtuális hálózatok összekapcsolása az ExpressRoute-Kapcsolatcsoportok](howto-linkvnet-cli.md) cikk.

## <a name="modify"></a>Az ExpressRoute-Kapcsolatcsoportok módosítása

Egyes ExpressRoute-kapcsolatcsoport tulajdonságainak kapcsolat befolyásolása nélkül módosíthatja. A következő módosításokat üzemkimaradás nélkül végezheti el:

* Engedélyezheti vagy az ExpressRoute prémium bővítmény letiltása az ExpressRoute-kapcsolatcsoport esetében.
* Az ExpressRoute-kapcsolatcsoport sávszélességét, növelheti, feltéve, hogy kapacitás érhető el a porton. Alacsonyabb verziójúra változtatása a kapcsolatcsoport sávszélességétől azonban nem támogatott. 
* A mérési terv a korlátlan díjas módosítható. Mérési csomag módosítása a korlátlan, a forgalmi díjas adatokhoz azonban nem támogatott.
* Engedélyezheti és letilthatja az *klasszikus működés engedélyezése*.

További információ a korlátok és korlátozások: a [ExpressRoute – gyakori kérdések](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény engedélyezése

Az ExpressRoute prémium bővítmény a következő parancs segítségével engedélyezheti a meglévő kapcsolatcsoport:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

A kapcsolatcsoport most már engedélyezve van az ExpressRoute prémium bővítmény szolgáltatásokat. Az első lépések a premium bővítmény funkció számlázása, amint a parancs már sikeresen futott.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény letiltása

> [!IMPORTANT]
> Ez a művelet sikertelen lehet, ha erőforrást, amely nagyobb, mint a megengedett a standard szintű kapcsolatcsoportot használ.
> 
> 

Mielőtt letiltaná az ExpressRoute prémium bővítmény, ismerje meg a következő feltételeknek:

* Mielőtt standard Visszaléptetés a prémium szintű, győződjön meg arról, hogy legfeljebb 10 virtuális hálózatokat a kapcsolatcsoporthoz kapcsolt. Ha több mint 10, a frissítési kérelem meghiúsul, és a megadott prémium szintű alapján számlázunk.
* Más geopolitikai régiókban lévő összes virtuális hálózatot kell leválasztása. Ha Ön nem leválasztása a virtuális hálózatok, a frissítési kérelem sikertelen lesz, és a Díjszámítás, prémium szintű díjakat.
* Az útvonaltábla kisebb, mint 4000 útvonalak privát társviszony-létesítés kell lennie. Ha az útválasztási táblázat mérete nagyobb, mint 4000, csökken a BGP-munkamenetben. A munkamenet nem fog újra engedélyezve, amíg a meghirdetett előtagok száma nem éri a 4000.

Az ExpressRoute prémium bővítmény a meglévő kapcsolatcsoport letilthatja az alábbi példa szerint:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-kapcsolatcsoport sávszélességét frissítése

A támogatott sávszélesség-lehetőségek a szolgáltató, ellenőrizze a [ExpressRoute – gyakori kérdések](expressroute-faqs.md). Választhat a meglévő kapcsolatcsoport méreténél nagyobb méretű.

> [!IMPORTANT]
> Ha nincs elegendő kapacitás a meglévő porton, akkor előfordulhat, hogy hozza létre újból az ExpressRoute-kapcsolatcsoporthoz. A kapcsolatcsoport nem frissíthető, ha nincsenek további kapacitás érhető el az adott helyhez.
>
> Megszakítás nélküli ExpressRoute-kapcsolatcsoport sávszélességét nem csökkenthető. Alacsonyabb verziójúra változtatása sávszélesség megköveteli, hogy az ExpressRoute-kapcsolatcsoport megszüntetése, és ezután építse ki újra a ExpressRoute-kapcsolatcsoporttal.
>

Miután eldöntötte, hogy van szüksége, használja az alábbi parancsot a kapcsolatcsoport átméretezése mérete:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

A kapcsolatcsoport van méretezni a Microsoft oldalán. Ezután lépjen kapcsolatba a kapcsolatszolgáltató frissíteni a saját oldalán, a módosítás megfelelő a konfigurációk. Miután végrehajtotta ezt az értesítést, megkezdjük a számlázást, a frissített sávszélesség-beállítás.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>A Termékváltozat a áthelyezése díjköteles korlátlan

Az ExpressRoute-kapcsolatcsoport SKU-ja a következő példa használatával módosíthatja:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és Resource Manager-környezetben való hozzáférés szabályozásához

Tekintse át a következő témakör utasításait [helyezze át az ExpressRoute-Kapcsolatcsoportok a klasszikusból a Resource Manager-alapú üzemi modellbe](expressroute-howto-move-arm.md).

## <a name="delete"></a>A megszüntetés és a egy ExpressRoute-kapcsolatcsoport törlése

Megszüntetése, és a egy ExpressRoute-kapcsolatcsoport törlése, győződjön meg arról, hogy megértette a következő feltételeknek:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet nem sikerül, ellenőrizze, hogy ha bármely virtuális hálózatokhoz kapcsolódnak-e a kapcsolatcsoportot.
* Ha az ExpressRoute kapcsolatcsoport szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépített**,-e, hogy azok oldalán a kapcsolatcsoport megszüntetése a szolgáltató. Továbbra is erőforrásokat tartalékolnia, és addig, amíg a szolgáltató befejeződött, a kapcsolatcsoport megszüntetése, és értesítést küld nekünk fel díjat.
* Törölje a kapcsolatcsoportot, ha a szolgáltató eltávolította a kapcsolatcsoportot. Kapcsolatcsoport megszüntetése van, az üzembe helyezési állapota szolgáltató beállítás **nincs kiépítve**. Ez leállítja a kapcsolatcsoport számlázását.

Az ExpressRoute-kapcsolatcsoport a következő parancs futtatásával törölheti:

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a kapcsolatcsoportot, győződjön meg arról, hogy a következő feladatokat végezheti el:

* [Útválasztás az ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása](howto-routing-cli.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoport](howto-linkvnet-cli.md)
