---
title: 'Azure ExpressRoute: Társviszony-létesítés konfigurálása: klasszikus'
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 05602538f206032d924b39a7dd8f4325c48a5224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931378"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Peering létrehozása és módosítása ExpressRoute-kapcsolati kapcsolatban (klasszikus)
> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Videó - Privát társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó - Nyilvános társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft-társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

Ez a cikk bemutatja a powershell és a klasszikus üzembe helyezési modell használatával egy ExpressRoute-kapcsolati kapcsolatlétesítési/útválasztási konfiguráció létrehozásának és kezelésének lépéseit. Az alábbi lépések azt is bemutatják, hogyan ellenőrizheti az ExpressRoute-kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti és szüntetheti meg őket. Konfigurálhatja egy, két vagy mindhárom társviszony-létesítés (Azure private, Azure nyilvános és a Microsoft) egy ExpressRoute-kapcsolat. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. 

Ezek az utasítások csak a Layer 2 kapcsolódási szolgáltatásokat nyújtó szolgáltatókkal létrehozott áramkörökre vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt Layer 3 szolgáltatásokat (általában IPVPN-t, például MPLS-t) kínál, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az utasításokat [egy ExpressRoute-kapcsolat létrehozásához,](expressroute-howto-circuit-classic.md) és a kapcsolati kapcsolat szolgáltatója engedélyezve legyen a folytatás előtt. Az ExpressRoute-kapcsolatcsoportnak kiosztott és engedélyezett állapotban kell lennie, hogy az alább ismertetett parancsmagokat futtatni lehessen.

### <a name="download-the-latest-powershell-cmdlets"></a>A legújabb PowerShell-parancsmagok letöltése

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="azure-private-peering"></a>Azure privát társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. **Hozzon létre egy ExpressRoute-kapcsolatcsoportot.**

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze az ExpressRoute-áramkört, hogy nincs-e kiépítve.**
   
   Ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport ki van-e építve, és engedélyezve is van-e.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Eredményük a következő:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Győződjön meg arról, hogy az áramkör kiépített és engedélyezett jelenik meg. Ha nem, működjön együtt a kapcsolatszolgáltatóval, és a kapcsolatcsoport a szükséges állapotra és állapotra jusson.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára.**

   Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Ellenőrizze, hogy az áramköregyetlen más társviszony-létesítése sem használja-e ugyanazt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ellenőrizze, hogy nem használja-e a 65515-ös t.
   * Egy MD5-kivonat, ha használni kívánja. **Nem kötelező**.
     
   A következő példával konfigurálhatja az Azure privát társviszony-létesítését a kapcsolatcsoporthoz:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Ha MD5-kivonatot szeretne használni, az alábbi példában konfigurálhatja a privát társviszony-létesítést a kapcsolatcsoporthoz:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Ellenőrizze, hogy az AS-számot társviszony-létesítési ASN-ként adja-e meg, ne ügyfél ASN-ként.
   > 

### <a name="to-view-azure-private-peering-details"></a>Azure privát társviszony-létesítés részleteinek megtekintése

A konfigurációrészleteit a következő parancsmag segítségével tekintheti meg:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Eredményük a következő:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával. A következő példában az áramkör VLAN-azonosítója 100-ról 500-ra frissül.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Azure privát társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti. A parancsmag futtatása előtt győződjön meg arról, hogy az összes virtuális hálózat nincs összekapcsolva az ExpressRoute-kapcsolatról.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Azure nyilvános társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat.

> [!NOTE]
> Az Azure nyilvános társviszony-létesítéselavult az új áramkörök.
>

### <a name="to-create-azure-public-peering"></a>Azure nyilvános társviszony-létesítés létrehozása

1. **ExpressRoute-kapcsolat létrehozása**

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure nyilvános társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze az ExpressRoute-áramkört a kiépítés ellenőrzéséhez**

   Először ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport Kiosztott és Engedélyezett állapotban van-e.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Eredményük a következő:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Ellenőrizze, hogy az áramkör kiépített és engedélyezett ként jelenik-e meg. Ha nem, működjön együtt a kapcsolatszolgáltatóval, és a kapcsolatcsoport a szükséges állapotra és állapotra jusson.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Az Azure nyilvános társviszony-létesítésének konfigurálása a hálózathoz**
   
   A folytatás előtt győződjön meg arról, hogy rendelkezik a következő adatokkal:
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Ellenőrizze, hogy az áramköregyetlen más társviszony-létesítése sem használja-e ugyanazt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Egy MD5-kivonat, ha használni kívánja. **Nem kötelező**.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy az AS-számot társviszony-létesítési ASN-ként adja meg, és nem az ügyfél ASN-ként.
   >  
     
   A következő példával konfigurálhatja az Azure nyilvános társviszony-létesítését a kapcsolatcsoporthoz:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Ha MD5-kivonatot szeretne használni, az alábbi példával konfigurálja a csoportot:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Azure nyilvános társviszony-létesítés részleteinek megtekintése

A konfiguráció részleteinek megtekintéséhez használja a következő parancsmast:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Eredményük a következő:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával. Ebben a példában az áramkör VLAN-azonosítója 200-ról 600-ra frissül.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Ellenőrizze, hogy az áramkör kiépített és engedélyezett ként jelenik-e meg. 
### <a name="to-delete-azure-public-peering"></a>Azure nyilvános társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a következő parancsmag futtatásával távolíthatja el:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. **ExpressRoute-kapcsolat létrehozása**
  
   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze az ExpressRoute-áramkört a kiépítés ellenőrzéséhez**

   Ellenőrizze, hogy az áramkör kiépített és engedélyezett ként jelenik-e meg. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Eredményük a következő:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Ellenőrizze, hogy az áramkör kiépített és engedélyezett ként jelenik-e meg. Ha nem, működjön együtt a kapcsolatszolgáltatóval, és a kapcsolatcsoport a szükséges állapotra és állapotra jusson.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **A Microsoft társviszony-létesítésének konfigurálása a kapcsolatcsoporthoz**
   
    Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Ellenőrizze, hogy az áramköregyetlen más társviszony-létesítése sem használja-e ugyanazt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Vesszővel tagolt listát küldhet, ha előtagok készletét tervezi. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-számra vannak regisztrálva, megadhatja az AS-számot, amelyre regisztrálva vannak. **Nem kötelező**.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Egy MD5-kivonat, ha használni kívánja. **Választható.**
     
   Futtassa a következő parancsmast a Microsoft-társviszony-létesítés konfigurálásához a kapcsolatcsoporthoz:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Microsoft társviszony-létesítés részleteinek megtekintése

A konfigurációrészleteit a következő parancsmag segítségével tekintheti meg:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Eredményük a következő:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Microsoft társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármely részét a következő parancsmag segítségével frissítheti:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Microsoft társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a következő parancsmag futtatásával távolíthatja el:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>További lépések

Ezután [kapcsolja össze a virtuális hálózatot egy ExpressRoute-kapcsolatcsoportlal.](expressroute-howto-linkvnet-classic.md)

* A munkafolyamatokról további információt az [ExpressRoute-munkafolyamatok című témakörben talál.](expressroute-workflows.md)
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
