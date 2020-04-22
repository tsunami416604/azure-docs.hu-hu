---
title: Az S2S VPN használata az Azure ExpressRoute privát társviszony-létesítésének biztonsági mentéseként | Microsoft dokumentumok
description: Ez a lap architekturális javaslatokat nyújt az Azure ExpressRoute privát társviszony-létesítéss S2S VPN-nel történő biztonsági mentéséhez.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687828"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Az S2S VPN használata az ExpressRoute privát társviszony-létesítésének biztonsági mentéseként

A cikk című [tervezése vész-helyreállítási ExpressRoute privát társviszony-létesítési][DR-PP], megvitattuk, hogy szükség van a biztonsági mentési kapcsolat megoldás egy ExpressRoute privát társviszony-létesítési kapcsolat, és hogyan kell használni a georedundáns ExpressRoute-áramkörök erre a célra. Ebben a cikkben nézzük meg, hogyan lehet kihasználni és fenntartani a helyek közötti (S2S) VPN-t az ExpressRoute privát társviszony-létesítésének visszaénül. 

A georedundáns ExpressRoute-áramkörökkel ellentétben az ExpressRoute-VPN vész-helyreállítási kombinációt csak aktív-passzív módban használhatja. A passzív módban a biztonsági mentési hálózati kapcsolat használatának egyik fő kihívása, hogy a passzív kapcsolat gyakran meghibásodna az elsődleges kapcsolat mellett. A passzív kapcsolat hibáinak gyakori oka az aktív karbantartás hiánya. Ezért ebben a cikkben koncentráljunk arra, hogyan ellenőrizheti és tarthatja aktívan az ExpressRoute-alapú privát társviszony-létesítést tartalmazó S2S VPN-kapcsolatot.

>[!NOTE] 
>Ha egy adott útvonalat expressroute-on és VPN-en keresztül is meghirdetnek, az Azure az ExpressRoute-on keresztül imittált.  
>

Ebben a cikkben tekintse meg, hogyan ellenőrizheti a kapcsolatot mind az Azure szempontjából, mind az ügyfél oldali hálózati peremperspektívából. Az ellenőrzés mindkét végéről történő érvényesítése attól függetlenül segít, hogy ön kezeli-e azokat az ügyféloldali hálózati eszközöket, amelyek a Microsoft hálózati entitásaival egyenrangúak. 

## <a name="example-topology"></a>Példa topológia

A beállítás, van egy helyszíni hálózat csatlakozik egy Azure hub virtuális hálózat egy ExpressRoute-kapcsolaton keresztül, és egy S2S VPN-kapcsolat. Az Azure hub virtuális hálózat a küllővirtuális hálózatra nézve, ahogy az az alábbi ábrán látható:

![1][1]

A beállítás során az ExpressRoute-kapcsolatcsoport a helyszíni "Customer Edge" (CE) útválasztók párján fejeződik be. A helyszíni LAN a CE-útválasztókhoz egy vezérkövető módban működő tűzfalon keresztül csatlakozik. Az S2S VPN közvetlenül levan állítva a tűzfalakon.

Az alábbi táblázat a topológia legfontosabb IP-előtagjait sorolja fel:

| **Entitás** | **Előtag** |
| --- | --- |
| Helyszíni helyi hálózat | 10.1.11.0/25 |
| Az Azure Hub virtuális hálózata | 10.17.11.0/25 |
| Az Azure beszélt virtuális hálózat | 10.17.11.128/26 |
| Helyszíni tesztkiszolgáló | 10.1.11.10 |
| Küllős virtuális hálózat teszt virtuális gépe | 10.17.11.132 |
| ExpressRoute elsődleges kapcsolat p2p alhálózata | 192.168.11.16/30 |
| ExpressRoute másodlagos kapcsolat p2p alhálózata | 192.168.11.20/30 |
| VPN-átjáró elsődleges BGP-társ ÁNAK IP-címe | 10.17.11.76 |
| VPN-átjáró másodlagos BGP-társ IP-címe | 10.17.11.77 |
| Helyszíni tűzfal VPN BGP-társ IP-címe | 192.168.11.88 |
| Elsődleges CE-útválasztó i/f a tűzfal IP-címe felé | 192.168.11.0/31 |
| Tűzfal i/f felé elsődleges CE router IP | 192.168.11.1/31 |
| Másodlagos CE-útválasztó i/f a tűzfal IP felé | 192.168.11.2/31 |
| Tűzfal i/f a másodlagos CE-útválasztó IP-címe felé | 192.168.11.3/31 |


Az alábbi táblázat a topológia ASN-jeit sorolja fel:

| **Autonóm rendszer** | **Asn** |
| --- | --- |
| Helyszíni követelmények | 65020 |
| Microsoft Nagyvállalati Edge | 12076 |
| Virtuális hálózat GW (ExR) | 65515 |
| Virtuális hálózat GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Magas rendelkezésre állás aszimmetria nélkül

### <a name="configuring-for-high-availability"></a>Konfigurálás a magas rendelkezésre álláshoz

