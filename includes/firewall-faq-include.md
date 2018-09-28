---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 9/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 1242cf80ffd283471eb507612949a81d4b3fd594
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47401644"
---
### <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Egy teljes körűen állapot-nyilvántartó tűzfal--szolgáltatásként beépített magas rendelkezésre állás és a korlátlan felhőbeli méretezhetőséget. Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure tűzfal milyen funkciók támogatottak?  

* Állapotalapú tűzfal mint szolgáltatás
* Beépített magas rendelkezésre állás korlátlan felhőbeli skálázással
* FQDN-szűrés
* FQDN-címkék
* Hálózati forgalomra vonatkozó szűrési szabályok
* Kimenő SNAT-támogatás
* Bejövő DNAT-támogatás
* Központilag létrehozása, érvényesítése, és jelentkezzen alkalmazás és a hálózati kapcsolat házirendek az Azure-előfizetések és a virtuális hálózatok között
* Teljes integráció az Azure Monitorral a naplózáshoz és az elemzéshez 

### <a name="what-is-the-pricing-for-azure-firewall"></a>Mi az Azure-tűzfal a díjszabása?

Azure tűzfal tartalmaz egy állandó költség + változó költség:

* Rögzített díj: $1.25/firewall/hour
* Változó díj: a tűzfal (bejövő vagy kimenő) által feldolgozott $ 0,03/GB

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Mi az az Azure tűzfal a tipikus telepítési modell?

Azure tűzfal telepíthet minden olyan virtuális hálózaton, de az ügyfelek általában üzembe helyezni a központi virtuális hálózaton, és a Központ-küllő modellek hozzá más virtuális hálózatokat. Ezután beállíthatja az alapértelmezett útvonal, a társviszonyban álló virtuális hálózatba, mutasson a tűzfal központi virtuális hálózaton.

### <a name="how-can-i-install-the-azure-firewall"></a>Hogyan telepíthetem az Azure-tűzfal?

Az Azure portal, PowerShell, REST API használatával, vagy sablonok használatával is beállíthatja az Azure-tűzfal. Lásd: [oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure tűzfal](../articles/firewall/tutorial-firewall-deploy-portal.md) részletes útmutatásait.

### <a name="what-are-some-azure-firewall-concepts"></a>Mik azok az Azure-tűzfal fogalmak?

Az Azure tűzfal szabályok és szabálygyűjtemények támogatja. Egy szabály gyűjteménye, amely egyforma sorrendben, valamint elsőbbségi szabályok. Szabálygyűjtemények prioritásuk szerinti sorrendben vannak végrehajtva. Hálózati szabálygyűjtemények magasabb prioritású, mint az alkalmazás szabálygyűjtemények, és az összes szabály is leáll.

Szabálygyűjtemény két típusa van:

* *Alkalmazás szabályok*:, amelyekkel konfigurálhatja a teljes tartománynevek (FQDN), amely egy alhálózatról érhető el. 
* *A hálózati szabályok*:, amelyekkel konfigurálhatja a szabályt, amely tartalmazza a forrás-címek, protokollok, célportok és célcímek. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure-tűzfal támogatja a bejövő forgalom szűrése?

Azure-tűzfalon támogatja a bejövő és kimenő szerinti szűrés konfigurálható. Bejövő protection egy nem HTTP/Https protokollokhoz. Például az RDP, SSH és FTP protokollt.
 
### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Naplózás és elemzési szolgáltatásokat az Azure-tűzfal által támogatott?

