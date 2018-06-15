---
title: 'Létrehozása és módosítása az Azure ExpressRoute-kapcsolatcsoportot: CLI |} Microsoft Docs'
description: Ez a cikk ismerteti, hogyan hozzon létre, kiépítéséhez, győződjön meg arról, frissítése, törlése és kiosztásának megszüntetése parancssori felület használatával ExpressRoute-kapcsolatcsoportot.
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
ms.openlocfilehash: cd4e31336fd0e90b13f1c3984de89f24e65b052b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23933248"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Létrehozásához és módosításához az ExpressRoute-kapcsolatcsoportot parancssori felület használatával


Ez a cikk ismerteti, hogyan hozhat létre Azure ExpressRoute-kapcsolatcsoportot a parancssori felület (CLI) használatával. Ez a cikk is bemutatja, hogyan ellenőrizze az állapot, update vagy delete és az expressroute-kapcsolatcsoporthoz kiosztásának megszüntetése. Ha más módszert használhatja a ExpressRoute-Kapcsolatcsoportok, választhat a cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Előkészületek

* A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI 2.0-s verziójának telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI 2.0-s verziójának használatába](/cli/azure/get-started-with-azure-cli).
* Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.

## <a name="create"></a>Hozzon létre, és helyezze üzembe az ExpressRoute-kapcsolatcsoportot

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Jelentkezzen be az Azure-fiókjával, és jelölje ki az előfizetését

A konfiguráció első lépésként jelentkezzen be az Azure-fiókjával. Az alábbi példák segítségével csatlakozzon:

```azurecli
az login
```

Keresse meg a fiókot az előfizetésekben.

```azurecli
az account list
```

Válassza ki az előfizetést, amely számára ExpressRoute-kapcsolatcsoportot létrehozni kívánja.

