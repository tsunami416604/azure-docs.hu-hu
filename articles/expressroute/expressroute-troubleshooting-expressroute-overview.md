---
title: 'Kapcsolat ellenőrzése – ExpressRoute hibaelhárítási útmutatója: Azure | Microsoft Docs'
description: Ez az oldal útmutatást nyújt a ExpressRoute-áramkör végpontok közötti kapcsolatának hibaelhárításához és ellenőrzéséhez.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 026900e3dcbf7c20750bb8e17e44ba64897c9a30
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "71123441"
---
# <a name="verifying-expressroute-connectivity"></a>Az ExpressRoute-kapcsolat ellenőrzése
Ez a cikk segítséget nyújt az ExpressRoute-kapcsolatok ellenőrzéséhez és hibakereséséhez. A ExpressRoute, amely a helyszíni hálózatot kiterjeszti a Microsoft-felhőbe egy olyan privát kapcsolaton keresztül, amelyet egy kapcsolat szolgáltatója tesz lehetővé, a következő három különálló hálózati zónát foglalja magában:

-   Ügyfél hálózata
-   Szolgáltatói hálózat
-   Microsoft Datacenter

A jelen dokumentum célja, hogy segítse a felhasználót annak azonosításában, hogy a probléma hol található (vagy még ha) fennáll a kapcsolódási probléma, és hogy melyik zónában van a megfelelő csapat segítségének megkeresése a probléma megoldásához. Ha a probléma megoldásához Microsoft-támogatásra van szükség, nyisson meg egy támogatási jegyet [Microsoft ügyfélszolgálata][Support].

> [!IMPORTANT]
> A dokumentum célja, hogy segítséget nyújtson az egyszerű problémák diagnosztizálásában és javításában. Nem helyettesíti a Microsoft támogatási szolgálatát. Nyisson meg egy támogatási jegyet [Microsoft ügyfélszolgálata][Support] ha nem tudja megoldani a problémát a megadott útmutatás használatával.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
Az alábbi ábrán egy ügyfél hálózatának logikai kapcsolata látható a Microsoft Network ExpressRoute használatával.
[![1]][1]

Az előző ábrán a számok a legfontosabb hálózati pontokat jelölik. A hálózati pontokra gyakran hivatkoznak a cikk a hozzájuk társított szám alapján.

A ExpressRoute kapcsolati modelljétől (a felhőalapú Exchange közös elhelyezésének, pont-pont típusú Ethernet-kapcsolatnak vagy bármely-a-minden (IPVPN)) függően a 3. és 4. hálózati pont lehet kapcsolók (2. rétegbeli eszközök). A legfontosabb hálózati pontok a következők:

1.  Ügyfél-számítási eszköz (például kiszolgáló vagy számítógép)
2.  CEs: ügyfél peremhálózati útválasztói 
3.  PEs (CE felé irányuló): Provider Edge-útválasztók/kapcsolók, amelyek az ügyfél peremhálózati útválasztói felé néznek. Ez a dokumentum PE-CEs néven szerepel.
4.  PEs (MSEE szembenézve): Provider Edge-útválasztók/kapcsolók, amelyek a Msee felé néznek. Ez a dokumentum PE-Msee néven szerepel.
5.  Msee: Microsoft Enterprise Edge (MSEE) ExpressRoute útválasztók
6.  Virtual Network (VNet) átjáró
7.  Számítási eszköz az Azure VNet

Ha a felhőalapú Exchange közös elhelyezésű vagy pont-pont típusú Ethernet-kapcsolati modelljeit használják, az ügyfél peremhálózati útválasztója (2) a BGP-társat a Msee (5) használatával hozza létre. A 3. és a 4. hálózati pontok továbbra is léteznek, de némileg transzparensek a 2. rétegbeli eszközöknél.

Ha a bármely-a-bármelyik (IPVPN) kapcsolati modellt használja, a PEs (MSEE szembenéző) (4) a BGP-társat a Msee (5) használatával hozza létre. Az útvonalak ezután a IPVPN-szolgáltató hálózatán keresztül propagálják az ügyfél hálózatát.

