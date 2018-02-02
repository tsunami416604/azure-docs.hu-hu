---
title: "A kapcsolat ellenőrzése: Azure ExpressRoute-hibaelhárítási útmutatója |} Microsoft Docs"
description: "Ezen a lapon útmutatás hibaelhárítási és az ExpressRoute-kapcsolatcsoportot végpontok közötti kapcsolat ellenőrzése."
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 74b6589a7e06570d978dfe40c5f5bf140e092cc6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="verifying-expressroute-connectivity"></a>Az ExpressRoute-kapcsolat ellenőrzése
ExpressRoute, egy a helyszíni hálózat kibővítve a Microsoft felhőbe, amely a kapcsolat szolgáltatójánál megkönnyíthető titkos kapcsolaton keresztül, a következő három különböző hálózati zónák foglal magában:

-   Ügyfél hálózati
-   Szolgáltató
-   Microsoft Datacenter

A jelen dokumentum célja, hogy segítségével a felhasználó határozza meg, hol (vagy ha a) a kapcsolódási problémát létezik, és melyik zónába, és ezáltal a probléma megoldásához megfelelő csapat segítségért belül. Ha egy probléma megoldásához szükség van a Microsoft támogatási szolgálatához, nyissa meg a támogatási jegy [Microsoft Support][Support].

> [!IMPORTANT]
> Ez a dokumentum célja, diagnosztizálása és egyszerű problémák kijavítása érdekében. Nem célja a helyettesítheti a Microsoft támogatási szolgálatához. A támogatási jegy megnyitása [Microsoft Support] [ Support] Ha nem tudja megoldani a problémát az útmutató segítségével.
>
>

## <a name="overview"></a>Áttekintés
Az alábbi ábrán a logikai ExpressRoute segítségével a Microsoft hálózatához a felhasználói hálózati kapcsolatát.
[![1]][1]

A fenti ábrán a számok kulcs hálózati pontok jelzi. A hálózati pontokat gyakran ez a cikk keresztül által hivatkozott a hozzájuk társított számokat.

Attól függően, hogy a ExpressRoute-kapcsolat modell (felhőalapú Exchange történő együttes elhelyezés, Point-to-Point protokoll Ethernet-kapcsolat vagy bármely elem közöttiként (IPVPN)) a hálózati 3. és 4 is lehetnek kapcsolók (2. rétegbeli eszközök). A főbb hálózati pontokat mutatja a következők:

1.  Ügyfél számítási eszközt (például kiszolgáló vagy számítógép)
2.  Tanúsítványigénylési webszolgáltatás: Ügyfél peremhálózati útválasztók 
3.  PEs (CE hozzáférhető): szolgáltató peremhálózati útválasztók/kapcsolók, amelyek az ügyfél peremhálózati útválasztók használata során szembesülnek. Néven PE-CEs ebben a dokumentumban.
4.  PEs (MSEE hozzáférhető): szolgáltató peremhálózati útválasztók/kapcsolók használata során szembesülnek MSEEs. Néven PE-MSEEs ebben a dokumentumban.
5.  MSEEs: A Microsoft vállalati peremhálózati (MSEE) ExpressRoute útválasztók
6.  (VNet) virtuális hálózati átjáró
7.  Számítási eszköz az Azure virtuális hálózaton

A felhőalapú Exchange közös elhelyezés vagy Point-to-Point protokoll Ethernet-kapcsolat modellek használata esetén az ügyfél peremhálózati útválasztója (2) a BGP társviszony-létesítés (5) MSEEs hozzák létre. 3. és 4 hálózati pontok volna továbbra is létezik, de lehet némileg átlátszó 2. rétegbeli eszközök.

Ha a bármely elem közöttiként (IPVPN) kapcsolat modellt használja, a PEs (MSEE hozzáférhető) (4) hozzák létre a BGP társviszony-létesítés MSEEs (5). Útvonalak volna majd propagálódik vissza az ügyfél hálózati az IPVPN szolgáltatás szolgáltató hálózaton keresztül.

>[!NOTE]
>Microsoft ExpressRoute magas rendelkezésre állás, a BGP-munkamenetek között MSEEs (5) és a PE-MSEEs (4) a redundáns pár van szükség. Hálózati elérési út redundáns két szintén javasolt felhasználói hálózat és a PE-CEs között. Azonban bármely elem közöttiként (IPVPN) kapcsolat modellben, egyetlen CE eszközt (2) lehet, hogy kapcsolódik egy vagy több PEs (3).
>
>

