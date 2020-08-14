---
title: Azure Firewall GYIK
description: Azure Firewall kapcsolatos gyakori kérdések. Felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely megvédi az Azure Virtual Network-erőforrásait.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: efb793898da03d2a024b559075a2d55e79b20d65
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208520"
---
# <a name="azure-firewall-faq"></a>Azure Firewall GYIK

## <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Ez egy teljes körűen kiépíthető, magas rendelkezésre állású és korlátlan Felhőbeli skálázhatósági szolgáltatás. Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Milyen képességeket támogat a Azure Firewall?

Azure Firewall szolgáltatásokkal kapcsolatos további tudnivalókért lásd: [Azure Firewall szolgáltatások](features.md).

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Mi a Azure Firewall általános telepítési modellje?

Az Azure Firewall bármely virtuális hálózaton üzembe helyezhető, de az ügyfelek jellemzően központi virtuális hálózaton helyezik üzembe, és más virtuális hálózatokat társítanak hozzá küllős modellben. Ezután megadhatja az alapértelmezett útvonalat a virtuális hálózatok között, hogy erre a központi tűzfal virtuális hálózatra mutasson. A globális VNet-társítás támogatott, de a lehetséges teljesítmény-és késési problémák miatt nem ajánlott a régiók között. A legjobb teljesítmény érdekében régiónként egy tűzfalat helyezzen üzembe.

Ennek a modellnek az előnye, hogy a különböző előfizetésekben található többküllős virtuális hálózatok esetében központilag gyakorolható a vezérlés. A költségmegtakarításhoz nem kell külön tűzfalat telepítenie az egyes VNet. A költségmegtakarítást az ügyfélforgalmi mintázatok alapján kell mérni a kapcsolódó társítási költséghez viszonyítva.

## <a name="how-can-i-install-the-azure-firewall"></a>Hogyan telepíthetem a Azure Firewall?

Azure Firewall a Azure Portal, a PowerShell, a REST API vagy a sablonok használatával állítható be. Lásd [az oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása](tutorial-firewall-deploy-portal.md) című témakört, amely részletes útmutatást nyújt a Azure Portal.

## <a name="what-are-some-azure-firewall-concepts"></a>Mik azok a Azure Firewall fogalmak?

Azure Firewall támogatja a szabályokat és a szabályok gyűjteményeit. A szabálygyűjtemény olyan szabályok halmaza, amelyek ugyanazt a sorrendet és prioritást használják. A szabályok gyűjteményei prioritásuk sorrendjében hajthatók végre. A hálózati szabályok gyűjteményei magasabb prioritással rendelkeznek, mint az alkalmazási szabályok gyűjteményei, és minden szabály leáll.

Háromféle típusú szabálygyűjtemény létezik:

* *Alkalmazási szabályok*: olyan teljes tartománynevek (FQDN-EK) konfigurálása, amelyek egy alhálózatból érhetők el.
* *Hálózati szabályok*: a forrás-, protokoll-, célport-és célcím-szabályokat tartalmazó szabályok konfigurálása.
* *NAT-szabályok*: KONFIGURÁLJA a DNAT szabályokat a bejövő internetkapcsolatok engedélyezéséhez.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Támogatja Azure Firewall a bejövő forgalom szűrését?

