---
title: 'Azure ExpressRoute: Kapcsolat ellenőrzése – Hibaelhárítási útmutató'
description: Ez a lap az ExpressRoute-kapcsolat hibaelhárításával és érvényesítésével kapcsolatos utasításokat tartalmazza.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330957"
---
# <a name="verifying-expressroute-connectivity"></a>Az ExpressRoute-kapcsolat ellenőrzése
Ez a cikk segítséget nyújt az ExpressRoute-kapcsolat ellenőrzéséhez és hibaelhárításához. Az ExpressRoute egy helyszíni hálózatot terjeszt ki a Microsoft-felhőbe egy olyan magánkapcsolaton keresztül, amelyet általában egy kapcsolatszolgáltató segít. Az ExpressRoute-kapcsolat hagyományosan három különböző hálózati zónát foglal magában, az alábbiak szerint:

-   Ügyfélhálózat
-   Szolgáltatói hálózat
-   Microsoft Datacenter

> [!NOTE]
> Az ExpressRoute közvetlen kapcsolati modelljében (10/100 Gb/s sávszélességgel) az ügyfelek közvetlenül csatlakozhatnak a Microsoft Enterprise Edge (MSEE) útválasztóinak portjához. Ezért a közvetlen kapcsolati modellben csak ügyfél- és Microsoft-hálózati zónák vannak.
>


A dokumentum célja, hogy segítsen a felhasználónak azonosítani, hogy fennáll-e kapcsolódási probléma, és ha van ilyen. Ezáltal, hogy segítsen segítséget kérni a megfelelő csapat megoldani a problémát. Ha a probléma megoldásához Microsoft-támogatásra van szükség, nyisson meg egy támogatási jegyet a [Microsoft támogatási szolgálatával.][Support]

> [!IMPORTANT]
> Ez a dokumentum egyszerű problémák diagnosztizálásának és javításának elősegítésére szolgál. Nem helyettesíti a Microsoft támogatását. Nyisson meg egy támogatási jegyet a [Microsoft támogatási szolgálatával,][Support] ha nem tudja megoldani a problémát a megadott útmutatás alapján.
>
>

## <a name="overview"></a>Áttekintés
Az alábbi ábra az ügyfélhálózat és a Microsoft-hálózat közötti logikai kapcsolatot mutatja be az ExpressRoute használatával.
[![1]][1]

Az előző ábrán a számok a legfontosabb hálózati pontokat jelölik. Ezekre a hálózati pontokra a cikk ben időnként a hozzájuk tartozó szám hivatkozik. Az ExpressRoute-kapcsolati modelltől függően - A Felhőalapú Exchange-kapcsolat, a Point-to-Point Ethernet-kapcsolat vagy az IPVPN (IPVPN) a 3- as és 4-es hálózati pontok lehetnek kapcsolók (Layer 2 eszközök) vagy útválasztók (Layer 3 eszközök). A közvetlen kapcsolódási modellben nincsenek 3-as és 4-es hálózati pontok; ehelyett a cem-ek (2) sötét szálakon keresztül közvetlenül kapcsolódnak a kis- és nagyösszenyomódásokhoz. A legfontosabb hálózati pontok a következők:

1.  Ügyfél számítási eszköz (például kiszolgáló vagy számítógép)
2.  CE-k: Ügyfélperemútválasztók 
3.  PE -k (CE-vel szemben): Az ügyfél peremhálózati útválasztóinak felé néző szolgáltató peremhálózati útválasztói/kapcsolói. Ebben a dokumentumban pe-ek néven szerepel.
4.  PE-k (MSEE szemben): A szolgáltató peremhálózati útválasztói/kapcsolói, amelyek a legnagyobb arányban jelentős ebbe-k felé néznek. Ebben a dokumentumban PE-MSE-k.
5.  Msees: Microsoft Enterprise Edge (MSEE) ExpressRoute-útválasztók
6.  Virtuális hálózati (VNet) átjáró
7.  Számítási eszköz az Azure virtuális hálózaton

Ha a Cloud Exchange-társelhelyezést, a point-to-point Ethernet et vagy a közvetlen kapcsolódási modelleket használja, a (2) közép- és középvállalatok (2) bgp-társviszony-létesítést hoznak létre a kis- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép-és közép- és közép- és közép-irigylési vállalatok (5). 