```azurecli
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. A támogatott szolgáltatók, a helyek és a sávszélesség listájának lekérdezése

ExpressRoute-kapcsolatcsoportot létrehozni, meg kell támogatott kapcsolat szolgáltatókat, a helyek és a sávszélesség-beállítások listája. A parancssori felület parancs "az hálózati expressroute lista--szolgáltatók" ezt az információt fogja használni a későbbi lépésekben adja vissza:

```azurecli
az network express-route list-service-providers
```

A rendszer a választ az alábbi példához hasonló:

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

Ellenőrizze a válasz szerepel-e a kapcsolat szolgáltatóját. Jegyezze fel az expressroute-kapcsolatcsoporthoz létrehozásakor kell a következő információkat:

* Name (Név)
* PeeringLocations
* BandwidthsOffered

Most már készen áll az ExpressRoute-kapcsolatcsoportot létrehozni.

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute-kapcsolatcsoport létrehozása

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoportot lesz számlázva abban a pillanatban a szolgáltatási kulcs ad ki. Hajtsa végre ezt a műveletet, amikor a kapcsolat szolgáltatójánál kiépíteni a kapcsolat készen áll.
> 
> 

Ha még nem rendelkezik egy erőforráscsoport, kell létrehoznia egyet az ExpressRoute-kapcsolatcsoportot létrehozása előtt. Létrehozhat egy erőforráscsoportot a következő parancs futtatásával:

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

A következő példa bemutatja, hogyan szilícium Valley egy 200 MB/s Equinix keresztül ExpressRoute-kapcsolatcsoportot létrehozni. Különböző szolgáltatók és más beállítások használata, amikor a kérést helyettesítse be ezt az információt. 

Győződjön meg arról, hogy megadja a helyes SKU réteg és SKU-család:

* Termékváltozat szint határozza meg, egy ExpressRoute standard vagy ExpressRoute prémium bővítmény engedélyezve van-e. A standard Termékváltozat vagy "Prémium" lekérése a prémium szintű bővítmény "Standard" adhatja meg.
* SKU-család határozza meg a számlázási. Megadhat "Metereddata" mért adatforgalmi és a "Unlimiteddata" egy adatforgalmi. Módosíthatja a számlázási típus az "Metereddata" a "Unlimiteddata", de nem módosíthatja a típusát, a "Unlimiteddata" a "Metereddata".


Az ExpressRoute-kapcsolatcsoportot lesz számlázva abban a pillanatban a szolgáltatási kulcs ad ki. Az alábbi példa tulajdonképpen egy kérelem egy új szolgáltatás kulcs:

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A válasz tartalmazza a szolgáltatási kulcs.

### <a name="4-list-all-expressroute-circuits"></a>4. A lista összes ExpressRoute-Kapcsolatcsoportok

Minden létrehozott ExpressRoute-Kapcsolatcsoportok listájának lekéréséhez futtassa a "az hálózati expressroute list" parancsot. Ez a parancs használatával ezt az információt bármikor kérheti le. Összes áramkör listázásához, a hívást paraméter nélkül.

```azurecli
az network express-route list
```

A szolgáltatás kulcs szerepel az *ServiceKey* mezőjét, a válasz.

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

Részletes leírását, a paraméterek kaphat a parancs segítségével futtassa a "-h" paraméter.

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. A szolgáltatás kulcs küldése a kapcsolat szolgáltatójánál történő üzembe helyezéséhez

"ServiceProviderProvisioningState" szolgáltatói oldalán kiépítés aktuális állapotára vonatkozó információkat nyújt. Az állapot a Microsoft oldalon az állapot biztosít. További információkért lásd: a [munkafolyamatok cikk](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolat a következő állapotban van:

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

A kapcsolatcsoport módosítja a következő állapotot, ha a kapcsolat szolgáltatójánál folyamatban van, lehetővé téve az Ön:

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Meg kell tudni használni az ExpressRoute-kapcsolatcsoportot a következő állapotban kell lennie:

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Ellenőrizze rendszeresen a kapcsolatcsoport kulcs állapotát és az állapot

Az állapot és a kapcsolatcsoport kulcs állapotának ellenőrzése értesíti Önt arról, amikor a szolgáltató a kapcsolatcsoport engedélyezve van. A kapcsolat konfigurálása után "ServiceProviderProvisioningState" jelenik meg "Kiépítve", a következő példában látható módon:

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

A rendszer a választ az alábbi példához hasonló:

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

### <a name="7-create-your-routing-configuration"></a>7. Az útválasztó-konfiguráció létrehozása

Részletes útmutatásért lásd: a [ExpressRoute-áramkör útválasztási konfigurációja](howto-routing-cli.md) cikk létrehozásához és módosításához a kapcsolatcsoport esetében.

> [!IMPORTANT]
> Ezek az utasítások csak a szolgáltatók által biztosított réteg 2 internetkapcsolati szolgáltatás használatával létrehozott kapcsolatok vonatkoznak. A szolgáltató által kezelt használata réteg (általában az IP VPN, például az MPLS) 3 szolgáltatások, a kapcsolat szolgáltatójánál konfigurálja és kezeli az Ön útválasztást.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal

A következő csatolja az ExpressRoute-kapcsolatcsoportot egy virtuális hálózatot. Használja a [virtuális hálózatok összekapcsolása ExpressRoute-Kapcsolatcsoportok](howto-linkvnet-cli.md) cikk.

## <a name="modify"></a>Egy ExpressRoute-kapcsolatcsoportot módosítása

ExpressRoute-kapcsolatcsoportot egyes tulajdonságait módosíthatja kapcsolat befolyásolása nélkül. Állásidő nélkül a következő módosításokat végezheti el:

* Engedélyezheti vagy ExpressRoute prémium bővítményeket tiltsa le az ExpressRoute-kapcsolatcsoport esetében.
* Az ExpressRoute-kapcsolatcsoportot sávszélességét növelheti, feltéve, hogy kapacitás érhető el a port. Alacsonyabb verziójúra változtatása a sávszélességet a kapcsolat azonban nem támogatott. 
* A mérési terv díjköteles adatokból adatforgalmi módosítható. A mérési terv módosítása az korlátlan adatforgalom díjköteles adatok azonban nem támogatott.
* Engedélyezheti és letilthatja *klasszikus műveletek engedélyezése*.

Korlátai és korlátozásai további információkért tekintse meg a [ExpressRoute – gyakori kérdések](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>A prémium szintű ExpressRoute-bővítmény engedélyezése

A prémium szintű ExpressRoute-bővítmény engedélyezheti a meglévő kapcsolat a következő parancsot:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

A kapcsolatcsoport most már engedélyezett ExpressRoute prémium bővítmény funkciók. Az első lépések számlázási, a prémium szintű bővítmény képességhez, amint sikeresen futtatta a parancsot.

### <a name="to-disable-the-expressroute-premium-add-on"></a>A prémium szintű ExpressRoute-bővítmény letiltása

> [!IMPORTANT]
> A művelet sikertelen lesz, amely nagyobb, mint mi a szabványos kör megengedett erőforrások használata.
> 
> 

Az ExpressRoute prémium bővítmény letiltása előtt ismertetése a következő feltételeknek:

* Mielőtt visszaminősítését prémiumról szabvány, győződjön meg arról, hogy rendelkezik-e kapcsolva a kapcsolatcsoport legfeljebb 10 virtuális hálózatok. Ha több mint 10, a frissítési kérelem sikertelen lesz, és a prémium ütemben számlázás meg.
* Minden virtuális hálózat más geopolitikai régiókban kell választható. Az összes virtuális hálózat nem választható, ha a frissítési kérelem sikertelen lesz, és a prémium ütemben számlázás meg.
* Az útvonaltábla a magánhálózati társviszony-létesítés kisebb, mint 4000 útvonalait kell lennie. Ha az útvonal tábla mérete nagyobb, mint 4000 útvonalakat, csökken a BGP-munkamenetet. A munkamenet nem kell újra, amíg hirdetett előtagok száma nem éri el 4000 engedélyezve.

Letilthatja a ExpressRoute prémium bővítmény a meglévő kapcsolat az alábbi példa szerint:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-kapcsolatcsoport sávszélessége frissítése

A támogatott sávszélesség-beállításait a szolgáltató, ellenőrizze a [ExpressRoute – gyakori kérdések](expressroute-faqs.md). Kiválaszthatja a mérete nagyobb, mint a meglévő expressroute méretét.

> [!IMPORTANT]
> Ha nincs elég kapacitás a meglévő porton, előfordulhat, hogy újra létre kell hozni az ExpressRoute-kapcsolatcsoportot. A kapcsolat nem frissíthető, ha nincsenek további kapacitást érhető el az adott helyhez.
>
> Nem csökkenti a sávszélesség az ExpressRoute-kapcsolatcsoportot megszakítása nélkül. Alacsonyabb verziójúra változtatása a sávszélesség szükséges, hogy az ExpressRoute-kapcsolatcsoport kiosztásának megszüntetése, és ezután ossza az új ExpressRoute-kapcsolatcsoportot.
>

Miután eldöntötte, hogy van szüksége, az alábbi parancs segítségével méretezze át a kapcsolatcsoport mérete:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

A kör mérete a Microsoft oldalon. Ezután lépjen kapcsolatba a kapcsolat szolgáltatójánál, ez a módosítás megfelelő a ügyféloldali konfigurációjának frissítése. Miután elvégezte ezt az értesítést, az első lépések számlázást, a frissített sávszélesség-beállítás.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>A Termékváltozat áthelyezése díjköteles korlátlanra

Az alábbi példa használatával módosíthatja az ExpressRoute-kapcsolatcsoport Termékváltozata:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és Resource Manager-környezetben való hozzáférés szabályozása

Tekintse át a utasításait [a Resource Manager üzembe helyezési modellben a klasszikus áthelyezése ExpressRoute-Kapcsolatcsoportok](expressroute-howto-move-arm.md).

## <a name="delete"></a>Megszüntetés és az ExpressRoute-kapcsolatcsoport törlése

Kiosztásának megszüntetése, és az ExpressRoute-kapcsolatcsoport törléséhez, tudja, hogy a következő feltételeknek:

* Az ExpressRoute-kapcsolatcsoport az összes virtuális hálózatot kell választható. Ha ez a művelet sikertelen, ellenőrizze, hogy ha a virtuális hálózatok a kapcsolatcsoport van csatolva.
* Ha az ExpressRoute-kapcsolatcsoport szolgáltatás szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépítve**, hogy kiosztásának megszüntetése a kapcsolatcsoport az oldalon a szolgáltató együttműködve. A Folytatás kiszámlázni Önnek, amíg a szolgáltató befejeződött, a kapcsolat megszüntetés, és értesíti, és az erőforrásokat.
* A kapcsolatcsoport törölheti, ha a szolgáltató a kapcsolatcsoport rendelkezik platformelőfizetés. Ha expressroute-kapcsolatcsoporthoz platformelőfizetés van, az üzembe helyezési állapota szolgáltató beállítása **nincs kiépítve**. Ezzel leállítja a számlázási a kör.

Az ExpressRoute-kapcsolatcsoport törlése a következő parancs futtatásával:

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a kapcsolatcsoport, győződjön meg arról, hogy a következő feladatokat hajthatnak végre:

* [Létrehozásához és módosításához az ExpressRoute-kapcsolatcsoport esetében routing](howto-routing-cli.md)
* [A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoportot](howto-linkvnet-cli.md)