> [!NOTE]
>A ExpressRoute magas rendelkezésre állása érdekében a Microsoft a Msee (5) és a PE-Msee (4) közötti BGP-munkamenetek redundáns pár követelményét igényli. A hálózati elérési utak redundáns párosítása is javasolt a Customer Network és a PE-CEs között. A bármely-a-any (IPVPN) kapcsolati modellben azonban egyetlen CE-eszköz (2) kapcsolódhat egy vagy több PEs-hoz (3).
>
>

Egy ExpressRoute áramkör érvényesítéséhez a következő lépéseket kell megadnia (a társított szám által jelzett hálózati ponttal):
1. [Az áramkör üzembe helyezésének és állapotának ellenőrzése (5)](#validate-circuit-provisioning-and-state)
2. [Ellenőrizze, hogy legalább egy ExpressRoute-társítás konfigurálva van-e (5)](#validate-peering-configuration)
3. [Az ARP ellenőrzése a Microsoft és a szolgáltató között (4 és 5 közötti kapcsolat)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Érvényesítse a BGP-t és az útvonalakat a MSEE (BGP 4 – 5 között, és 5 – 6, ha egy VNet csatlakoztatva van)](#validate-bgp-and-routes-on-the-msee)
5. [A forgalmi statisztika (5-öt áthaladó forgalom) keresése](#check-the-traffic-statistics)

A későbbiekben további érvényesítéseket és ellenőrzéseket is felveszünk, és visszatekintjük a havi rendszerességgel.

## <a name="validate-circuit-provisioning-and-state"></a>Az áramkör üzembe helyezésének és állapotának ellenőrzése
A kapcsolati modelltől függetlenül létre kell hozni egy ExpressRoute áramkört, és így az áramkör kiépítés során generált szolgáltatási kulcsot. Az ExpressRoute áramkör üzembe helyezése egy redundáns 2. rétegbeli kapcsolatot létesít a PE-Msee (4) és a Msee (5) között. A ExpressRoute-áramkör létrehozásával, módosításával, kiépítésével és ellenőrzésével kapcsolatos további információkért tekintse meg a [ExpressRoute-áramkör létrehozása és módosítása][CreateCircuit]című cikket.

>[!TIP]
>A szolgáltatási kulcs egyedileg azonosít egy ExpressRoute-áramkört. Ez a kulcs szükséges a dokumentumban említett PowerShell-parancsok többségéhez. Ha a Microsofttól vagy egy ExpressRoute-partnertől segítségre van szüksége a ExpressRoute kapcsolatos problémák megoldásához, adja meg a szolgáltatás kulcsát, hogy az áramkör könnyen azonosítható legyen.
>
>

### <a name="verification-via-the-azure-portal"></a>Ellenőrzés a Azure Portal használatával
A Azure Portal egy ExpressRoute áramkör állapotát a bal oldali menüsoron, majd a ExpressRoute áramkör ![kiválasztásával][2] lehet ellenőrizni. A "minden erőforrás" alatt felsorolt ExpressRoute kör kiválasztásával megnyílik az ExpressRoute-áramkör panel. A panel ![3][3] szakaszában a ExpressRoute Essentials a következő képernyőképen látható módon jelenik meg:

![4][4]    

A ExpressRoute Essentialsben az áramkör *állapota* az áramkör állapotát jelzi a Microsoft oldalon. A *szolgáltató állapota* azt jelzi, hogy az áramkör üzembe helyezése */kiépítés nem* történt-e meg a szolgáltatói oldalon. 

Ahhoz, hogy egy ExpressRoute-áramkör működőképes legyen, *engedélyezni* kell az *áramköri állapotot* , és a *szolgáltatói állapotot* kell *kiépíteni*.

> [!NOTE]
> Ha az *áramkör állapota* nincs engedélyezve, forduljon a [Microsoft ügyfélszolgálatahoz][Support]. Ha a *szolgáltató állapota* nincs kiépítve, forduljon a szolgáltatóhoz.
>
>

### <a name="verification-via-powershell"></a>Ellenőrzés a PowerShell használatával
Egy erőforráscsoport összes ExpressRoute-áramkörének listázásához használja a következő parancsot:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Az erőforráscsoport nevét az Azure-on keresztül érheti el. Tekintse meg a dokumentum előző alszakaszát, és vegye figyelembe, hogy az erőforráscsoport neve szerepel a példa képernyőképén.
>
>

Egy adott ExpressRoute-áramkör kiválasztásához használja az alábbi parancsot:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

A minta válasza:

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

Annak ellenőrzéséhez, hogy a ExpressRoute áramkör működik-e, különös figyelmet fordít a következő mezőkre:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Ha a *CircuitProvisioningState* nincs engedélyezve, forduljon a [Microsoft ügyfélszolgálatahoz][Support]. Ha a *ServiceProviderProvisioningState* nincs kiépítve, forduljon a szolgáltatóhoz.
>
>

### <a name="verification-via-powershell-classic"></a>Ellenőrzés a PowerShell (klasszikus) használatával
Az előfizetéshez tartozó összes ExpressRoute-áramkör listázásához használja az alábbi parancsot:

    Get-AzureDedicatedCircuit

Egy adott ExpressRoute-kör kiválasztásához használja a következő parancsot:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

A minta válasza:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Annak ellenőrzéséhez, hogy az ExpressRoute áramkör működik-e, különös figyelmet fordít a következő mezőkre: ServiceProviderProvisioningState: kiépített állapot: engedélyezve

> [!NOTE]
> Ha az *állapot* nincs engedélyezve, forduljon a [Microsoft ügyfélszolgálatahoz][Support]. Ha a *ServiceProviderProvisioningState* nincs kiépítve, forduljon a szolgáltatóhoz.
>
>

## <a name="validate-peering-configuration"></a>Egyenrangú konfiguráció ellenőrzése
Miután a szolgáltató befejezte a ExpressRoute áramkör kiépítési folyamatát, a MSEE-PRs (4) és a Msee (5) közötti ExpressRoute áramkörön létrehozhat útválasztási konfigurációt. Minden ExpressRoute áramkörhöz engedélyezve van egy, kettő vagy három útválasztási környezet: Azure Private-kapcsolat (az Azure-beli virtuális hálózatok felé irányuló forgalom), Azure nyilvános kapcsolat (nyilvános IP-címekre irányuló forgalom az Azure-ban) és Microsoft-partneri kapcsolat (az Office 365-es adatforgalom). Az útválasztási konfiguráció létrehozásával és módosításával kapcsolatos további információkért tekintse meg a [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering]című cikket.

### <a name="verification-via-the-azure-portal"></a>Ellenőrzés a Azure Portal használatával

> [!NOTE]
> Ha a 3. réteg a szolgáltató által biztosított, és a társítások üresek a portálon, frissítse az áramköri konfigurációt a portál frissítés gombjával. Ez a művelet a megfelelő útválasztási konfigurációt alkalmazza az áramkörön. 
>
>

A Azure Portal egy ExpressRoute áramkör állapota ellenőrizhető a bal oldali menüsorban található ![2][2] lehetőség kiválasztásával, majd a ExpressRoute áramkör kiválasztásával. A "minden erőforrás" alatt felsorolt ExpressRoute kör kiválasztásával megnyílik az ExpressRoute-áramkör panelje. A panel ![3][3] szakaszában a ExpressRoute Essentials a következő képernyőképen látható módon jelenik meg:

![5][5]

Az előző példában látható módon, ahogy az Azure Private peering útválasztási környezete engedélyezve van, az Azure nyilvános és a Microsoft egyenrangú útválasztási környezetek nem engedélyezettek. Egy sikeresen engedélyezett társítási környezet esetében az elsődleges és a másodlagos pont – pont (BGP) alhálózatok is elérhetők. A/30 alhálózatot a Msee és a PE-Msee kapcsolati IP-címéhez használja a rendszer. 

> [!NOTE]
> Ha nincs engedélyezve a társítás, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos alhálózatok megfelelnek-e a PE-Msee konfigurációjának. Ha nem, a MSEE-útválasztók konfigurációjának módosításához tekintse meg a [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering] című témakört.
>
>

### <a name="verification-via-powershell"></a>Ellenőrzés a PowerShell használatával
Az Azure Private peering konfigurációs adatainak beszerzéséhez használja a következő parancsokat:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

A sikeres konfiguráláshoz a következő a válasz:

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

 A sikeresen engedélyezett társítási környezet a felsorolt elsődleges és másodlagos címek előtagjai lennének. A/30 alhálózatot a Msee és a PE-Msee kapcsolati IP-címéhez használja a rendszer.

Az Azure nyilvános társ-összevonási konfiguráció részleteit az alábbi parancsokkal szerezheti be:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

A Microsoft társközi konfigurációs adatainak beszerzéséhez használja a következő parancsokat:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Ha nincs konfigurálva egy társítás, hibaüzenet jelenik meg. Egy példa válaszra, ha a megadott (Azure-beli nyilvános) kapcsolat nem az áramkörön belül van konfigurálva:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Ha nincs engedélyezve a társítás, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos alhálózatok megfelelnek-e a társított PE-MSEE konfigurációjának. Ellenőrizze azt is, hogy a megfelelő *VlanId*, *AzureASN*és *PeerASN* használják-e a msee, és hogy ezek az értékek a társított PE-MSEE-ben használt értékekre vannak leképezve. Ha az MD5-kivonatolás van kiválasztva, a megosztott kulcsnak azonosnak kell lennie a MSEE és a PE-MSEE párban. A MSEE-útválasztók konfigurációjának módosításához tekintse meg a [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering]című témakört.  
>
>

### <a name="verification-via-powershell-classic"></a>Ellenőrzés a PowerShell (klasszikus) használatával
Az Azure Private peering konfigurációs adatainak beszerzéséhez használja a következő parancsot:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

A sikeres konfiguráláshoz a következő példa a válasz:

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

Egy sikeres, engedélyezett társítási kontextusban az elsődleges és a másodlagos társ-alhálózatok vannak felsorolva. A/30 alhálózatot a Msee és a PE-Msee kapcsolati IP-címéhez használja a rendszer.

Az Azure nyilvános társ-összevonási konfiguráció részleteit az alábbi parancsokkal szerezheti be:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

A Microsoft társközi konfigurációs adatainak beszerzéséhez használja a következő parancsokat:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> Ha a szolgáltató a 3. rétegbeli társításokat állította be, a ExpressRoute-társítások a portálon vagy a PowerShellen keresztül történő beállítása felülírja a szolgáltatói beállításokat. A szolgáltatói oldali társítás beállításainak alaphelyzetbe állításához a szolgáltató támogatására van szükség. Csak akkor módosítsa a ExpressRoute-társításokat, ha biztos abban, hogy a szolgáltató csak a 2. rétegbeli szolgáltatásokat biztosítja!
>
>

> [!NOTE]
> Ha nincs engedélyezve a társítás, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos egyenrangú alhálózatok megfelelnek-e a társított PE-MSEE konfigurációjának. Ellenőrizze azt is, hogy a megfelelő *VlanId*, *AzureAsn*és *PeerAsn* használják-e a msee, és hogy ezek az értékek a társított PE-MSEE-ben használt értékekre vannak leképezve. A MSEE-útválasztók konfigurációjának módosításához tekintse meg a [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering]című témakört.
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Az ARP ellenőrzése a Microsoft és a szolgáltató között
Ez a szakasz a PowerShell (klasszikus) parancsokat használja. Ha a PowerShell Azure Resource Manager parancsait használta, ellenőrizze, hogy rendelkezik-e rendszergazdai/társ-rendszergazdai hozzáféréssel az előfizetéshez. Azure Resource Manager parancsok használatával kapcsolatos hibaelhárításhoz tekintse meg a [Resource Manager-alapú üzemi modell dokumentumának beszerzése ARP-táblákat][ARP] .

> [!NOTE]
>Az ARP beszerzéséhez a Azure Portal és az Azure Resource Manager PowerShell-parancsokat is használhatja. Ha a Azure Resource Manager PowerShell-parancsokkal kapcsolatos hibák léptek fel, a klasszikus PowerShell-parancsok ugyanúgy működnek, mint a klasszikus PowerShell-parancsok, Azure Resource Manager ExpressRoute áramkörök is működnek.
>
>

A következő paranccsal kérheti le az ARP-táblázatot az elsődleges MSEE útválasztóról a privát társak számára:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

A parancsra adott válasz például a sikeres forgatókönyvben:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Hasonlóképpen megtekintheti az ARP-táblázatot a MSEE *elsődleges* /*másodlagos* elérési útjában *, a* nyilvános /*nyilvános* /*Microsoft* -társak esetében.

Az alábbi példa azt szemlélteti, hogy a parancs válasza nem létezik a társításhoz.

    ARP Info:
       
> [!NOTE]
> Ha az ARP-tábla nem rendelkezik a MAC-címekhez hozzárendelt adapterek IP-címeivel, tekintse át a következő információkat:
>1. Ha a MSEE-PR és a MSEE közötti kapcsolathoz hozzárendelt/30 alhálózat első IP-címe a MSEE-PR felületén van használatban. Az Azure mindig a második IP-címet használja a Msee.
>2. Ellenőrizze, hogy az ügyfél (C-tag) és a szolgáltatás (S-tag) VLAN-címkék egyeznek-e mind a MSEE, mind a MSEE párban.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>A BGP és az útvonalak ellenőrzése a MSEE
Ez a szakasz a PowerShell (klasszikus) parancsokat használja. Ha a PowerShell Azure Resource Manager parancsait használta, ellenőrizze, hogy rendelkezik-e rendszergazdai/társ-rendszergazdai hozzáféréssel az előfizetéshez.

> [!NOTE]
>A BGP-információk beszerzéséhez a Azure Portal és az Azure Resource Manager PowerShell-parancsok is használhatók. Ha a Azure Resource Manager PowerShell-parancsokkal kapcsolatos hibák léptek fel, a klasszikus PowerShell-parancsok ugyanúgy működnek, mint a klasszikus PowerShell-parancsok, Azure Resource Manager ExpressRoute áramkörök is működnek.
>
>

Egy adott útválasztási környezet útválasztási táblázatának (BGP szomszéd) összefoglalásának lekéréséhez használja a következő parancsot:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Példa erre a válaszra:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Ahogy az előző példában is látható, a parancs hasznos lehet meghatározni, hogy mennyi ideig lett létrehozva az útválasztási környezet. Azt is jelzi, hogy hány útvonal-előtagokat hirdettek meg a társ-útválasztó.

> [!NOTE]
> Ha az állapot aktív vagy üresjáratban van, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos egyenrangú alhálózatok megfelelnek-e a társított PE-MSEE konfigurációjának. Ellenőrizze azt is, hogy a megfelelő *VlanId*, *AzureAsn*és *PeerAsn* használják-e a msee, és hogy ezek az értékek a társított PE-MSEE-ben használt értékekre vannak leképezve. Ha az MD5-kivonatolás van kiválasztva, a megosztott kulcsnak azonosnak kell lennie a MSEE és a PE-MSEE párban. A MSEE-útválasztók konfigurációjának módosításához tekintse meg a [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering]című témakört.
>
>

> [!NOTE]
> Ha bizonyos célhelyek nem érhetők el egy adott társon keresztül, ellenőrizze az adott társi környezethez tartozó Msee útválasztási táblázatát. Ha az útválasztási táblázatban szerepel az egyező előtag (amely lehet a lefoglalt IP-cím), akkor ellenőrizze, hogy vannak-e tűzfalak/NSG/ACL-ek az elérési úton, és hogy engedélyezik-e a forgalmat.
>
>

A következő paranccsal kérheti le a teljes útválasztási táblázatot a MSEE az adott *privát* útválasztási környezet *elsődleges* elérési útjáról:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

A parancs sikeres kimenetele például a következő:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Hasonlóképpen megtekintheti az útválasztási táblázatot az *elsődleges* /*másodlagos* elérési úton lévő MSEE, a nyilvános */* *nyilvános* / a*Microsoft* egy egyenrangú kontextusban.

Az alábbi példa azt szemlélteti, hogy a parancs válasza nem létezik a társításhoz:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Forgalmi statisztika keresése
A kombinált elsődleges és másodlagos elérési út forgalmi statisztikájának lekéréséhez a következő parancsot használhatja:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

A parancs mintájának kimenete:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

A nem létező társításhoz tartozó parancs mintájának kimenete a következő:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Következő lépések
További információért és segítségért tekintse meg az alábbi hivatkozásokat:

- [Microsoft ügyfélszolgálata][Support]
- [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása][CreateCircuit]
- [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logikai expressz útvonal-kapcsolat"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Minden erőforrás ikon"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Áttekintés ikon"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "A ExpressRoute Essentials minta képernyőképe"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "A ExpressRoute Essentials minta képernyőképe"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






