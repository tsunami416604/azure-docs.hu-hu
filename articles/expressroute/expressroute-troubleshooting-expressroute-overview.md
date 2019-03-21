---
title: 'Ellenőrizze a kapcsolatot – ExpressRoute-hibaelhárítási útmutató: Azure| Microsoft Docs'
description: Ezen a lapon útmutatás hibaelhárítás és a egy ExpressRoute-kapcsolatcsoport a teljes körű kapcsolódás ellenőrzése.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 888f4dedf2fda0f54297d42a5f813abf73ded748
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121519"
---
# <a name="verifying-expressroute-connectivity"></a>Az ExpressRoute-kapcsolat ellenőrzése
Ez a cikk segítséget nyújt a ellenőrizze-e, és az ExpressRoute-kapcsolat hibaelhárítása. Az ExpressRoute, amely kiterjeszti a helyszíni hálózatot a Microsoft-felhőbe, hogy a kapcsolatszolgáltató megkönnyíthető privát kapcsolaton keresztül, a következő három különböző hálózati zónák foglalja magában:

-   Ügyfélhálózat
-   Hálózati szolgáltató
-   Microsoft Datacenter

Ez a dokumentum célja, hogy segítséget nyújtson a felhasználót, hogy határozza meg, hol (vagy még akkor is, ha) kapcsolódási probléma létezik, és melyik zónába, és ezáltal megfelelő csapat a probléma megoldásához segítségért belül. Ha a Microsoft támogatási van szüksége a probléma megoldásához szükséges, nyisson meg egy támogatási jegyet a [Support][Support].

> [!IMPORTANT]
> Ez a dokumentum célja felderítésére és egyszerű problémák elhárítására. Nem célja a helyettesítheti a Microsoft ügyfélszolgálatához. Nyisson meg egy támogatási jegyet a [Support] [ Support] Ha nem tudja megoldani a problémát, útmutatása.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
Az alábbi ábrán a logikai egy ExpressRoute-tal a Microsoft hálózati ügyfél hálózati kapcsolatát.
[![1]][1]

A fenti ábrán a számok jelölik a főbb hálózati pontokat. A hálózati pontokat gyakran ez a cikk keresztül által hivatkozott a hozzájuk társított számokat.

Az ExpressRoute-kapcsolat modell (Cloud Exchange közös elhelyezés, pontok közötti Ethernet-kapcsolat vagy -bármely (IPVPN-)) a hálózati pontokat, 3. és 4 kapcsolók (2. rétegbeli eszközök) is lehet. A főbb hálózati pontokat szemléltetett a következők:

1.  Ügyfél számítási eszköz (például egy kiszolgáló vagy számítógép)
2.  CEs: Ügyfél peremhálózati útválasztók 
3.  PEs (CE kapcsolódó): Szolgáltató peremhálózati útválasztók/kapcsolók, amelyek az ügyfél peremhálózati útválasztók fennálló. Néven PE-CEs ebben a dokumentumban.
4.  PEs (MSEE kapcsolódó): Szolgáltató peremhálózati útválasztók/kapcsolók, amelyek fennálló Msee. Néven PE-Msee ebben a dokumentumban.
5.  Msee: A Microsoft vállalati peremhálózati (MSEE) az ExpressRoute-útválasztók
6.  Virtuális hálózat (VNet) átjáró
7.  Az Azure-beli virtuális eszközön COMPUTE

A Cloud Exchange közös elhelyezés vagy a pontok közötti Ethernet-kapcsolat kapcsolati modellek használata esetén az ügyfél peremhálózati útválasztója (2) a BGP társviszony-létesítés (5) Msee-hozzák létre. 3. és 4 hálózati pontokat szeretne továbbra is létezik, de lehet némileg átlátszó, 2. rétegbeli eszközök.

A-bármely (IPVPN) kapcsolat modellt használja, ha a PEs (MSEE kapcsolódó) (4) szeretné létrehozni a BGP-társviszonyt az Msee (5). Útvonalak majd térjen vissza az ügyfél hálózati az IPVPN szolgáltatás szolgáltató hálózaton keresztül továbbítja.

> [!NOTE]
>ExpressRoute magas rendelkezésre állás érdekében a Microsoft a redundáns BGP-munkamenetek között msee-k (5) és (4) PE-Msee-pár igényel. Hálózati elérési utak redundáns párjai is javasolt ügyfél hálózatán és PE-CEs között. Azonban – bármely (IPVPN) kapcsolat modellben egyetlen CE eszköz (2) csatlakozhat egy vagy több PEs (3).
>
>