Azure-tűzfalon megtekintésére és elemzésére tűzfalnaplók integrálva van az Azure Monitor szolgáltatással. Naplók a Log Analytics, az Azure Storage vagy az Event Hubs lehet küldeni. A Log Analytics vagy a különböző eszközök, például az Excel és a Power BI elemezhetők. További információkért lásd: [oktatóanyag: a figyelő Azure tűzfal-naplókon](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure-tűzfal működése eltér a meglévő szolgáltatások, például a piactéren az nva-k

Az Azure tűzfal egyszerű tűzfal szolgáltatása kezelheti az egyes felhasználói forgatókönyv. Valószínű, hogy külső nva-k és az Azure-tűzfal lesz. Jobban működnek együtt az alapvető prioritást.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Mi a különbség az Application Gateway WAF és az Azure-tűzfal?

A webalkalmazási tűzfal (WAF) az Application Gateway, amelyek a webalkalmazásoknak a gyakori támadások és biztonsági rések bejövő központi védelmet nyújt olyan szolgáltatása. Azure-tűzfalon biztosít minden portok és protokollok a kimenő hálózati szintű védelmet és alkalmazásszintű védelemhez tartozó kimenő HTTP/s Bejövő védelmét nem HTTP/Https protokollok (például RDP, SSH-FTP) feltételesen tervezett Azure-tűzfal általánosan elérhető

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Mi a különbség a hálózati biztonsági csoportok (NSG-k) és a tűzfal az Azure között?

Az Azure-tűzfal szolgáltatás egészíti ki a hálózati biztonsági csoport funkciót. Együtt biztosítanak a nagyobb "defense jellegű" hálózati biztonság. Hálózati biztonsági csoportok adja meg a hálózati réteg elosztott forgalom szűrése belül minden előfizetésben található virtuális hálózatok erőforrásokra irányuló forgalom korlátozásához. Az Azure tűzfal egy teljes mértékben állapotalapú, a központi hálózati tűzfal-szolgáltatásként, amely hálózati és az alkalmazás-szintű védelmet biztosít a különböző előfizetésekben találhatóak, és a virtuális hálózatok között. 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hogyan állíthatok be Azure-tűzfal saját szolgáltatásvégpontokkal?

Biztonságos hozzáférés PaaS-szolgáltatások javasoljuk, hogy a Szolgáltatásvégpontok. Ha szeretné, engedélyezze a szolgáltatásvégpontokat az Azure-tűzfal alhálózat, és tiltsa le azokat a csatlakoztatott küllő virtuális hálózatokon. Ezzel a módszerrel, előnyös funkciók--service endpoint securityhez és a központi naplózás minden forgalom.

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Hogyan állítsa le és indítsa el az Azure-tűzfal?

Azure PowerShell használatával *felszabadítása* és *lefoglalni* módszereket.

Példa:

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Újra hozzá kell rendelnie egy tűzfal- és nyilvános IP-cím, az eredeti erőforráscsoportban és előfizetésben.

### <a name="what-are-the-known-service-limits"></a>Mik az ismert szolgáltatási korlátai?

* Azure tűzfal esetében a helyreállítható legfeljebb 1000 TB / hó tűzfal /. 
* Egy példányát a központi virtuális hálózaton futó Azure tűzfal a virtuális hálózati korlátozások, mely legfeljebb 50 küllő virtuális hálózatok közötti társviszony rendelkezik.  
* Azure-tűzfalon nem működik globális társviszony-létesítéshez, ezért rendelkeznie kell legalább egy tűzfal központi telepítési régiónként.
* Az Azure tűzfal 10 k alkalmazás és 10 k hálózati szabályok támogatja.

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>A központi virtuális hálózaton előre Azure tűzfal is, és küllő virtuális hálózatok közötti hálózati forgalom szűrése?

Igen, az Azure tűzfal hub virtuális hálózatban két küllő virtuális hálózat közötti útvonal és a szűrő forgalom. Minden küllő virtuális hálózatok alhálózatokat megfelelően működjön az Azure-tűzfal mutató ebben a forgatókönyvben az alapértelmezett átjáróként UDR kell rendelkeznie.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network"></a>Azure-tűzfal előre is, és az azonos virtuális hálózatban lévő alhálózatok közötti hálózati forgalom szűrése?

Az azonos virtuális hálózatba, vagy közvetlenül két társviszonyban álló virtuális hálózatban lévő alhálózatok közötti forgalmat irányítja közvetlenül, még akkor is, ha az udr-t mutat az Azure-tűzfal alapértelmezett átjáróként. A belső hálózati szegmentálást ajánlott módszer, hogy a hálózati biztonsági csoportok. Ebben a forgatókönyvben a tűzfalon az alhálózat alhálózati forgalom küldésére, udr-t a cél alhálózat hálózati előtagot mindkét alhálózat a explicit módon kell tartalmaznia.