Egy ExpressRoute-kapcsolatcsoportot ellenőrzéséhez a következő lépéseket (és a hálózati pont, a hozzájuk társított számokat által jelzett) foglalkozik:
1. [Ellenőrizze a kiépítésével és állapota (5)](#validate-circuit-provisioning-and-state)
2. [Ellenőrizze a legalább egy ExpressRoute-társviszony létesítése – konfigurálva (5)](#validate-peering-configuration)
3. [Ellenőrizze a Microsoft és a szolgáltatás között ARP szolgáltató (4. és 5 közötti kapcsolat)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Ellenőrizze a BGP és a MSEE (BGP-4-5 és 5 és 6 Ha csatlakozik egy VNet között) az útvonalak](#validate-bgp-and-routes-on-the-msee)
5. [Ellenőrizze a forgalom statisztika (5 áthaladó forgalom)](#check-the-traffic-statistics)

További ellenőrzések és ellenőrzések hozzáadódnak a jövőbeli, próbálkozzon újra a havi!

##<a name="validate-circuit-provisioning-and-state"></a>Kiépítésével és állapotának ellenőrzése
Függetlenül a kapcsolat modell ExpressRoute-kapcsolatcsoportot létrehozni rendelkezik, és így a szolgáltatás kulcs létre kiépítésével. A redundáns 2. rétegbeli kapcsolatot PE-MSEEs (4) és MSEEs (5) között ExpressRoute-kapcsolatcsoportot kiépítés hoz létre. Létrehozása, módosítása, telepítéséhez, és ellenőrizze a ExpressRoute-kapcsolatcsoportot kapcsolatos további információkért lásd: a cikk [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot][CreateCircuit].

>[!TIP]
>A szolgáltatás kulcs egyedileg azonosítja az ExpressRoute-kapcsolatcsoportot. A kulcs mindenképpen szükséges a legtöbb ebben a dokumentumban említett powershell-parancsokat. Emellett kell segítségre van szüksége a Microsoft vagy egy ExpressRoute partnertől ExpressRoute a problémákat, adja meg a szolgáltatás kulcsát a kapcsolatcsoport könnyen azonosításához.
>
>

###<a name="verification-via-the-azure-portal"></a>Az Azure-portálon ellenőrzése
Az Azure portálon, ExpressRoute-kapcsolatcsoportot állapotának ellenőrizhetők kiválasztásával ![2][2] a bal oldali oldalsó sáv menüben, és jelölje be az ExpressRoute-kapcsolatcsoportot. Egy ExpressRoute kiválasztása "Összes erőforrás" alatt szereplő áramkör az ExpressRoute-kapcsolatcsoport panel megnyitása. Az a ![3][3] részében találhatja, a essentials találhatók, ahogy az alábbi képernyőfelvételen az ExpressRoute:

![4][4]    

Az ExpressRoute Essentials *állapot áramkör* Microsoft oldalán a kapcsolatcsoport állapotát jelzi. *Szolgáltató állapota* azt jelzi, hogy a kapcsolatcsoport nem *kiépítve/nem létesített* szolgáltatói oldalán. 

Az ExpressRoute-kapcsolatcsoportot működik, hogy a *áramkör állapot* kell lennie *engedélyezve* és a *szolgáltató állapota* kell *kiépítve*.

>[!NOTE]
>Ha a *állapot áramkör* van nincs engedélyezve, forduljon a [Microsoft Support][Support]. Ha a *szolgáltató állapota* van nincs telepítve, forduljon a szolgáltatójához.
>
>

###<a name="verification-via-powershell"></a>PowerShell ellenőrzése
Egy erőforráscsoportban található ExpressRoute-Kapcsolatcsoportok listájában, használja a következő parancsot:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Az erőforráscsoport neve az Azure szolgáltatáson keresztül érheti el. Tekintse meg az előző ebben a dokumentumban, és vegye figyelembe, hogy a példa képernyőfelvétel az erőforráscsoport neve szerepel.
>
>

Válasszon egy adott ExpressRoute-kapcsolatcsoportot erőforráscsoportban, használja a következő parancsot:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

A rendszer egy minta választ:

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

Annak ellenőrzéséhez, hogy ha ExpressRoute-kapcsolatcsoportot működőképességét, különös figyelmet fordítani az alábbi mezők:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Ha a *CircuitProvisioningState* van nincs engedélyezve, forduljon a [Microsoft Support][Support]. Ha a *ServiceProviderProvisioningState* van nincs telepítve, forduljon a szolgáltatójához.
>
>

###<a name="verification-via-powershell-classic"></a>Ellenőrzési PowerShell (klasszikus)
Adott előfizetésen belül ExpressRoute-Kapcsolatcsoportok listájában, használja a következő parancsot:

    Get-AzureDedicatedCircuit

Egy adott ExpressRoute-kapcsolatcsoportot kiválasztásához a következő paranccsal:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

A rendszer egy minta választ:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Annak ellenőrzéséhez, hogy ha ExpressRoute-kapcsolatcsoportot működőképességét, különös figyelmet fordítani az alábbi mezők: ServiceProviderProvisioningState: kiépített állapot: engedélyezve

>[!NOTE]
>Ha a *állapot* van nincs engedélyezve, forduljon a [Microsoft Support][Support]. Ha a *ServiceProviderProvisioningState* van nincs telepítve, forduljon a szolgáltatójához.
>
>

##<a name="validate-peering-configuration"></a>Társviszony-létesítési konfiguráció ellenőrzése
A szolgáltató a kiépítés az ExpressRoute-kapcsolatcsoport befejezése után egy útválasztási konfigurációja az ExpressRoute-kapcsolatcsoport között MSEE-PRs (4) és MSEEs (5) keresztül hozhatók létre. Minden egyes ExpressRoute-kapcsolatcsoportot rendelkezhet engedélyezve van egy, kettő vagy három útválasztási környezetek: Azure magánhálózati társviszony-létesítési (forgalom titkos virtuális hálózatok az Azure-ban), az Azure nyilvános társviszony-létesítés (forgalom nyilvános IP-címek az Azure-ban) és a Microsoft társviszony-létesítési (forgalom Dynamics 365 és az Office 365 való). Létrehozásához és módosításához útválasztási konfigurációs módjáról további információkért lásd: a cikk [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot útválasztási][CreatePeering].

###<a name="verification-via-the-azure-portal"></a>Az Azure-portálon ellenőrzése

>[!NOTE]
>Ha a 3 rétegbeli a szolgáltató által biztosított, és a társviszony üresek a portálon, a frissítés gombra kattintva a protal a kapcsolatcsoport beállításainak frissítése. Ez a művelet a kapcsolatcsoport alkalmazzák a megfelelő útválasztási konfigurációja. 
>
>

Az Azure portálon, ExpressRoute-kapcsolatcsoportot állapotának ellenőrizhetők kiválasztásával ![2][2] a bal oldali oldalsó sáv menüben, és jelölje be az ExpressRoute-kapcsolatcsoportot. Egy ExpressRoute kiválasztása "Összes erőforrás" alatt szereplő áramkör nyitna az ExpressRoute-kapcsolatcsoport panel. Az a ![3][3] részében találhatja, a essentials kellene szerepelnie, ahogy az az alábbi képernyőfelvételen ExpressRoute:

![5][5]

Az előző példában, beállításértékeket Azure magánhálózati társviszony-létesítési útválasztási környezet engedélyezve van, mivel a nyilvános Azure és a Microsoft társviszony-létesítési útválasztási környezetek nem engedélyezett. Sikeresen engedélyezve a társviszony-létesítési környezetben is rendelkezik az elsődleges és másodlagos Point-to-Point protokoll (BGP szükséges) alhálózatok szerepel a listában. A/30 alhálózatokat használják a MSEEs és a PE-MSEEs illesztő IP-címét. 

>[!NOTE]
>Társviszony-létesítés nem engedélyezett, ha ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos alhálózatok egyeznek-e a konfigurációt a PE-MSEEs. Ha nem, módosítsa a MSEE útválasztók, tekintse meg [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot útválasztási][CreatePeering]
>
>

###<a name="verification-via-powershell"></a>PowerShell ellenőrzése
Ahhoz, hogy az Azure magánhálózati társviszony-létesítési konfiguráció részletei, használja a következő parancsokat:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

A minta választ, egy sikeresen konfigurált magánhálózati társviszony-létesítés, a következő:

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

 Sikeresen engedélyezve a társviszony-létesítési környezetben az elsődleges és másodlagos címelőtagokat felsorolt kellene lennie. A/30 alhálózatokat használják a MSEEs és a PE-MSEEs illesztő IP-címét.

Ahhoz, hogy az Azure nyilvános társviszony-létesítési konfiguráció részletei, használja a következő parancsokat:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Ahhoz, hogy a Microsoft társviszony-létesítési konfiguráció részletei, használja a következő parancsokat:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Ha nincs konfigurálva a társviszony-létesítés, nem lenne egy hibaüzenetet. Mintaválasz, ha a megadott társviszony-létesítés (Azure nyilvános társviszony-létesítés ebben a példában) nincs beállítva a kapcsolatcsoport belül:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Társviszony-létesítés nem engedélyezett, ha ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos alhálózatok egyeznek-e a konfigurációt a csatolt PE-MSEE a. Emellett ellenőrizze, hogy a megfelelő *VlanId*, *AzureASN*, és *PeerASN* MSEEs használják, és ha ezeket az értékeket rendel a csatolt PE-MSEE használt megfelelően. Az MD5 kivonatoló választása esetén a megosztott kulcsot kell ugyanazon a MSEE és a PE-MSEE pár. A MSEE útválasztók konfigurációjának módosítása, lásd [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot útválasztási] [CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Ellenőrzési PowerShell (klasszikus)
Ahhoz, hogy az Azure magánhálózati társviszony-létesítési konfiguráció részletei, a következő paranccsal:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

A rendszer egy minta választ, egy sikeresen konfigurált magánhálózati társviszony-létesítés esetében:

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

A sikeres legyen, engedélyezve társviszony-létesítési környezetben az elsődleges és másodlagos társ alhálózatok felsorolt kellene lennie. A/30 alhálózatokat használják a MSEEs és a PE-MSEEs illesztő IP-címét.

Ahhoz, hogy az Azure nyilvános társviszony-létesítési konfiguráció részletei, használja a következő parancsokat:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Ahhoz, hogy a Microsoft társviszony-létesítési konfiguráció részletei, használja a következő parancsokat:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>3 rétegbeli esetében a szolgáltató által beállított, az ExpressRoute-társviszony a portál vagy PowerShell beállítás felülírja a a szolgáltatás-szolgáltató beállításai. A szolgáltató támogatja a szolgáltatót társviszony-létesítési ügyféloldali-beállításainak visszaállításáról van szükség. Az ExpressRoute-társviszony csak akkor módosítsa, ha biztos, hogy a szolgáltató biztosítja a csak a 2. réteg szolgáltatásokat!
>
>

<p/>
>[!NOTE]
>Társviszony-létesítés nem engedélyezett, ha ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos társ alhálózatok egyeznek-e a konfigurációt a csatolt PE-MSEE a. Emellett ellenőrizze, hogy a megfelelő *VlanId*, *AzureAsn*, és *PeerAsn* MSEEs használják, és ha ezeket az értékeket rendel a csatolt PE-MSEE használt megfelelően. A MSEE útválasztók konfigurációjának módosítása, lásd [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot útválasztási] [CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>A Microsoft és a szolgáltató közötti ARP ellenőrzése
Ez a szakasz (klasszikus) PowerShell-parancsokat használ. Ha használta PowerShell Azure Resource Manager parancsok, győződjön meg arról, hogy rendelkezik-e rendszergazdai vagy társadminisztrátori hozzáférése az előfizetéshez. Hibaelhárítás az Azure Resource Manager használatával parancsok olvassa el a [ARP első tábla a Resource Manager üzembe helyezési modellel] [ ARP] dokumentum.

>[!NOTE]
>Ahhoz, hogy az ARP, az Azure portál és az Azure Resource Manager PowerShell-parancsokkal használható. Ha hiba történik, az Azure Resource Manager PowerShell-parancsokkal, klasszikus PowerShell-parancsok klasszikus PowerShell parancsokat is dolgozhat Azure Resource Manager ExpressRoute-Kapcsolatcsoportok használható.
>
>

Az az elsődleges MSEE útválasztó ARP-táblázat lekérése a magánhálózati társviszony-létesítés, a következő paranccsal:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Egy példa egy válasz ahhoz, hogy a parancs sikeres forgatókönyvben:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Ehhez hasonlóan a MSEE az ARP-táblázat ellenőrizheti a *elsődleges*/*másodlagos* elérési útja, a *titkos*/*nyilvános*/*Microsoft* esetében.

A következő példa bemutatja, hogy a parancs a társviszony-létesítés a válasz nem létezik.

    ARP Info:
       
>[!NOTE]
>Ha az ARP-táblázat nem rendelkezik a MAC-címek hozzárendelve felületek IP-címét, áttekintheti a következőket:
>1. Ha az első IP-címét a/30-as alhálózat hozzárendelt MSEE-PR és MSEE közötti kapcsolat MSEE-PR. felületén használja a rendszer Azure mindig MSEEs a második IP-címet használja.
>2. Győződjön meg arról, ha az ügyfél (C-címke) és a VLAN-címkék szolgáltatás (S-címke) felel meg a MSEE-PR és MSEE pár mindkét.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Ellenőrizze a BGP és a MSEE útvonalak
Ez a szakasz (klasszikus) PowerShell-parancsokat használ. Ha használta PowerShell Azure Resource Manager parancsok, győződjön meg arról, hogy rendelkezik-e rendszergazdai vagy társadminisztrátori hozzáférése az előfizetéshez.

>[!NOTE]
>Ahhoz, hogy a BGP-információkat, az Azure portál és az Azure Resource Manager PowerShell-parancsokkal használható. Ha hiba történik, az Azure Resource Manager PowerShell-parancsokkal, klasszikus PowerShell-parancsok klasszikus PowerShell parancsokat is dolgozhat Azure Resource Manager ExpressRoute-Kapcsolatcsoportok használható.
>
>

Ahhoz, hogy az útvonaltábla (BGP szomszéd) összefoglaló egy adott útválasztási környezet, a következő paranccsal:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Egy példa egy válasz a következő:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Ahogy az előző példában is látható, a parancs nem hasznos, ha szeretné meghatározni, hogy mennyi ideig útválasztási környezet lett létrehozva. Azt is jelzi a társviszony-létesítési útválasztó által hirdetett útvonal előtagok számát.

>[!NOTE]
>Ha az állapot az aktív vagy inaktív, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos társ alhálózatok egyeznek-e a konfigurációt a csatolt PE-MSEE a. Emellett ellenőrizze, hogy a megfelelő *VlanId*, *AzureAsn*, és *PeerAsn* MSEEs használják, és ha ezeket az értékeket rendel a csatolt PE-MSEE használt megfelelően. Az MD5 kivonatoló választása esetén a megosztott kulcsot kell ugyanazon a MSEE és a PE-MSEE pár. A MSEE útválasztók konfigurációjának módosítása, lásd [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot útválasztási][CreatePeering].
>
>

<p/>
>[!NOTE]
>Ha egyes célhoz keresztül adott társviszony-létesítés nem érhetők el, ellenőrizze, a MSEEs a társviszony-létesítési adott környezethez tartozó útvonaltábla. Ha egy egyező előtag (Ez lehet gelt IP) az útválasztási táblázatban szerepel, majd ellenőrizze Ha tűzfalak/NSG/hozzáférés-vezérlési listák elérési útján, és ha a forgalom.
>
>

A teljes útvonaltábla lekérése MSEE a *elsődleges* az adott elérési *titkos* útválasztási környezet, használja a következő parancsot:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Egy példa a parancs sikeres eredménye a következő:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Ehhez hasonlóan az útválasztási táblát a MSEE ellenőrizheti a *elsődleges*/*másodlagos* elérési útja, a *titkos*/*nyilvános*/*Microsoft* társviszony-létesítési környezetben.

A következő példa bemutatja, hogy a parancs a társviszony-létesítés a válasz nem létezik:

    Route Table Info:

##<a name="check-the-traffic-statistics"></a>A forgalom statisztikai adatainak ellenőrzése
Sikerült megszerezni a kombinált elsődleges és másodlagos elérési forgalom statisztika--bájt bejövő és kimenő adatforgalma – társviszony-létesítési környezetet, a következő paranccsal:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

A minta a parancs kimenete:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

A minta a nem létező társviszony-létesítés parancs kimenete:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>További lépések
További információt vagy a help tekintse meg a következőket:

- [A Microsoft támogatás][Support]
- [Létrehozásához és módosításához ExpressRoute-kapcsolatcsoportot][CreateCircuit]
- [Létrehozásához és módosításához útválasztás az ExpressRoute-kapcsolatcsoportot][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logikai Expressroute-kapcsolat"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Minden erőforrás ikon"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Áttekintés ikon"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials minta képernyőképe"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials minta képernyőképe"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