Azure Firewall támogatja a bejövő és a kimenő szűrést. A bejövő védelem általában nem HTTP/S protokollokhoz használatos. Például RDP, SSH és FTP protokoll. A legjobb bejövő HTTP/S-védelem érdekében használjon egy webalkalmazási tűzfalat, például az [Azure webalkalmazási tűzfalat (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Mely naplózási és elemzési szolgáltatásokat támogatja a Azure Firewall?

Azure Firewall integrálva van Azure Monitor a tűzfalak megtekintésére és elemzésére. Naplók küldhetők Log Analyticsba, Azure Storage-ba vagy Event Hubsba. Ezek Log Analytics vagy különböző eszközök, például az Excel és a Power BI segítségével elemezhetők. További információ: [oktatóanyag Azure Firewall-naplók figyelése](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hogyan működik a Azure Firewall eltérően a meglévő szolgáltatások, például a NVA a piactéren?

A Azure Firewall egy alapszintű tűzfal-szolgáltatás, amely bizonyos felhasználói forgatókönyvek kezelésére képes. A rendszer azt várta, hogy a külső gyártótól származó NVA és Azure Firewall kombinációja. A jobb együttműködés a legfontosabb prioritás.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Mi a különbség a Application Gateway WAF és a Azure Firewall között?

A webalkalmazási tűzfal (WAF) a Application Gateway szolgáltatása, amely a webalkalmazások központosított védelmét nyújtja a gyakori biztonsági rések és sebezhetőségek ellen. A Azure Firewall bejövő védelmet biztosít a nem HTTP/S protokollokhoz (például RDP, SSH, FTP), a kimenő hálózati szintű védelmet minden porthoz és protokollhoz, valamint az alkalmazás szintű védelmet a kimenő HTTP/S-n.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Mi a különbség a hálózati biztonsági csoportok (NSG) és a Azure Firewall között?

A Azure Firewall szolgáltatás kiegészíti a hálózati biztonsági csoport funkcióit. Együttesen jobb "védelmi mélyreható" hálózati biztonságot nyújtanak. A hálózati biztonsági csoportok elosztott hálózati rétegbeli forgalmi szűrést biztosítanak az egyes előfizetésekben található virtuális hálózatokon belüli erőforrások forgalmának korlátozására. A Azure Firewall egy teljes körűen kiszolgált, központosított hálózati tűzfal, amely hálózati és alkalmazás-szintű védelmet biztosít a különböző előfizetések és virtuális hálózatok között.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>Támogatottak-e a hálózati biztonsági csoportok (NSG) a AzureFirewallSubnet?

Azure Firewall felügyelt szolgáltatás több védelmi réteggel, beleértve a platform védelmét NIC szintű NSG (nem megtekinthető).  Az alhálózati szintű NSG nem szükségesek a AzureFirewallSubnet, és le vannak tiltva a szolgáltatás megszakításának biztosítása érdekében.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hogyan beállítani Azure Firewall a saját szolgáltatás-végpontokkal?

A Pásti-szolgáltatásokhoz való biztonságos hozzáféréshez szolgáltatási végpontokat ajánlunk. Engedélyezheti a szolgáltatási végpontokat az Azure Firewall alhálózatban, és letilthatja őket a csatlakoztatott küllős virtuális hálózatokon. Így mindkét funkció előnyeit élvezheti: a szolgáltatás végpontjának biztonsága és a központi naplózás minden forgalom esetében.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Mi a Azure Firewall díjszabása?

Lásd: [Azure Firewall díjszabása](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Hogyan állíthatom le és indíthatja el Azure Firewall?

Használhatja Azure PowerShell felszabadítási *deallocate* és *lefoglalási* metódusokat.

Például:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> A tűzfalat és a nyilvános IP-címet újra hozzá kell rendelnie az eredeti erőforráscsoporthoz és előfizetéshez.

## <a name="what-are-the-known-service-limits"></a>Milyen korlátozások vonatkoznak az ismert szolgáltatásokra?

Azure Firewall szolgáltatási korlátokat az [Azure-előfizetések és-szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)című témakörben talál.

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Egy hub-alapú virtuális hálózatban Azure Firewall a hálózati forgalmat két küllős virtuális hálózat között?

Igen, a hub virtuális hálózat Azure Firewall használatával irányíthatja és szűrheti a forgalmat két küllős virtuális hálózat között. Az egyes küllős virtuális hálózatok alhálózatai számára egy UDR kell mutatnia, amely a forgatókönyv megfelelő működéséhez az Azure Firewallre mutat.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure Firewall az alhálózatok közötti hálózati adatforgalmat ugyanabban a virtuális hálózatban vagy összekapcsolt virtuális hálózatban?

Igen. Ha azonban a UDR úgy konfigurálja, hogy átirányítsa az alhálózatok közötti forgalmat az azonos VNET, további figyelmet igényel. Ha a VNET-címtartomány a UDR célként megadott előtagjaként van használatban, az egy adott alhálózaton lévő összes forgalmat a Azure Firewall-példányon keresztül is átirányítja egy másik gépre. Ennek elkerüléséhez adjon meg egy útvonalat az alhálózathoz a UDR egy következő ugrás típusú **VNET**. Előfordulhat, hogy az ilyen útvonalak kezelése nehézkes, és a hiba miatt fennáll. A belső hálózati szegmentálás ajánlott módszere olyan hálózati biztonsági csoportok használata, amelyek nem igényelnek UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure Firewall kimenő SNAT a magánhálózati hálózatok között?

A Azure Firewall nem SNAT, ha a cél IP-cím egy [IANA RFC 1918-es](https://tools.ietf.org/html/rfc1918)magánhálózati IP-címtartomány. Ha a szervezete nyilvános IP-címtartományt használ a magánhálózatok számára, Azure Firewall SNATs a forgalmat a AzureFirewallSubnet egyik tűzfal magánhálózati IP-címére. A Azure Firewall konfigurálhatja úgy, hogy **ne** SNAT a nyilvános IP-címtartományt. További információ: [Azure Firewall SNAT magánhálózati IP-címtartományok](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>A hálózati virtuális berendezésre kényszerített bújtatás/láncolás?

A kényszerített bújtatás akkor támogatott, ha új tűzfalat hoz létre. A kényszerített bújtatáshoz nem konfigurálhat meglévő tűzfalat. További információ: [Azure Firewall kényszerített bújtatás](forced-tunneling.md).

Az Azure Firewallnak közvetlen internetkapcsolatra van szüksége. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat **fenntartása érdekében ezt** a 0.0.0.0/0 UDR kell felülbírálnia a **NextHopType** értékkel.

Ha a konfigurációhoz kényszerített bújtatás szükséges egy helyszíni hálózathoz, és meghatározhatja a célként megadott IP-előtagokat az internetes célhelyekhez, akkor ezeket a tartományokat a helyszíni hálózattal is konfigurálhatja a következő ugrásként a AzureFirewallSubnet felhasználó által megadott útvonalán keresztül. Vagy a BGP használatával is meghatározhatja ezeket az útvonalakat.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Vannak olyan tűzfal-erőforráscsoport korlátozásai?

Igen. A tűzfalnak, a VNet és a nyilvános IP-címnek ugyanabban az erőforráscsoporthoz kell tartoznia.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>A bejövő internetes hálózati forgalom DNAT konfigurálásakor szükség van egy megfelelő hálózati szabály konfigurálására is a forgalom engedélyezéséhez?

Nem. A NAT-szabályok implicit módon hozzáadnak egy megfelelő hálózati szabályt a lefordított forgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Az Azure Firewall szabályfeldolgozási logikájával kapcsolatos további információkért tekintse meg az [Azure Firewall szabályfeldolgozási logikájával](rule-processing.md) kapcsolatos cikket.

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hogyan működnek a helyettesítő karakterek egy alkalmazás-szabályban célként megadott FQDN-ben?

A helyettesítő karakterek használata jelenleg csak a teljes tartománynév bal oldalán lehetséges. Például: ***. contoso.com** és ***contoso.com**.

Ha a ***. contoso.com**konfigurálja, akkor a *anyvalue*. contoso.com, de nem contoso.com (a tartomány csúcspontja) használatát teszi lehetővé. Ha engedélyezni szeretné a tartomány csúcspontját, explicit módon konfigurálnia kell célként megadott FQDN-ként.

## <a name="what-does-provisioning-state-failed-mean"></a>Mit jelent a *kiépítési állapot: nem sikerült* ?

Az Azure Firewall minden konfigurációs változás alkalmazásakor megkísérli frissíteni az összes alapul szolgáló háttérpéldányt. Ritka esetekben előfordulhat, hogy az egyik ilyen háttérbeli példány nem tud frissíteni az új konfigurációval, és a frissítési folyamat leáll sikertelen kiépítési állapottal. Az Azure Firewall továbbra is működőképes, de az alkalmazott konfiguráció inkonzisztens állapotba kerülhet, így egyes példányoknál az előző konfiguráció van érvényben, másoknál pedig a frissített szabálykészlet. Ha ez történik, próbálja meg még egyszer frissíteni a konfigurációt, amíg a művelet nem sikerül, és a tűzfal *sikeres* kiépítési állapotban van.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Hogyan kezeli a Azure Firewall a tervezett karbantartást és a nem tervezett hibákat?
A Azure Firewall egy aktív-aktív konfigurációban számos háttér-csomópontból áll.  A tervezett karbantartáshoz a csomópontok zökkenőmentes frissítéséhez a kapcsolatok kiürítése szükséges.  A frissítések az egyes Azure-régiók esetében nem munkaidőn kívüli időpontokban vannak megtervezve, így tovább korlátozható a megszakadás kockázata.  A nem tervezett problémák esetén egy új csomópontot hozunk létre a meghibásodott csomópont cseréjéhez.  Az új csomóponthoz való csatlakozás általában a hiba időpontjától számított 10 másodpercen belül újra létrejön.

## <a name="how-does-connection-draining-work"></a>Hogyan működik a kapcsolatok kiürítése?

Bármilyen tervezett karbantartás esetén a kapcsolatok kiürítési logikája szabályosan frissíti a háttérbeli csomópontokat. A Azure Firewall 90 másodpercet vár a meglévő kapcsolatok bezárásához. Ha szükséges, az ügyfelek automatikusan újra létrehozhatnak kapcsolatot egy másik háttér-csomóponttal.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Létezik a tűzfal neve?

Igen. A tűzfal neveként 50 karakteres korlát van.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Miért van Azure Firewall a/26 alhálózat méretét?

A Azure Firewallnak több virtuálisgép-példányt kell kiépítenie. A/26 címtartomány biztosítja, hogy a tűzfalon elegendő IP-cím álljon rendelkezésre a skálázáshoz.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Meg kell változtatni a tűzfal alhálózatának méretét a szolgáltatás léptékének megfelelően?

Nem. Azure Firewall a/26-nál nagyobb alhálózatra nincs szükség.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Hogyan javíthatom a tűzfal átviteli sebességét?

Azure Firewall kezdeti átviteli kapacitása 2,5 – 3 GB/s, amely 30 GB/s-ig méretezhető. A CPU-használat és az átviteli sebesség alapján automatikusan méretezhető.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Mennyi időt vesz igénybe a Azure Firewall felskálázása?

A Azure Firewall fokozatosan méretezhető, ha az átlagos átviteli sebesség vagy a CPU-felhasználás 60%-on. Az alapértelmezett üzemelő példány maximális átviteli sebessége körülbelül 2,5 – 3 GB/s, és megkezdi a skálázást, amikor eléri az adott szám 60%-át. A vertikális felskálázás öt – hét percet vesz igénybe. 

A teljesítmény tesztelésekor győződjön meg arról, hogy legalább 10 – 15 percig teszteli a tesztet, és új kapcsolatokat indít el, hogy kihasználhassa az újonnan létrehozott tűzfal-csomópontok előnyeit.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Alapértelmezés szerint a Azure Firewall engedélyezi a Active Directory hozzáférését?

Nem. A Azure Firewall alapértelmezés szerint blokkolja Active Directory hozzáférését. A hozzáférés engedélyezéséhez konfigurálja a AzureActiveDirectory szolgáltatás címkéjét. További információ: [Azure Firewall szolgáltatás címkéi](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Ki lehet zárni egy teljes tartománynevet vagy egy IP-címet Azure Firewall fenyegetések felderítésére alapuló szűrés alapján?

Igen, a Azure PowerShell használatával elvégezheti a következőket:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>A TCP ping és hasonló eszközök miért tudnak sikeresen csatlakozni a célként megadott FQDN-hez, még akkor is, ha nincs Azure Firewall szabály, amely engedélyezi ezt a forgalmat?

A TCP-pingelés valójában nem csatlakozik a célként megadott FQDN-hez. Ez azért történik, mert Azure Firewall transzparens proxyja az 80/443-as porton figyeli a kimenő forgalmat. A TCP-ping kapcsolatot létesít a tűzfallal, amely ezután eldobja a csomagot, és naplózza a kapcsolatot. Ez a viselkedés nem befolyásolja a biztonságot. A félreértések elkerülése érdekében azonban vizsgáljuk a viselkedés lehetséges változásait.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Az IP-csoportok által támogatott IP-címek száma korlátozott?

Igen. További információ: Azure- [előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>Áthelyezhetek egy IP-csoportot egy másik erőforráscsoporthoz?

Nem, az IP-csoportok másik erőforráscsoporthoz való áthelyezése jelenleg nem támogatott.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Mi a Azure Firewall TCP üresjárati időkorlátja?

A hálózati tűzfal szabványos viselkedése a TCP-kapcsolatok életben tartásának biztosítása, és ha nincs tevékenység, azonnal lezárhatja őket. Azure Firewall TCP Üresjárati időkorlát négy perc. Ez a beállítás nem konfigurálható. Ha egy inaktivitási időtartam hosszabb az időtúllépési értéknél, nincs garancia arra, hogy a TCP-vagy HTTP-munkamenet továbbra is fennáll. Gyakori eljárás a TCP Keep-Alive használata. Ez a gyakorlat hosszabb ideig tart a kapcsolatok aktív állapotban. További információ: [.net-példák](https://docs.microsoft.com/dotnet/api/system.net.servicepoint.settcpkeepalive?redirectedfrom=MSDN&view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_).

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>Telepíthetek Azure Firewall nyilvános IP-cím nélkül?

Nem, jelenleg a Azure Firewall nyilvános IP-címmel kell telepíteni.

## <a name="where-does-azure-firewall-store-customer-data"></a>Hol tárolja Azure Firewall az ügyféladatokat?

Azure Firewall nem helyezi át és nem tárolja az ügyféladatokat a szolgáltatásban üzembe helyezett régióból.