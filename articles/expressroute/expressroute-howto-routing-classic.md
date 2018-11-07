---
title: 'Útválasztás (társviszony-létesítés) az expressroute-kapcsolatcsoport konfigurálása: Azure: klasszikus |} A Microsoft Docs'
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc;ganesr
ms.openlocfilehash: 6e099d0cdf659aa6ed2ccbef1381021ae55c72c2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261842"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>ExpressRoute-kapcsolatcsoport (klasszikus) társviszony létesítése és módosítása
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Videó - privát társviszony](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó - nyilvános társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

Ez a cikk végigvezeti a lépéseken, hozhat létre és kezelhet egy ExpressRoute-kapcsolatcsoporttal a PowerShell és a klasszikus üzemi modell használatával útválasztási konfigurációja. Az alábbi lépések azt is bemutatják, hogyan ellenőrizheti az ExpressRoute-kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti és szüntetheti meg őket. Egy, kettő vagy az összes mindhárom társviszony-létesítést (Azure privát, Azure nyilvános és Microsoft) ExpressRoute-kapcsolatcsoport konfigurálhatja. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. 

Ezek az utasítások csak 2. rétegbeli kapcsolatot szolgáltatásokat kínáló szolgáltatóknál létrehozott Kapcsolatcsoportok vonatkoznak. Ha használja a szolgáltató által kínált felügyelt Layer 3 szolgáltatások (általában egy IPVPN, mint az MPLS), a kapcsolatszolgáltató konfigurálása és kezelése az útválasztást Ön helyett.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az utasításokat [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) , és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt a folytatáshoz. Az ExpressRoute-kapcsolatcsoportnak kiosztott és engedélyezett állapotban kell lennie, hogy az alább ismertetett parancsmagokat futtatni lehessen.

### <a name="download-the-latest-powershell-cmdlets"></a>Töltse le a legújabb PowerShell-parancsmagok

Telepítse az Azure Service Management (SM) PowerShell-modulok és az ExpressRoute-modul legújabb verzióit. Az alábbi példa használata esetén vegye figyelembe, hogy a verziószáma (a példában 5.1.1-es) változik, amint a parancsmagok újabb verziói jelennek meg.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

További információkért lásd: [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/overview) konfigurálása az Azure PowerShell-modulok használata a számítógép részletes útmutatást.

### <a name="sign-in"></a>Bejelentkezés

Jelentkezzen be az Azure-fiókjába, használja az alábbi példák:

1. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Keresse meg a fiókot az előfizetésekben.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

4. Ezután használja a következő parancsmagot az Azure-előfizetés hozzáadása a PowerShell a klasszikus üzemi modellhez.

  ```powershell
  Add-AzureAccount
  ```

## <a name="azure-private-peering"></a>Azure privát társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. **Hozzon létre egy ExpressRoute-kapcsolatcsoportot.**

  Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze, hogy ki van építve az ExpressRoute-kapcsolatcsoporthoz.**
   
  Ellenőrizze, hogy ha a az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett.

  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Adja vissza:

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
   
  Győződjön meg arról, hogy a kapcsolatcsoport kiosztott és engedélyezett jeleníti meg. Ha nem, beolvasni a szükséges állapot és az állapot a kapcsolatcsoport a kapcsolatszolgáltató együttműködve.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
3. **Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoporthoz.**

  Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:
   
  * Egy /30 alhálózat az elsődleges kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
  * Egy /30 alhálózat a másodlagos kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
  * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg arról, hogy nincs másik társviszony-létesítés a kapcsolatcsoport használja az ugyanazon VLAN-azonosítót.
  * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Győződjön meg arról, hogy 65515 nem használ.
  * Egy MD5-kivonat, ha használni kívánja. **Választható**.
     
  Az alábbi példa segítségével a konfigurálása az Azure privát társviszony-létesítést a kapcsolatcsoporthoz.:

  ```powershell
  New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
  ```    

  Egy MD5-kivonat használni kívánt, ha az alábbi példa használatával konfigurálja a privát társviszony-létesítést a kapcsolatcsoporthoz.:

  ```powershell
  New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
  ```
     
  > [!IMPORTANT]
  > Győződjön meg arról, hogy társviszony-létesítési ASN-t, ne ügyfél ASN-t, adja meg az AS-számot.
  > 

### <a name="to-view-azure-private-peering-details"></a>Azure privát társviszony-létesítés részleteinek megtekintése

Konfiguráció részleteit az alábbi parancsmag használatával tekintheti meg:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Adja vissza:

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

A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával. A következő példában a kör VLAN-azonosító frissítése folyamatban van a 100 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Azure privát társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti. Győződjön meg arról, hogy minden virtuális hálózat, az ExpressRoute-kapcsolatcsoport leválasztása a parancsmag futtatása előtt.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Azure nyilvános társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat.

### <a name="to-create-azure-public-peering"></a>Azure nyilvános társviszony-létesítés létrehozása

1. **ExpressRoute-kapcsolatcsoport létrehozása**

  Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure nyilvános társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze a ExpressRoute-kapcsolatcsoport győződjön meg arról, hogy annak kiépítése**

  Először ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport Kiosztott és Engedélyezett állapotban van-e.

  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Adja vissza:

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
   
  Győződjön meg arról, hogy a kapcsolatcsoport kiosztott és engedélyezett jeleníti meg. Ha nem, beolvasni a szükséges állapot és az állapot a kapcsolatcsoport a kapcsolatszolgáltató együttműködve.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
4. **Az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz konfigurálása**
   
  Győződjön meg arról, hogy rendelkezik a következő adatokat, mielőtt továbblépne:
   
  * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
  * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
  * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg arról, hogy nincs másik társviszony-létesítés a kapcsolatcsoport használja az ugyanazon VLAN-azonosítót.
  * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
  * Egy MD5-kivonat, ha használni kívánja. **Választható**.

  > [!IMPORTANT]
  > Győződjön meg arról, hogy társviszony-létesítési ASN-t, és ne ügyfél ASN-t, adja meg az AS-számot.
  >  
     
  Az alábbi példa segítségével a konfigurálása az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz.:

  ```powershell
  New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
  ```
     
  Ha szeretné használni egy MD5-kivonat, használja a következő példát a kapcsolatcsoport konfigurálása:
     
  ```powershell
  New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
  ```
     
### <a name="to-view-azure-public-peering-details"></a>Azure nyilvános társviszony-létesítés részleteinek megtekintése

Konfigurációs részletek megtekintéséhez használja a következő parancsmagot:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Adja vissza:

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

A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával. Ebben a példában a kör VLAN-azonosító frissítése folyamatban van 200-ról 600-ra.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Győződjön meg arról, hogy a kapcsolatcsoport kiosztott és engedélyezett jeleníti meg. 
### <a name="to-delete-azure-public-peering"></a>Azure nyilvános társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a következő parancsmag futtatásával távolíthatja el:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. **ExpressRoute-kapcsolatcsoport létrehozása**
  
  Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze a ExpressRoute-kapcsolatcsoport győződjön meg arról, hogy annak kiépítése**

  Győződjön meg arról, hogy a kapcsolatcsoport kiosztott és engedélyezett jeleníti meg. 
   
  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Adja vissza:
   
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
   
  Győződjön meg arról, hogy a kapcsolatcsoport kiosztott és engedélyezett jeleníti meg. Ha nem, beolvasni a szükséges állapot és az állapot a kapcsolatcsoport a kapcsolatszolgáltató együttműködve.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
3. **Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz**
   
    Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg arról, hogy nincs másik társviszony-létesítés a kapcsolatcsoport használja az ugyanazon VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha azt tervezi, hogy az előtagok megadni küldhet egy vesszővel tagolt lista. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-számra vannak regisztrálva, megadhatja az AS-számot, amelyre regisztrálva vannak. **Választható**.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Egy MD5-kivonat, ha használni kívánja. **Nem kötelező.**
     
  A következő parancsmag futtatásával konfigurálhatja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz:
 
  ```powershell
  New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
  ```

### <a name="to-view-microsoft-peering-details"></a>Microsoft társviszony-létesítés részleteinek megtekintése

Konfiguráció részleteit az alábbi parancsmag használatával tekintheti meg:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Adja vissza:

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

Az alábbi parancsmag használatával a konfiguráció bármelyik részét frissítheti:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Microsoft társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a következő parancsmag futtatásával távolíthatja el:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>További lépések

Ezután [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-classic.md).

* Munkafolyamatokkal kapcsolatos további információkért lásd: [az ExpressRoute-munkafolyamatokat](expressroute-workflows.md).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).