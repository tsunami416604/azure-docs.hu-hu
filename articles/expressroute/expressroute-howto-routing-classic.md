---
title: 'Útválasztás (társviszony-létesítés) az expressroute-kapcsolatcsoport konfigurálása: Azure: klasszikus |} A Microsoft Docs'
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 9cebb196bd91da704798fb001763a76e6d090472
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "31594137"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>ExpressRoute-kapcsolatcsoport (klasszikus) társviszony létesítése és módosítása
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Videó - privát társviszony](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó - nyilvános társviszony-létesítés](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft társviszony-létesítés](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

Ez a cikk végigvezeti a lépéseken, hozhat létre és kezelhet egy ExpressRoute-kapcsolatcsoporttal a PowerShell és a klasszikus üzemi modell használatával útválasztási konfigurációja. Az alábbi lépések azt is bemutatják, hogyan ellenőrizheti az ExpressRoute-kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti és szüntetheti meg őket.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek
* Az Azure Service Management (SM) PowerShell-parancsmagjainak legújabb verzióját kell. További információkért lásd: [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/overview).  
* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az utasításokat [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) , és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt a folytatáshoz. Az ExpressRoute-kapcsolatcsoportnak kiosztott és engedélyezett állapotban kell lennie, hogy az alább ismertetett parancsmagokat futtatni lehessen.

> [!IMPORTANT]
> Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha 3. rétegbeli szolgáltatásokat kínáló szolgáltatóval rendelkezik (tipikusan IPVPN, mint az MPLS), a kapcsolatszolgáltató konfigurálja és felügyeli az útválasztást Ön helyett.
> 
> 

Egy, két vagy akár mindhárom társviszony-létesítést (Azure privát, Azure nyilvános és Microsoft) is konfigurálhatja egy adott ExpressRoute-kapcsolatcsoportban. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Jelentkezzen be az Azure-fiókjával, és válasszon ki egy előfizetést
1. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

        Connect-AzureRmAccount

2. Keresse meg a fiókot az előfizetésekben.

        Get-AzureRmSubscription

3. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. Ezután használja a következő parancsmagot az Azure-előfizetés hozzáadása a PowerShell a klasszikus üzemi modellhez.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Azure privát társviszony-létesítés
Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása
1. **A PowerShell-modul importálása az expressroute-hoz.**
   
    Importálnia kell az Azure és az ExpressRoute-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. Futtassa az alábbi parancsokat a PowerShell-munkamenetbe az Azure és az ExpressRoute modulok importálásához. A verzió eltérőek lehetnek.    
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Hozzon létre egy ExpressRoute-kapcsolatcsoportot.**
   
    Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat. 
3. **Ellenőrizze a ExpressRoute-kapcsolatcsoport győződjön meg arról, hogy ki van építve.**
   
    Először ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport Kiosztott és Engedélyezett állapotban van-e. Lásd az alábbi példát.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Győződjön meg arról, hogy a kapcsolatcsoport kiosztott és engedélyezett jeleníti meg. Ha nem, beolvasni a szükséges állapot és az állapot a kapcsolatcsoport a kapcsolatszolgáltató együttműködve.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoporthoz.**
   
    Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
   * Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező**.
     
    A következő parancsmag futtatásával konfigurálhatja az Azure privát társviszony-létesítést a kapcsolatcsoporthoz.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
     
    Használhatja az alábbi parancsmagot, ha MD5-kivonatot kíván használni.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Azure privát társviszony-létesítés részleteinek megtekintése
A konfiguráció részleteit az alábbi parancsmaggal kérheti le.

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

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


### <a name="to-update-azure-private-peering-configuration"></a>Azure privát társviszony-létesítés konfigurációjának frissítése
A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával. Az alábbi példában a kör VLAN azonosítóját 100-ról 500-ra frissítjük.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Azure privát társviszony-létesítés törlése
A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti.

> [!WARNING]
> A parancsmag futtatása előtt győződjön meg róla, hogy az összes virtuális hálózat le van választva az ExpressRoute-kapcsolatcsoportról. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Azure nyilvános társviszony-létesítés
Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat.

### <a name="to-create-azure-public-peering"></a>Azure nyilvános társviszony-létesítés létrehozása
1. **A PowerShell-modul importálása az expressroute-hoz.**
   
    Importálnia kell az Azure és az ExpressRoute-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. Futtassa az alábbi parancsokat a PowerShell-munkamenetbe az Azure és az ExpressRoute modulok importálásához. A verzió eltérőek lehetnek.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **ExpressRoute-kapcsolatcsoport létrehozása**
   
    Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure nyilvános társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
3. **Ellenőrizze az ExpressRoute kapcsolatcsoport ki van építve**
   
    Először ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport Kiosztott és Engedélyezett állapotban van-e. Lásd az alábbi példát.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Győződjön meg arról, hogy a kapcsolatcsoport kiosztott és engedélyezett jeleníti meg. Ha nem, beolvasni a szükséges állapot és az állapot a kapcsolatcsoport a kapcsolatszolgáltató együttműködve.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz konfigurálása**
   
    Mielőtt folytatná, ellenőrizze az alábbi információk meglétét:
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező**.
     
    A következő parancsmag futtatásával konfigurálhatja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz.
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
     
    Használhatja az alábbi parancsmagot, ha MD5-kivonatot kíván használni.
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Azure nyilvános társviszony-létesítés részleteinek megtekintése
A konfiguráció részleteit az alábbi parancsmaggal kérheti le.

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

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


### <a name="to-update-azure-public-peering-configuration"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése
A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával.

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

A fenti példában a kör VLAN-azonosítóját 200-ról 600-ra frissítjük.

### <a name="to-delete-azure-public-peering"></a>Azure nyilvános társviszony-létesítés törlése
A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti.

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Microsoft társviszony-létesítés
Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása
1. **A PowerShell-modul importálása az expressroute-hoz.**
   
    Importálnia kell az Azure és az ExpressRoute-modulokat a PowerShell-munkamenetbe az ExpressRoute-parancsmagok használatának elkezdéséhez. Futtassa az alábbi parancsokat a PowerShell-munkamenetbe az Azure és az ExpressRoute modulok importálásához. A verzió eltérőek lehetnek.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **ExpressRoute-kapcsolatcsoport létrehozása**
   
    Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
3. **Ellenőrizze az ExpressRoute kapcsolatcsoport ki van építve**
   
    Először ellenőrizze, hogy van-e az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Győződjön meg arról, hogy a kapcsolatcsoport kiosztott és engedélyezett jeleníti meg. Ha nem, beolvasni a szükséges állapot és az állapot a kapcsolatcsoport a kapcsolatszolgáltató együttműködve.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz**
   
    Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha több előtagot kíván megadni, vesszővel tagolt listában adhatja meg őket. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-számra vannak regisztrálva, megadhatja az AS-számot, amelyre regisztrálva vannak. **Ez nem kötelező**.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező.**
     
    A kapcsolatcsoport Microsoft pering konfigurálásához a következő parancsmag futtatása
     
        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-view-microsoft-peering-details"></a>Microsoft társviszony-létesítés részleteinek megtekintése
A konfiguráció részleteit az alábbi parancsmaggal kérheti le.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

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


### <a name="to-update-microsoft-peering-configuration"></a>Microsoft társviszony-létesítés konfigurációjának frissítése
A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Microsoft társviszony-létesítés törlése
A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>További lépések
Ezután [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-classic.md).

* Munkafolyamatokkal kapcsolatos további információkért lásd: [az ExpressRoute-munkafolyamatokat](expressroute-workflows.md).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).