Ha az Any-to-any (IPVPN) kapcsolati modellt használja, a PE-MSE-k (4) bgp-társviszonyt létesítést hoznak létre a msee-kkel (5). A PE-MSE-k az IPVPN-szolgáltatóhálózaton keresztül továbbítják a Microsofttól kapott útvonalakat az ügyfélhálózatra.

> [!NOTE]
>A magas rendelkezésre állás érdekében a Microsoft teljesen redundáns párhuzamos kapcsolatot hoz létre a kis- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép-és közép- és közép- és közép-irigylési (4) párok között. Az ügyfélhálózat és a PE-ce-k párja között is egy teljesen redundáns párhuzamos hálózati útvonalat ösztönöz. A magas rendelkezésre állással kapcsolatos további információkért tekintse meg a [Tervezés magas rendelkezésre állású expressroute-mal című cikket.][HA]
>
>

Az ExpressRoute-kapcsolati kapcsolat hibaelhárítása az alábbi logikai lépéseket követi:

* [Áramkör-kiépítés és állapot ellenőrzése](#verify-circuit-provisioning-and-state)
  
* [Társviszony-létesítési konfigurációellenőrzése](#validate-peering-configuration)
  
* [ARP érvényesítése](#validate-arp)
  
* [BGP és útvonalak ellenőrzése az MSEE-n](#validate-bgp-and-routes-on-the-msee)
  
* [A forgalom megerősítése](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Áramkör-kiépítés és állapot ellenőrzése
Az ExpressRoute-áramkör kiépítése redundáns 2. Az ExpressRoute-kapcsolat létrehozásáról, módosításáról, kiszervezéséről és ellenőrzéséről az [ExpressRoute-kapcsolat létrehozása és módosítása][CreateCircuit]című témakörben olvashat bővebben.

>[!TIP]
>A szolgáltatáskulcs egyedileg azonosítja az ExpressRoute-áramkört. Ha segítségre van szüksége a Microsofttól vagy egy ExpressRoute-partnertől egy ExpressRoute-probléma megoldásához, adja meg a szolgáltatáskulcsot a kapcsolatcsoport azonosításához.
>
>

### <a name="verification-via-the-azure-portal"></a>Ellenőrzés az Azure portalon keresztül
Az Azure Portalon nyissa meg az ExpressRoute-áramkörpanelt. A panel ![3][3] részében az ExpressRoute-alapok a következő képernyőképen láthatómódon jelennek meg:

![4][4]    

Az ExpressRoute Essentials szolgáltatásban a *Kapcsolatcsoport állapota* jelzi az áramkör állapotát a Microsoft oldalán. *A szolgáltató állapota* azt jelzi, hogy a kapcsolatcsoport *ki van-e építve/nincs kiépítve* a szolgáltatói oldalon. 

Ahhoz, hogy egy ExpressRoute-kapcsolat működjön, a *kapcsolatcsoport állapotának* *engedélyezve* kell lennie, *a Szolgáltató állapotának* *pedig ki építve*nek kell lennie.

> [!NOTE]
> Az ExpressRoute-kapcsolat konfigurálása után, ha a *Kapcsolatcsoport állapota* nem engedélyezett állapotban van, forduljon a [Microsoft támogatási szolgálatához.][Support] Másrészről, ha a *Szolgáltató állapota* nem kiépített állapotban van, forduljon a szolgáltatóhoz.
>
>

### <a name="verification-via-powershell"></a>Ellenőrzés a PowerShellen keresztül
Az erőforráscsoport összes ExpressRoute-kapcsolatának listázásához használja a következő parancsot:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Ha egy erőforráscsoport nevét keresi, az előfizetés összes erőforráscsoportjának listázásával, a *Get-AzResourceGroup* paranccsal szerezheti be.
>


Egy erőforráscsoportban egy adott ExpressRoute-kapcsolat kiválasztásához használja a következő parancsot:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

A minta válasz:

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

Annak ellenőrzéséhez, hogy egy ExpressRoute-kapcsolat működik-e, fordítson különös figyelmet a következő mezőkre:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Az ExpressRoute-kapcsolat konfigurálása után, ha a *Kapcsolatcsoport állapota* nem engedélyezett állapotban van, forduljon a [Microsoft támogatási szolgálatához.][Support] Másrészről, ha a *Szolgáltató állapota* nem kiépített állapotban van, forduljon a szolgáltatóhoz.
>
>

## <a name="validate-peering-configuration"></a>Társviszony-létesítési konfigurációellenőrzése
Miután a szolgáltató befejezte az ExpressRoute-hálózat kiépítését, több eBGP-alapú útválasztási konfiguráció hozható létre az ExpressRoute-áramkörön keresztül a számítógép-gyártók/MSEE-PE-k (2)/(4) és a MsEEs (5) között. Minden ExpressRoute-kapcsolatcsoport rendelkezhet: Azure privát társviszony-létesítés (forgalom az Azure-ban lévő privát virtuális hálózatok) és/vagy a Microsoft társviszony-létesítés (a paaS és az SaaS nyilvános végpontjaira irányuló forgalom). Az útválasztási konfiguráció létrehozásáról és módosításáról az [Útválasztás létrehozása és módosítása ExpressRoute-kapcsolaton iménykapcsolatban][CreatePeering]című témakörben olvashat bővebben.

### <a name="verification-via-the-azure-portal"></a>Ellenőrzés az Azure portalon keresztül

> [!NOTE]
> Az IPVPN-kapcsolati modellben a szolgáltatók felelősek a társviszony-létesítések konfigurálásáért (3. réteg szolgáltatások). Egy ilyen modellben, miután a szolgáltató konfigurált egy társviszony-létesítést, és ha a társviszony-létesítés üres a portálon, próbálja meg frissíteni az áramkör konfigurációját a portál frissítési gombjával. Ez a művelet lekéri az aktuális útválasztási konfigurációt az áramkörből. 
>

Az Azure Portalon az ExpressRoute-kapcsolatlétesítés állapota ellenőrizhető az ExpressRoute-áramkör panel alatt. A panel ![3][3] részében az ExpressRoute társviszony-létesítések a következő képernyőképen látható módon jelennek meg:

![5][5]

Az előző példában, ahogy az Azure privát társviszony-létesítési ki van építve, míg az Azure nyilvános és a Microsoft-társviszony-létesítési nincsenek kiépítve. A sikeresen kiépített társviszony-létesítési környezetben is az elsődleges és másodlagos pont-pont alhálózatok listázott. A /30 alhálózat ot az MSE-k és az első és a közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép-ek ip-címének IP-címéhez használja. A kiépített társviszony-létesítések esetében a listaelem azt is jelzi, hogy ki módosította utoljára a konfigurációt. 

> [!NOTE]
> Ha a társviszony-létesítés engedélyezése sikertelen, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos alhálózatok megegyeznek-e a csatolt CE/PE-MSEE konfigurációjával. Azt is ellenőrizze, hogy a megfelelő *VlanId*, *AzureASN*és *PeerASN* a msees-ken, és ha ezek az értékek leképezik a kapcsolódó CE/PE-MSEE. Ha MD5 kivonatolást választ, a megosztott kulcsnak azonosnak kell lennie az MSEE és a PE-MSEE/CE páron. A korábban konfigurált megosztott kulcs biztonsági okokból nem jelenik meg. Ha ezen konfigurációk bármelyikét módosítania kell egy MSEE-útválasztón, olvassa el az [Útválasztás létrehozása és módosítása ExpressRoute-kapcsolathoz című témakört.][CreatePeering]  
>

> [!NOTE]
> A kapcsolathoz rendelt /30 alhálózaton a Microsoft kiválasztja az Alhálózat második használható IP-címét az MSEE-felülethez. Ezért győződjön meg arról, hogy az alhálózat első használható IP-címe a társviszonyt létesített CE/PE-MSEE-n van hozzárendelve.
>


### <a name="verification-via-powershell"></a>Ellenőrzés a PowerShellen keresztül
Az Azure privát társviszony-létesítési konfigurációs részleteinek leküzdése érdekében használja a következő parancsokat:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

A sikeresen konfigurált privát társviszony-létesítéshez a mintaválasz a következő:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 A sikeresen engedélyezett társviszony-létesítési környezetben az elsődleges és másodlagos címelőtagok szerepelnek. A /30 alhálózat ot az MSE-k és az első és a közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép- és közép-ek ip-címének IP-címéhez használja.

Az Azure nyilvános társviszony-létesítési konfigurációjának részleteinek leküzdése érdekében használja a következő parancsokat:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

A Microsoft társviszony-létesítési konfigurációs részleteinek beszerezéséhez használja a következő parancsokat:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Ha nincs konfigurálva társviszony-létesítés, hibaüzenet jelenik meg. A minta válasz, ha a megadott társviszony-létesítés (Azure nyilvános társviszony-létesítés ebben a példában) nincs konfigurálva a körkapcsolaton belül:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Ha a társviszony-létesítés engedélyezése sikertelen, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos alhálózatok megegyeznek-e a csatolt CE/PE-MSEE konfigurációjával. Azt is ellenőrizze, hogy a megfelelő *VlanId*, *AzureASN*és *PeerASN* a msees-ken, és ha ezek az értékek leképezik a kapcsolódó CE/PE-MSEE. Ha MD5 kivonatolást választ, a megosztott kulcsnak azonosnak kell lennie az MSEE és a PE-MSEE/CE páron. A korábban konfigurált megosztott kulcs biztonsági okokból nem jelenik meg. Ha ezen konfigurációk bármelyikét módosítania kell egy MSEE-útválasztón, olvassa el az [Útválasztás létrehozása és módosítása ExpressRoute-kapcsolathoz című témakört.][CreatePeering]  
>
>

> [!NOTE]
> A kapcsolathoz rendelt /30 alhálózaton a Microsoft kiválasztja az Alhálózat második használható IP-címét az MSEE-felülethez. Ezért győződjön meg arról, hogy az alhálózat első használható IP-címe a társviszonyt létesített CE/PE-MSEE-n van hozzárendelve.
>

## <a name="validate-arp"></a>ARP érvényesítése

Az ARP-tábla egy adott társviszony-létesítés IP-címének és MAC-címének leképezését tartalmazza. Az ExpressRoute-kapcsolatlétesítés ARP-táblája a következő információkat biztosítja az egyes összeköttetésekhez (elsődleges és másodlagos):
* A helyszíni útválasztó-összeköttetés IP-címének hozzárendelése a MAC-címhez
* ExpressRoute-útválasztó ip-címének hozzárendelése a MAC-címhez
* A leképezési ARP-táblák kora segíthet a 2.


Lásd: [ARP-táblák beszerzése az Erőforrás-kezelő telepítési modell dokumentumában,][ARP] az ExpressRoute-társviszony-létesítés ARP-táblájának megtekintéséről, valamint a 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>BGP és útvonalak ellenőrzése az MSEE-n

Ha az útválasztási táblát az MSEE-ből szeretné lekérni a *privát* útválasztási környezet *elsődleges* elérési útján, használja a következő parancsot:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Példa erre a válasz:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Ha az MSEE és a CE/PE-MSEE közötti eBGP-társviszony-létesítés állapota aktív vagy tétlen állapotban van, ellenőrizze, hogy a hozzárendelt elsődleges és másodlagos társalhálózatok megegyeznek-e a csatolt CE/PE-MSEE konfigurációjával. Azt is ellenőrizze, hogy a megfelelő *VlanId*, *AzureAsn*és *PeerAsn* a msees-ken, és ha ezek az értékek leképezik a csatolt PE-MSEE/CE használt értékeket. Ha MD5 kivonatolást választ, a megosztott kulcsnak azonosnak kell lennie az MSEE és a CE/PE-MSEE páron. Ha ezen konfigurációk bármelyikét módosítania kell egy MSEE-útválasztón, olvassa el az [Útválasztás létrehozása és módosítása ExpressRoute-kapcsolathoz című témakört.][CreatePeering]
>


> [!NOTE]
> Ha bizonyos célállomások nem érhetőek el társviszony-létesítés közben, ellenőrizze a megfelelő társviszony-létesítési környezetben az MSG-k útvonaltábláját. Ha egy megfelelő előtag (lehet NATed IP) található az útválasztási táblában, majd ellenőrizze, hogy vannak-e tűzfalak/NSG/ACL-ek az elérési úton, amelyek blokkolják a forgalmat.
>


A következő példa a parancs nem létező társviszony-létesítésre adott válaszát mutatja be:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>A forgalom megerősítése
Az elsődleges és másodlagos elérési út összesített forgalmi statisztikáinak – a társviszony-létesítési környezetben és kívüli bájtok – leigazolásához használja a következő parancsot:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

A parancs mintakimenete a következő:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

A parancs mintakimenete nem létező társviszony-létesítéshez:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Következő lépések
További információkért vagy segítségért tekintse meg az alábbi hivatkozásokat:

- [Microsoft támogatási szolgálat][Support]
- [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása][CreateCircuit]
- [ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Logikai expressz útvonal-kapcsolat"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Minden erőforrás ikon"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Áttekintő ikon"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials mintaképernyő"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials mintaképernyő"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