ExpressRoute-kapcsolatcsoport ellenőrzéséhez a következő lépéseket (a a hálózati pont, a hozzájuk társított számokat által jelzett) terjed ki:
1. [Ellenőrizze a Kapcsolatcsoportok kiépítésével és állapotát (5)](#validate-circuit-provisioning-and-state)
2. [Ellenőrizze a legalább egy ExpressRoute társviszony-létesítés konfigurálva (5)](#validate-peering-configuration)
3. [Ellenőrizze a Microsoft és a szolgáltatás közötti ARP szolgáltató (4. és 5 közötti kapcsolat)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Ellenőrizze a BGP és az MSEE-(BGP-4 – 5 és 5 és 6 Ha csatlakoztatva van a virtuális hálózat között) az útvonalak](#validate-bgp-and-routes-on-the-msee)
5. [Ellenőrizze a forgalom statisztika (5 áthaladó forgalom)](#check-the-traffic-statistics)

További ellenőrzések és ellenőrzések megjelenik a jövőbeli, ellenőrzés havonta!

## <a name="validate-circuit-provisioning-and-state"></a>Ellenőrizze a Kapcsolatcsoportok kiépítésével és állapota
Függetlenül a kapcsolat modell egy ExpressRoute-kapcsolatcsoporttal rendelkezik hozhatók létre, és az így létrehozott Szolgáltatáskulcs kapcsolatcsoport-kiépítési. Az ExpressRoute-Kapcsolatcsoportok kiépítési létesít egy redundáns 2. rétegbeli kapcsolatokat PE-Msee (4) és Msee (5) között. Létrehozására, üzembe helyezése, és ellenőrizze a ExpressRoute-kapcsolatcsoport módjáról további információkért tekintse meg a cikket [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport][CreateCircuit].

>[!TIP]
>Szolgáltatáskulcs egyedileg azonosítja az ExpressRoute-kapcsolatcsoport. Ez a kulcs megadása kötelező a ebben a dokumentumban említett powershell-parancsok legtöbbje esetén. Ezenkívül kell segítségre van szüksége a Microsoft vagy a ExpressRoute-partnerré ExpressRoute a problémákat, adja meg a kulcs könnyen azonosíthatja a kapcsolatcsoportot.
>
>

### <a name="verification-via-the-azure-portal"></a>Az Azure Portalon keresztül ellenőrzése
Az Azure Portalon, az ExpressRoute-kapcsolatcsoport állapota ellenőrizhető kiválasztásával ![2][2] a bal oldali oldalsó sáv menüben, és kiválasztja az ExpressRoute-kapcsolatcsoportot. Kiválasztása az ExpressRoute kapcsolatcsoport "Minden erőforrás" részen az ExpressRoute-kapcsolatcsoport panel megnyílik. Az a ![3][3] részében, az ExpressRoute essentials láthatók, az alábbi képernyőképen látható módon:

![4][4]    

Az ExpressRoute essentialsben *kapcsolatcsoport állapota* Microsoft oldalán a kapcsolatcsoport állapotát jelzi. *Szolgáltató állapota* azt jelzi, ha a kapcsolatcsoport *kiépített/nem kiépített* -szolgáltató oldalán. 

ExpressRoute-kapcsolatcsoport működik, hogy a *kapcsolatcsoport állapota* kell *engedélyezve* és a *szolgáltató állapota* kell lennie *kiépített*.

> [!NOTE]
> Ha a *kapcsolatcsoport állapota* van nincs engedélyezve, lépjen kapcsolatba [Support][Support]. Ha a *szolgáltató állapota* van nincs kiépítve, forduljon a szolgáltatójához.
>
>

### <a name="verification-via-powershell"></a>Ellenőrzési PowerShell-lel
Egy erőforráscsoport ExpressRoute-Kapcsolatcsoportok listájában, használja a következő parancsot:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Az Azure-on keresztül kérheti le az erőforráscsoport nevét. Tekintse meg az előző ebben a dokumentumban, és vegye figyelembe, hogy az erőforráscsoport neve szerepel-e a példaként szolgáló képernyőképen.
>
>

Egy adott ExpressRoute-kapcsolatcsoport ki egy erőforráscsoportot, használja a következő parancsot:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Van egy mintaválasz:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Annak ellenőrzéséhez, ha az ExpressRoute-kapcsolatcsoport működik, különös figyelmet fordítani a következő mezőket:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Ha a *CircuitProvisioningState* van nincs engedélyezve, lépjen kapcsolatba [Support][Support]. Ha a *ServiceProviderProvisioningState* van nincs kiépítve, forduljon a szolgáltatójához.
>
>

### <a name="verification-via-powershell-classic"></a>Ellenőrzési PowerShell-lel (klasszikus)
Egy előfizetéshez tartozó ExpressRoute-Kapcsolatcsoportok listájában, használja a következő parancsot:

    Get-AzureDedicatedCircuit

Egy adott ExpressRoute-kapcsolatcsoport kijelöléséhez használja a következő parancsot:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Van egy mintaválasz:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Annak ellenőrzéséhez, ha az ExpressRoute-kapcsolatcsoport működik, különös figyelmet fordítani a következő mezőket: ServiceProviderProvisioningState : Üzembe helyezett állapot: Engedélyezve

> [!NOTE]
> Ha a *állapot* van nincs engedélyezve, lépjen kapcsolatba [Support][Support]. Ha a *ServiceProviderProvisioningState* van nincs kiépítve, forduljon a szolgáltatójához.
>
>

## <a name="validate-peering-configuration"></a>Társviszony-létesítési konfigurációjának ellenőrzése
A szolgáltatót az ExpressRoute-kapcsolatcsoport kiépítési befejezését követően egy útválasztási konfigurációja MSEE-lekéréses kérelmek (4) és Msee (5) között ExpressRoute-kapcsolatcsoporton keresztül hozható létre. Egyes ExpressRoute-kapcsolatcsoport engedélyezve van egy, kettő vagy három útválasztási környezetek veheti fel: Az Azure privát társviszony-létesítés (forgalom az Azure-beli virtuális magánhálózatok), az Azure nyilvános társviszony-létesítés (forgalom nyilvános IP-címek az Azure-ban) és Microsoft társviszony-létesítés (forgalom Office 365 és Dynamics 365). Útválasztási konfiguráció létrehozása és módosítása módjáról további információkért tekintse meg a cikket [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport útválasztásának][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Az Azure Portalon keresztül ellenőrzése

> [!NOTE]
> Ha 3. rétegbeli a szolgáltató által biztosított, és a társviszony-létesítések üresek a portálon, frissítse a kapcsolatcsoport konfigurációját, a frissítés gomb segítségével a portálon. Ez a művelet a kapcsolatcsoport alkalmazzák a megfelelő útválasztási konfigurációja. 
>
>

Az Azure Portalon, az ExpressRoute-kapcsolatcsoport állapota kiválasztásával ellenőrizhetők ![2][2] a bal oldali oldalsó sáv menüben, és kiválasztja az ExpressRoute-kapcsolatcsoportot. Kiválasztása az ExpressRoute kapcsolatcsoport "Minden erőforrás" alatt felsorolt nyitna az ExpressRoute-kapcsolatcsoport panelen. Az a ![3][3] részében, az ExpressRoute essentials kellene szerepelnie, az alábbi képernyőképen látható módon:

![5][5]

Az előző példában feljegyzett Azure privát társviszony-létesítési útválasztási környezet engedélyezve van, mivel az Azure nyilvános és Microsoft társviszony-létesítési útválasztási környezetek nem engedélyezett. Társviszony-létesítési környezet sikeresen engedélyezve a felsorolt (BGP szükséges) az elsődleges és másodlagos point-to-point alhálózatokat is rendelkezik. A/30 alhálózatot az adapter IP-címe az Msee és PE-Msee szolgálnak. 

> [!NOTE]
> A társviszony nincs engedélyezve, ha ellenőrizze, ha a hozzárendelt elsődleges és másodlagos alhálózat megfelel-e PE-Msee a konfigurációt. Ha nem, akkor módosítsa a MSEE útválasztók, tekintse meg [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport útválasztásának][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Ellenőrzési PowerShell-lel
Az Azure privát társviszony-létesítési konfiguráció részletei lekéréséhez használja a következő parancsokat:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Egy mintaválasz egy konfigurálása sikerült privát társviszony-létesítéshez, a következő:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Társviszony-létesítési környezet sikeresen engedélyezve a felsorolt elsődleges és másodlagos címelőtagokat kellene. A/30 alhálózatot az adapter IP-címe az Msee és PE-Msee szolgálnak.

Az Azure nyilvános társviszony-létesítési konfiguráció részletei lekéréséhez használja a következő parancsokat:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

A Microsoft társviszony-létesítési konfiguráció részletei lekéréséhez használja a következő parancsokat:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Ha a társviszony nincs konfigurálva, egy hibaüzenet lenne. Mintaválasz, ha a megadott társviszony-létesítés (Azure-beli nyilvános társviszony-létesítés ebben a példában) nincs konfigurálva a kapcsolatcsoport belül:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Ha a társviszony-létesítés nem engedélyezett, ellenőrizze a Ha a hozzárendelt elsődleges és másodlagos alhálózat megfelel-e a konfigurációját, a társított PE MSEE. Is ellenőrizheti, ha a megfelelő *VlanId*, *AzureASN*, és *PeerASN* Msee használ, és ha ezek az értékek képez le a csatolt PE MSEE használják azokat. Ha MD5 kivonatoló választja, a megosztott kulcs azonos az MSEE-PE-MSEE párt kell lennie. Módosítsa a MSEE-útválasztó konfigurációját, tekintse meg [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport útválasztásának][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Ellenőrzési PowerShell-lel (klasszikus)
Az Azure privát társviszony-létesítési konfiguráció részletei lekéréséhez használja a következő parancsot:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

A következő mintát választ, egy konfigurálása sikerült privát társviszony-létesítéshez:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Sikeresen engedélyezve A társviszony-létesítési környezetben kell az elsődleges és másodlagos társ alhálózatok szerepel. A/30 alhálózatot az adapter IP-címe az Msee és PE-Msee szolgálnak.

Az Azure nyilvános társviszony-létesítési konfiguráció részletei lekéréséhez használja a következő parancsokat:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

A Microsoft társviszony-létesítési konfiguráció részletei lekéréséhez használja a következő parancsokat:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> 3. rétegbeli társviszonyok a szolgáltató által beállított, ha a portálon vagy a PowerShell segítségével az ExpressRoute-társviszony beállítása felülírja a szolgáltatás-szolgáltató beállításait. A szolgáltató oldalán társviszony-létesítési beállítások alaphelyzetbe állítását igényli a támogatása a service provider. Az ExpressRoute-társviszony csak akkor módosítsa, ha biztos, hogy a szolgáltató biztosítja a csak a 2. rétegbeli szolgáltatásokat!
>
>

> [!NOTE]
> A társviszony nincs engedélyezve, ha ellenőrizze, ha a hozzárendelt elsődleges és másodlagos társ alhálózatait megfelel-e a konfigurációját, a társított PE MSEE. Is ellenőrizheti, ha a megfelelő *VlanId*, *AzureAsn*, és *PeerAsn* Msee használ, és ha ezek az értékek képez le a csatolt PE MSEE használják azokat. Módosítsa a MSEE-útválasztó konfigurációját, tekintse meg [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport útválasztásának][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>A Microsoft és a szolgáltató közötti ARP ellenőrzése
Ez a szakasz a (klasszikus) PowerShell-parancsokat használja. Ha már használja az Azure-Resource Manager PowerShell-parancsokat, győződjön meg arról, hogy a rendszergazda/társadminisztrátor hozzáférést az előfizetéshez. Hibaelhárítás az Azure Resource Manager használatával a parancsok tekintse meg a [első ARP-táblák a Resource Manager-alapú üzemi modellben] [ ARP] dokumentumot.

> [!NOTE]
>ARP lekéréséhez az Azure portal és az Azure Resource Manager PowerShell-parancsokkal is használható. Ha hiba történik, az Azure Resource Manager PowerShell-parancsokkal, klasszikus PowerShell-parancsok is használhatók az Azure Resource Manager az ExpressRoute-Kapcsolatcsoportok klasszikus PowerShell-parancsokkal használható.
>
>

A privát társviszony-létesítés kérhet le az elsődleges MSEE-útválasztó ARP-táblázat, használja a következő parancsot:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

A parancs sikeres forgatókönyvben példaválasz:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Ehhez hasonlóan az MSEE az ARP táblázat ellenőrizheti a *elsődleges*/*másodlagos* elérési utat, a *privát*/*nyilvános*  / *Microsoft* társviszony-létesítéseket.

Az alábbi példa bemutatja, hogy a válasz egy társviszony-létesítéshez a parancs nem létezik.

    ARP Info:
       
> [!NOTE]
> Ha ARP-táblázat nem rendelkezik a MAC-címek hozzárendelve felületek IP-címeket, tekintse át a következő információkat:
>1. Ha az első IP-cím / 30 alhálózat hozzárendelt MSEE-közösségbeli adapterén használja az MSEE-PR és MSEE közötti kapcsolat Azure mindig Msee a második IP-címet használ.
>2. Győződjön meg arról, ha az ügyfél (C-címke) és a VLAN címkéket (S-címke) szolgáltatás megfelelő mindkét MSEE-PR-MSEE párt.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Ellenőrizze a BGP és az MSEE útvonalak
Ez a szakasz a (klasszikus) PowerShell-parancsokat használja. Ha már használja az Azure-Resource Manager PowerShell-parancsokat, győződjön meg arról, hogy a rendszergazda/társadminisztrátor hozzáférést az előfizetéshez.

> [!NOTE]
>A BGP lekérése az Azure portal és az Azure Resource Manager PowerShell-parancsokkal is használható. Ha hiba történik, az Azure Resource Manager PowerShell-parancsokkal, klasszikus PowerShell-parancsok is használhatók az Azure Resource Manager az ExpressRoute-Kapcsolatcsoportok klasszikus PowerShell-parancsokkal használható.
>
>

Az útvonaltábla (BGP-szomszéd) összefoglalójának lekérése egy adott útválasztási környezet, használja a következő parancsot:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

A következő példa választ:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Ahogy az az előző példában is látható, a parancs hasznos határozza meg, mennyi az útválasztási környezet létrehozását követően. Azt is jelzi, a társviszony-létesítési útválasztó által hirdetett útvonal előtagok száma.

> [!NOTE]
> Ha az állapot az aktív vagy inaktív, ellenőrizze, ha a hozzárendelt elsődleges és másodlagos társ alhálózatait megfelel-e a konfigurációját, a társított PE MSEE. Is ellenőrizheti, ha a megfelelő *VlanId*, *AzureAsn*, és *PeerAsn* Msee használ, és ha ezek az értékek képez le a csatolt PE MSEE használják azokat. Ha MD5 kivonatoló választja, a megosztott kulcs azonos az MSEE-PE-MSEE párt kell lennie. Módosítsa a MSEE-útválasztó konfigurációját, tekintse meg [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport útválasztásának][CreatePeering].
>
>

> [!NOTE]
> Ha bizonyos célok nem érhetők el egy adott társviszony-létesítésen keresztül, ellenőrizze az útvonaltáblát az msee-k az adott társviszony-létesítési környezethez tartozó. Ha egy megfelelő előtagot (Ez lehet gelt IP) megtalálható az útválasztási táblázatban, majd ellenőrizze Ha tűzfalak és NSG-t és hozzáférés-vezérlési listák az elérési úton, és ha azok lehetővé teszik, hogy a forgalom.
>
>

A teljes útválasztási táblázat MSEE beszerezni a *elsődleges* elérési útját, az adott *privát* útválasztási környezet, használja a következő parancsot:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Egy példa a sikeres a parancs eredménye:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Ehhez hasonlóan az útválasztási táblázatban az MSEE a származó ellenőrizheti a *elsődleges*/*másodlagos* elérési utat, a *privát* /  *Nyilvános*/*Microsoft* egy társviszony-létesítési környezetet.

Az alábbi példa bemutatja, hogy a válasz egy társviszony-létesítéshez a parancs nem létezik:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>A forgalom statisztikai adatainak ellenőrzése
A kombinált elsődleges és másodlagos elérési út forgalom statisztikájának beolvasása – bájt be és ki – társviszony-létesítési környezet, használja a következő parancsot:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

A minta a parancs kimenete:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

A minta egy nem létező társviszony-létesítéshez a parancs kimenete:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>További lépések
További információ vagy a Súgó tekintse meg az alábbi hivatkozásokat:

- [A Microsoft ügyfélszolgálatához][Support]
- [Létrehozása és módosítása egy ExpressRoute-kapcsolatcsoporttal][CreateCircuit]
- [Útválasztás az ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logikai Express Route-kapcsolat"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Az összes erőforrásaihoz ikon"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Áttekintés ikon"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Az ExpressRoute Essentials minta képernyőképe"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Az ExpressRoute Essentials minta képernyőképe"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