[Az ExpressRoute és a helyek közötti kapcsolatok konfigurálása a][Conf-CoExist] meglévő ExpressRoute-kapcsolat és az S2S VPN-kapcsolatok konfigurálását ismerteti. Ahogy azt [tárgyalta tervezése a magas rendelkezésre állás expressroute,,][HA]hogy javítsa ExpressRoute magas rendelkezésre állás a beállítás fenntartja a hálózati redundancia (elkerüli az egypontos hiba) egészen a végpontok ig. Az ExpressRoute-áramkörök elsődleges és másodlagos kapcsolatai is aktív-aktív módban működnek a helyszíni előtagok ugyanazon módon történő hirdetésével mindkét kapcsolaton keresztül. 

Az elsődleges CE-útválasztó helyszíni útvonalhirdetése az ExpressRoute-kapcsolat elsődleges kapcsolatán keresztül az alábbiakban látható (Junos parancsok):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

A másodlagos CE-útválasztó helyszíni útvonalhirdetése az ExpressRoute-kapcsolat on keresztül az alábbi (Junos parancsok):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

A biztonsági mentési kapcsolat magas rendelkezésre állásának javítása érdekében az S2S VPN aktív-aktív módban is konfigurálva van. Az Azure VPN-átjáró konfigurációja alább látható. Vegye figyelembe a VPN-konfiguráció vpn részeként az átjáró BGP-társ IP-címeit is felsorolják - 10.17.11.76 és 10.17.11.77.

![2][2]

