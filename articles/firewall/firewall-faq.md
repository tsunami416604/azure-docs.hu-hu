---
title: Gyakori kérdések az Azure tűzfalról
description: Gyakori kérdések az Azure tűzfalhoz. Felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely védi az Azure virtuális hálózati erőforrásait.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 5ddbb58837fbda0f14a07186d5a3053055954454
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677454"
---
# <a name="azure-firewall-faq"></a>Gyakori kérdések az Azure tűzfalról

## <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Ez egy teljesen állapotalapú tűzfal-as-a-szolgáltatás beépített magas rendelkezésre állás és korlátlan felhő méretezhetőség. Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Milyen képességeket támogat az Azure Firewall?

* Állapotalapú tűzfal szolgáltatásként
* Beépített, magas rendelkezésre állás korlátlan felhőméretezhetőséggel
* FQDN-szűrés
* FQDN-címkék
* Hálózati forgalomra vonatkozó szűrési szabályok
* Kimenő SNAT-támogatás
* Bejövő DNAT-támogatás
* Alkalmazás- és hálózati kapcsolatszabályzatok központi létrehozása, kényszerítése és naplózása az Azure-előfizetések és virtuális hálózatok között
* Teljes mértékben integrálva az Azure Monitorba a naplózáshoz és elemzéshez

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Mi az Azure Firewall tipikus telepítési modellje?

Az Azure Firewall bármely virtuális hálózaton üzembe helyezhető, de az ügyfelek általában központi virtuális hálózaton telepítheti, és más virtuális hálózatokat létesítenek egy küllős modellben. Ezután beállíthatja, hogy a társviszony-létesített virtuális hálózatok alapértelmezett útvonala erre a központi tűzfalvirtuális hálózatra mutasson. Globális virtuális kapcsolatlétesítés támogatott, de nem ajánlott, mert a potenciális teljesítmény és a késés problémák régiók között. A legjobb teljesítmény érdekében régiónként egy tűzfalat telepítsen.

Ennek a modellnek az az előnye, hogy központilag több küllős VNET-t irányít különböző előfizetések között. Költségmegtakarítás is történt, mivel nem kell külön-külön telepítenie a tűzfalat az egyes virtuális hálózatokban. A költségmegtakarítást a társtárs-társviszony-létesítési költségekhez képest kell mérni az ügyfél forgalmi mintái alapján.

## <a name="how-can-i-install-the-azure-firewall"></a>Hogyan telepíthetem az Azure tűzfalat?

Az Azure Firewall az Azure Portalon, a PowerShell, a REST API-n vagy a sablonok használatával állítható be. Tekintse meg [az oktatóanyagot: Az Azure Firewall telepítése és konfigurálása az Azure](tutorial-firewall-deploy-portal.md) Portal használatával lépésről lépésre című témakört.

## <a name="what-are-some-azure-firewall-concepts"></a>Mik az Azure Firewall-fogalmak?

Az Azure Firewall támogatja a szabályokat és a szabálygyűjteményeket. A szabálygyűjtemény olyan szabályok készlete, amelyek azonos sorrenddel és prioritással rendelkeznek. A szabálybeszedések végrehajtása a prioritásuk sorrendjében történik. A hálózati szabálygyűjtemények magasabb prioritásúak, mint az alkalmazásszabály-gyűjtemények, és minden szabály megszűnik.

A szabálygyűjtemények három típusa van:

* *Alkalmazásszabályok*: Konfigurálja az alhálózatról elérhető, teljesen minősített tartományneveket (FQDN).Configure fully qualified domain names (FQDNs that can be accessd from a subnet.
* *Hálózati szabályok*: Konfigurálja a forráscímeket, protokollokat, célportokat és célcímeket tartalmazó szabályokat.
* *NAT-szabályok*: A DNST-szabályok konfigurálása a bejövő internetkapcsolatok engedélyezéséhez.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Támogatja az Azure Firewall a bejövő forgalom szűrését?

Az Azure Firewall támogatja a bejövő és kimenő szűrést. A bejövő védelem általában nem HTTP/S protokollokhoz használatos. Például RDP, SSH és FTP protokollok. A legjobb bejövő HTTP/S védelem, használjon egy webalkalmazás tűzfal, például [az Azure Web Application Firewall (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Mely naplózási és elemzési szolgáltatásokat támogatja az Azure tűzfal?

Az Azure Firewall integrálva van az Azure Monitorlal a tűzfalnaplók megtekintéséhez és elemzéséhez. A naplók elküldhetők a Log Analytics, az Azure Storage vagy az Event Hubs. Elemezhetők a Log Analytics szolgáltatásban vagy különböző eszközökkel, például az Excellel és a Power BI-val. További információ: [Oktatóanyag: Az Azure tűzfal naplóinak figyelése.](tutorial-diagnostics.md)

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Hogyan működik az Azure Firewall másképpen, mint a meglévő szolgáltatások, például a piactéren nva-k?

Az Azure Firewall egy alapvető tűzfalszolgáltatás, amely képes kezelni bizonyos ügyfél-forgatókönyvek. Várható, hogy lesz egy mix a külső nva-k és az Azure Tűzfal. A jobb együttműködés alapvető prioritás.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Mi a különbség az Application Gateway WAF és az Azure Firewall között?

A webalkalmazás-tűzfal (WAF) az Application Gateway egyik szolgáltatása, amely központosított bejövő védelmet nyújt a webalkalmazásoknak a gyakori biztonsági rések és biztonsági rések elleni védelemmel szemben. Az Azure Firewall bejövő védelmet biztosít a nem HTTP/S protokollok (például RDP, SSH, FTP), kimenő hálózati szintű védelem az összes port és protokoll, és alkalmazásszintű védelem kimenő HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Mi a különbség a hálózati biztonsági csoportok (NSG-k) és az Azure Firewall között?

Az Azure Firewall szolgáltatás kiegészíti a hálózati biztonsági csoport funkcióit. Együtt jobb "mélyreható védelmi" hálózati biztonságot nyújtanak. A hálózati biztonsági csoportok elosztott hálózati rétegforgalom-szűrést biztosítanak, hogy az egyes előfizetésekben a virtuális hálózatokon belüli erőforrásokra irányuló forgalom korlátozása. Az Azure Firewall egy teljesen állapotalapú, központi hálózati tűzfal szolgáltatásként, amely hálózati és alkalmazásszintű védelmet biztosít a különböző előfizetések és virtuális hálózatok között.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>A hálózati biztonsági csoportok (NSG-k) támogatottak az Azure Tűzfal alhálózatban?

Az Azure Firewall egy felügyelt szolgáltatás több védelmi réteget, beleértve a platformvédelem hálózati hálózati szintű NSG-k (nem látható).  Alhálózati szintű NSG-k nem szükségesek az Azure Tűzfal alhálózaton, és le vannak tiltva, hogy ne szakadjon meg a szolgáltatás.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hogyan állíthatom be az Azure Firewall szolgáltatást a szolgáltatásvégpontokkal?

A PaaS-szolgáltatásokhoz való biztonságos hozzáférés érdekében a szolgáltatásvégpontok használatát javasoljuk. Választhat, hogy engedélyezi a szolgáltatásvégpontok az Azure Firewall alhálózatban, és tiltsa le őket a csatlakoztatott küllővirtuális hálózatokon. Így mindkét szolgáltatás előnyeit élvezheti: a szolgáltatásvégpont-biztonság és a központi naplózás az összes forgalomhoz.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Mi az Azure Firewall díjszabása?

Lásd: [Azure firewall díjszabása](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Hogyan állíthatom le és indíthatom el az Azure Firewall-t?

Használhatja az Azure PowerShell *deallocate* és *lefoglalási* módszerek.

Példa:

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
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Újra ki kell osztania a tűzfalat és a nyilvános IP-címet az eredeti erőforráscsoporthoz és előfizetéshez.

## <a name="what-are-the-known-service-limits"></a>Melyek az ismert szolgáltatási korlátok?

Az Azure Firewall szolgáltatáskorlátairól az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Az Azure Firewall egy központi virtuális hálózat előre, és szűri a hálózati forgalmat két küllős virtuális hálózatok?

Igen, az Azure Firewall segítségével egy központi virtuális hálózat útvonal-és-szűrési forgalom két küllővirtuális hálózat közötti forgalmat. A küllővirtuális hálózatok minden egyes küllővirtuális hálózatának rendelkeznie kell egy UDR-rel, amely az Azure tűzfalra mutat alapértelmezett átjáróként ehhez a forgatókönyvhöz a megfelelő működéshez.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Továbbíthatja és szűrheti az Azure Firewall a hálózati forgalmat az ugyanazon virtuális hálózatvagy társviszonyt létesített virtuális hálózatok alhálózatai között?

Igen. Azonban az UdRs konfigurálása az ugyanazon virtuális hálózat alhálózatai közötti forgalom átirányításához további figyelmet igényel. A virtuális hálózat címtartományának használata az UDR célelőtagjaként elegendő, ez is irányítja az összes forgalmat egy gépről egy másik gépre ugyanabban az alhálózaton keresztül az Azure Firewall-példányon keresztül. Ennek elkerülése érdekében adjon meg egy útvonalat az alhálózathoz az UDR-ben a következő ugrástípusú **virtuális hálózattal.** Ezeknek az útvonalaknak a kezelése nehézkes lehet, és hibákra hajlamos lehet. A belső hálózati szegmentálás ajánlott módszere a hálózati biztonsági csoportok használata, amelyekhez nincs szükség udrs-re.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Az Azure Firewall kimenő SNAT a magánhálózatok között?

Az Azure Firewall nem snat, ha a cél IP-cím egy privát IP-tartomány [iANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Ha a szervezet nyilvános IP-címtartományt használ a magánhálózatokhoz, az Azure Firewall SNAT-ok forgalmat bonyolítanak le az AzureFirewallSubnet tűzfal egyik privát IP-címére. Beállíthatja, hogy az Azure Firewall **ne** snat a nyilvános IP-címtartományban. További információ: [Azure Firewall SNAT private IP address ranges](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Támogatott a kényszerített bújtatás/láncolás egy hálózati virtuális berendezéshez?

A kényszerített bújtatás támogatott. További információ: [Azure Firewall kényszerített bújtatás (előzetes verzió)](forced-tunneling.md). 

Az Azure tűzfalnak közvetlen internetkapcsolattal kell rendelkeznie. Ha az AzureFirewallSubnet megtanulja az alapértelmezett útvonalat a helyszíni hálózatra a BGP-n keresztül, felül kell bírnia ezt egy 0.0.0.0/0 UDR-rel, amelyen a **NextHopType** érték **internetként** van beállítva a közvetlen internetkapcsolat fenntartásához.

Ha a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózatra, és meghatározhatja az internetes célhelyek cél IP-előtagait, konfigurálhatja ezeket a tartományokat a helyszíni hálózattal a következő ugrásként az AzureFirewallSubnet felhasználó által definiált útvonalon keresztül. Vagy használhatja a BGP-t ezeknek az útvonalaknak a definiálására.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Vannak tűzfalerőforrás-csoport korlátozások?

Igen. A tűzfalnak, a virtuális hálózatnak és a nyilvános IP-címnek ugyanabban az erőforráscsoportban kell lennie.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Amikor a DNST-t a bejövő internetes hálózati forgalomhoz konfigurálja, konfigurálnom kell egy megfelelő hálózati szabályt is a forgalom engedélyezéséhez?

Nem. A NAT-szabályok implicit módon hozzáadnak egy megfelelő hálózati szabályt a lefordított forgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Az Azure Firewall szabályfeldolgozási logikájával kapcsolatos további információkért tekintse meg az [Azure Firewall szabályfeldolgozási logikájával](rule-processing.md) kapcsolatos cikket.

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Hogyan működnek a helyettesítő karakterek egy alkalmazásszabályban a teljes tartománynna?

Ha a ***.contoso.com**kapcsolót konfigurálja, akkor *az anyvalue*.contoso.com értéket engedélyezi, de a contoso.com (a tartomány csúcsa). Ha engedélyezni szeretné a tartomány csúcspontját, explicit módon kell konfigurálnia azt célteljes tartománynévként.

## <a name="what-does-provisioning-state-failed-mean"></a>Mit jelent *a kiépítés állapota: Sikertelen* jelent?

Konfigurációs módosítás alkalmazásakor az Azure Firewall megpróbálja frissíteni az összes mögöttes háttérpéldányok. Ritka esetekben előfordulhat, hogy az ilyen háttérpéldányok egyike nem frissül az új konfigurációval, és a frissítési folyamat sikertelen kiépítési állapottal leáll. Az Azure tűzfal továbbra is működőképes, de az alkalmazott konfiguráció lehet inkonzisztens állapotban, ahol egyes példányok rendelkeznek az előző konfiguráció, ahol mások a frissített szabálykészlettel rendelkezik. Ha ez történik, próbálja meg frissíteni a konfigurációt még egyszer, amíg a művelet sikeres, és a tűzfal *sikeres* kiépítési állapotban van.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Hogyan kezeli az Azure Firewall a tervezett karbantartást és a nem tervezett hibákat?
Az Azure Firewall több háttércsomópontból áll egy aktív-aktív konfigurációban.  Minden tervezett karbantartás esetén kapcsolatkiürítési logikával rendelkezünk a csomópontok szabályos frissítéséhez.  A frissítéseket az Azure-régiók minden egyes régiójában munkaidőn kívüli órákban tervezik, hogy tovább csökkentsék a fennakadáskockázatát.  Nem tervezett problémák esetén egy új csomópontot hozunk végre a meghibásodott csomópont lecserélésére.  Az új csomóponthoz való kapcsolódás általában a hiba időpontjától számított 10 másodpercen belül helyreáll.

## <a name="how-does-connection-draining-work"></a>Hogyan működik a kapcsolat kiürítése?

Minden tervezett karbantartás esetén a kapcsolat kiürítése logika ikent frissíti háttércsomópontok. Az Azure Firewall 90 másodpercet vár a meglévő kapcsolatok bezárására. Szükség esetén az ügyfelek automatikusan újra létrehozhatják a kapcsolatot egy másik háttércsomóponttal.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Van karakterkorlát a tűzfalnevéhez?

Igen. A tűzfalnév50 karakteres korlátot jelent.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Miért kell az Azure Tűzfalnak /26 alhálózati méretet használnia?

Az Azure Firewall-nek több virtuálisgép-példányt kell kiépítenie a méretezés során. A /26 címterület biztosítja, hogy a tűzfal elegendő IP-címmel rendelkezzen a méretezéshez.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>A szolgáltatás méretezésekor módosítani kell a tűzfal alhálózatának méretét?

Nem. Az Azure Firewall nem kell egy alhálózat nagyobb, mint /26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Hogyan növelhetem a tűzfal átviteli átmenő számát?

Az Azure Firewall kezdeti átviteli kapacitása 2,5–3 Gb/s, és 30 Gb/s-ra skálázódik. A processzorhasználat és az átviteli hatás alapján horizontálisfelskálázás. Lépjen kapcsolatba az ügyfélszolgálattal a tűzfal átviteli kapacitásának növeléséhez.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Mennyi ideig tart az Azure Firewall horizontális felskálázása?

Öt-hét percet vesz igénybe az Azure Firewall horizontális felskálázása. Lépjen kapcsolatba az ügyfélszolgálattal a tűzfal kezdeti átviteli kapacitásának növeléséhez, ha gyorsabb automatikus skálázást igénylő adatlöketeket igényel.

A tűzfal automatikus skálázásának tesztelésekénél a következő szempontokat kell figyelembe venni:

- Az egy TCP-folyamat teljesítménye 1,4 Gb/s. Így egy teljesítménytesztnek több TCP-folyamatot kell létrehoznia.
- A teljesítményeszközöknek folyamatosan új kapcsolatokat kell létesíteniük ahhoz, hogy csatlakozhassanak a kibővített háttér-tűzfal példányaihoz. Ha a teszt kapcsolatot hoz létre egyszer az elején, majd ezek csak a kezdeti háttérpéldányokhoz kapcsolódnak. Annak ellenére, hogy a tűzfal felskálázódik, nem fog látni nagyobb teljesítményt, mert a kapcsolatok a kezdeti példányokhoz vannak társítva.


## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Lehetővé teszi az Azure tűzfal alapértelmezés szerint engedélyezi az Active Directory elérését?

Nem. Az Azure Firewall alapértelmezés szerint blokkolja az Active Directory-hozzáférést. A hozzáférés engedélyezéséhez konfigurálja az AzureActiveDirectory szolgáltatáscímkét. További információ: [Azure Firewall service tags](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Kizárhatok egy teljes tartománynt vagy egy IP-címet az Azure Firewall Threat Intelligence alapú szűrésből?

Igen, az Azure PowerShell segítségével teheti meg:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Miért tud egy TCP ping és hasonló eszközök sikeresen csatlakozni a cél fqdn akkor is, ha nincs szabály az Azure Firewall lehetővé teszi, hogy a forgalom?

A TCP-ping valójában nem csatlakozik a cél teljes tartománynnna. Ez azért történik, mert az Azure Firewall transzparens proxy figyeli a 80/443-as porton a kimenő forgalom. A TCP ping kapcsolatot létesít a tűzfallal, amely ezután eldobja a csomagot, és naplózza a kapcsolatot. Ennek a viselkedésnek nincs biztonsági hatása. Azonban, hogy elkerüljék a zavart vagyunk vizsgálja lehetséges változások ezt a viselkedést.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Korlátozva vannak az IP-csoportok által támogatott IP-címek számára vonatkozóan?

Igen. További információ: [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)