A helyszíni útvonalat a tűzfalak a VPN-átjáró elsődleges és másodlagos BGP-társainak hirdetik. Az útvonalhirdetések az alábbiakban láthatók (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>Az S2S VPN aktív-aktív módban történő konfigurálása nem csak a vész-helyreállítási biztonsági mentési hálózati kapcsolat magas rendelkezésre állását biztosítja, hanem nagyobb átviteli feszültséget is biztosít a biztonsági mentési kapcsolathoz. Más szóval az S2S VPN aktív-aktív módban történő konfigurálása ajánlott, mivel több mögöttes alagutas alagutat hoz létre.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Konfigurálás szimmetrikus forgalomhoz

Megjegyeztük, hogy ha egy adott helyszíni útvonalat az ExpressRoute és az S2S VPN-en keresztül is meghirdetnek, az Azure az ExpressRoute elérési útját részesíti előnyben. Ha az Azure-t az S2S VPN-útvonalat részesíti előnyben a párhuzamos ExpressRoute-tal szemben, a VPN-kapcsolaton keresztül konkrétabb útvonalakat (hosszabb előtagot nagyobb alhálózati maszkkal) kell hirdetnie. Célunk itt az, hogy a VPN-kapcsolatokat csak vissza. Így az Azure alapértelmezett útvonal-kiválasztási viselkedése összhangban van a célkitűzésünkkel. 

A mi felelősségünk annak biztosítása, hogy a helyszíni Azure-ból az Azure-ba irányuló forgalom is előnyben részesíti az ExpressRoute elérési útját az S2S VPN-nel szemben. A ce-útválasztók és tűzfalak alapértelmezett helyi preferenciája a helyszíni beállításban a 100. Így az ExpressRoute privát társviszony-létesítéseken keresztül kapott útvonalak helyi preferenciájának konfigurálásával 100-nál nagyobb (mondjuk 150) értéken, beállíthatjuk, hogy az Azure-ba irányuló forgalom az ExpressRoute-áramkört részesítse előnyben állandó állapotban.

Az ExpressRoute-kapcsolat elsődleges kapcsolatát lebontó elsődleges CE-útválasztó BGP-konfigurációja az alábbiakban látható. Figyelje meg, hogy az iBGP-munkameneten keresztül meghirdetett útvonalak helyi preferenciájának értéke 150-re van állítva. Hasonlóképpen biztosítanunk kell, hogy az ExpressRoute-kapcsolat másodlagos kapcsolatát lebontó másodlagos CE-útválasztó helyi preferenciája is 150-re van konfigurálva.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

A helyszíni tűzfalak útválasztási táblája megerősíti (alább látható), hogy az Azure-ba szánt helyszíni forgalom esetében az előnyben részesített elérési út az ExpressRoute-on keresztül van állandó állapotban.

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

A fenti útvonaltáblában a hub és a küllő virtuális hálózat útvonalak --10.17.11.0/25 és 10.17.11.128/26 -- látjuk ExpressRoute-kapcsolat előnyben részesített VPN-kapcsolatokkal szemben. A 192.168.11.0 és 192.168.11.2 IP-k a tűzfal interfész felé CE routerek.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Az útvonalcsere érvényesítése S2S VPN-en keresztül

A cikk korábbi részében ellenőriztük a tűzfalak helyszíni útvonalhirdetését a VPN-átjáró elsődleges és másodlagos BGP-társainak. Emellett erősítsük meg, hogy a tűzfalak a VPN-átjáró elsődleges és másodlagos BGP-társaitól kapott Azure-útvonalakat is meg kell erősíteni.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

Hasonlóképpen ellenőrizzük a helyszíni hálózati útvonal-előtagok az Azure VPN-átjáró által kapott. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

Amint fent látható, a VPN-átjáró a VPN-átjáró elsődleges és másodlagos BGP-társai által fogadott útvonalakat is rendelkezik. Emellett az elsődleges és másodlagos ExpressRoute-kapcsolatokon keresztül fogadott útvonalak (az 12076-tal előkészített AS-elérési úttal rendelkező) útvonalak on-lásd án is láthatóvá vált. A VPN-kapcsolatokon keresztül fogadott útvonalak megerősítéséhez ismernünk kell a kapcsolatok helyszíni BGP-társ IP-címét. A mi beállítás megfontolás alatt, ez 192.168.11.88 és látjuk az útvonalakat kapott belőle.

Ezután ellenőrizze az Azure VPN-átjáró által meghirdetett útvonalakat a helyszíni bgp-társ (192.168.11.88) létesítő tűzfalához.

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


Az útvonalcserék nem jelennek meg, és a kapcsolat sikertelen. Lásd: [Hibaelhárítás: Az Azure-ból helyek közötti VPN-kapcsolat nem tud csatlakozni, és leáll][VPN Troubleshoot] a VPN-kapcsolat hibaelhárításával kapcsolatos segítségért.

## <a name="testing-failover"></a>Feladatátvétel tesztelése

Most, hogy megerősítettük a sikeres útvonalcseréket a VPN-kapcsolaton (vezérlősíkon), úgy van beállítva, hogy a forgalmat (adatsíkot) az ExpressRoute-kapcsolatról a VPN-kapcsolatra váltsuk. 

>[!NOTE] 
>Éles környezetben a feladatátvételi tesztelést az ütemezett hálózati karbantartási munkaablak ban kell elvégezni, mivel az szolgáltatászavaró lehet.
>

A forgalmi kapcsoló előtt kövessük nyomon az aktuális útvonalat a beállításunkban a helyszíni tesztkiszolgálótól a küllővirtuális hálózatban lévő teszt virtuális gépig.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

A beállítás elsődleges és másodlagos ExpressRoute-pont-pont kapcsolatalhálózatai a 192.168.11.16/30 és a 192.168.11.20/30. A fenti nyomkövetési útvonalon a 3. Az MSEE-felület jelenléte megerősíti, hogy az aktuális útvonalunk a várakozásoknak megfelelően az ExpressRoute felett van.

Ahogy arról a [Reset ExpressRoute-kapcsolatlétesítések, használjuk][RST]a következő powershell-parancsokat az ExpressRoute-kapcsolat elsődleges és másodlagos társviszony-létesítésének letiltásához.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

A feladatátvételi váltási idő a BGP-konvergenciaidejénekfügg. A mi beállítás, a feladatátvevő kapcsoló néhány másodperc (kevesebb, mint 10). A váltás után a nyomkövetési útvonal ismétlése a következő útvonalat jeleníti meg:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

A traceroute eredménye megerősíti, hogy az S2S VPN-en keresztüli biztonsági mentési kapcsolat aktív, és szolgáltatásfolytonosságot biztosíthat, ha mind az elsődleges, mind a másodlagos ExpressRoute-kapcsolatok sikertelenek. A feladatátvételi tesztelés befejezéséhez engedélyezze vissza az ExpressRoute-kapcsolatokat, és normalizálja a forgalmat a következő parancskészlet használatával.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Annak ellenőrzéséhez, hogy a forgalom vissza van kapcsolva az ExpressRoute-ra, ismételje meg a traceroute-ot, és győződjön meg arról, hogy az expressroute-beli privát társviszony-létesítésen megy keresztül.

## <a name="next-steps"></a>További lépések

ExpressRoute célja a magas rendelkezésre állású egyetlen hibapont a Microsoft hálózaton belül. Az ExpressRoute-áramkör továbbra is egyetlen földrajzi régióra és egy szolgáltatóra korlátozódik. Az S2S VPN jó vészhelyreállítási passzív biztonsági mentési megoldás lehet egy ExpressRoute-kapcsolathoz. Egy megbízható passzív biztonsági mentési kapcsolat megoldás, rendszeres karbantartása a passzív konfiguráció és a rendszeres érvényesítésa a kapcsolat fontos. Alapvető fontosságú, hogy ne hagyja, hogy a VPN-konfiguráció elavulttá váljon, és rendszeresen (mondjuk negyedévenként) ismételje meg a cikkben leírt érvényesítési és feladatátvételi tesztelési lépéseket a karbantartási időszak során.

A VPN-átjáró metrikáin alapuló figyelési és riasztások engedélyezéséről a [Riasztások beállítása vpn-átjáró metrikákon című témakörben][VPN-alerts]található.

A BGP-konvergencia ExpressRoute-hibát követő meggyorsításához [konfigurálja a BFD-t az ExpressRoute-on keresztül.][BFD]

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "vizsgált topológia"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW konfiguráció"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



