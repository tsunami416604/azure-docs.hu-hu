---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 7/19/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 1db5f0a62a21d040949c9f4e4c42f80c86e76506
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39164124"
---
### <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

Az Azure tűzfal az egy felügyelt, felhőalapú és hálózati biztonsági szolgáltatás, amely védelmet nyújt az Azure Virtual Network-erőforrások. Egy teljes körűen állapot-nyilvántartó tűzfal--szolgáltatásként beépített magas rendelkezésre állás és a korlátlan felhőbeli méretezhetőséget. Központilag létrehozása, érvényesítése és alkalmazás és a hálózati kapcsolat házirendek jelentkezzen az előfizetések és a virtuális hálózatok között. Az Azure tűzfal jelenleg nyilvános előzetes verzióban érhető el.

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Milyen funkciók támogatottak az Azure-tűzfal nyilvános előzetes kiadás?  

* Állapot-nyilvántartó tűzfal szolgáltatásként
* Beépített magas rendelkezésre állás korlátlan felhőbeli skálázással
* FQDN-szűrés 
* Hálózati forgalom szűrési szabályok
* Kimenő SNAT-támogatás
* Központilag létrehozása, érvényesítése, és jelentkezzen alkalmazás és a hálózati kapcsolat házirendek az Azure-előfizetések és a virtuális hálózatok között
* Teljes integráció az Azure Monitorral a naplózáshoz és az elemzéshez 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Hogyan csatlakozhat az Azure tűzfal nyilvános előzetes verziója?

Az Azure tűzfal jelenleg felügyelt előzetes verziója, amely az Azure tűzfal nyilvános előzetes verzió dokumentációjában leírtaknak megfelelően a Register-AzureRmProviderFeature PowerShell-parancs használatával is csatlakozhat.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Mi az Azure-tűzfal a díjszabása?

Azure tűzfal tartalmaz egy állandó költség + változó költség. Az alábbi árak, és ezek további 50 %-kal a nyilvános előzetes verzióban képest.

* Rögzített díj: $1.25/firewall/hour
* Változó díj: a tűzfal (bejövő vagy kimenő) által feldolgozott $ 0,03/GB.

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Mi az az Azure tűzfal a tipikus telepítési modell?

Bár lehetséges Azure tűzfal bármely virtuális hálózaton üzembe helyezéséhez, a felhasználók a lenne általában üzembe helyezése az Azure-tűzfal központi virtuális hálózaton, más Hub & küllő modellben, a virtuális hálózatok társviszonyba állítása. Ezután a virtuális Társhálózatok tűzfal központi virtuális hálózaton mutasson beállíthatja az alapértelmezett útvonal.

### <a name="how-can-i-install-the-azure-firewall"></a>Hogyan telepíthetem az Azure-tűzfal?

Azure-tűzfalon keresztül az Azure portal, PowerShell, REST API vagy sablonok állíthat be. Lásd: [oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure tűzfal](../articles/firewall/tutorial-firewall-deploy-portal.md) részletes útmutatásait.

### <a name="what-are-some-azure-firewall-concepts"></a>Mik az Azure tűzfal fogalmak?

Az Azure tűzfal szabályok és szabálygyűjtemények támogatja. Egy szabálygyűjteményben egy szabályokat, hogy osztozzanak ugyanazon sorrendjét és prioritását. Szabálygyűjtemények rendszer végrehajtja a prioritásuk szerinti sorrendben, hálózati szabálygyűjtemények alkalmazás szabálygyűjtemények, magasabb prioritású, az összes szabály is leáll.
Szabálygyűjtemény két típusa van:

* Alkalmazás szabályokat: konfigurálhatja a teljes tartománynevek (FQDN), amely egy alhálózatról érhető el. 
* A hálózati szabályok: lehetővé teszi, hogy a forrás címe, protokoll, céloldali port és célcím tartalmazó szabályok konfigurálása. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure-tűzfal támogatja a bejövő forgalom szűrése?

Azure tűzfal nyilvános előzetes verziója támogatja a csak kimenő szűrés. Bejövő védelmét nem HTTP vagy Https protokollt (például: RDP, SSH-FTP) feltételesen tervezünk Azure tűzfal általánosan elérhető  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Milyen naplózási analytics az Azure-tűzfal által támogatott?

Azure-tűzfalon megtekintésére és elemzésére tűzfalnaplók integrálva van az Azure Monitor szolgáltatással. Naplók a Log Analytics, az Azure Storage vagy az Eseményközpont lehet küldeni. A Log Analytics vagy a különböző eszközök, például az Excel és a Power BI elemezhetők. Lásd: [oktatóanyag: a figyelő Azure tűzfal-naplókon](../articles/firewall/tutorial-diagnostics.md) további részletekért.

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>Hogyan működik a Azure tűzfal viszonyított meglévő például nva-k a Marketplace-en?

Az Azure tűzfal egyszerű tűzfal szolgáltatása kezelheti az egyes felhasználói forgatókönyv. Azt várhatóan 3. fél nva-k és az Azure-tűzfal rendelkező ügyfelek számára, és több együtt jobb lehetőségeken partnereink dolgozik. 
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Mi a különbség az Application Gateway WAF és az Azure-tűzfal?

A webalkalmazási tűzfal (WAF) az Application Gateway, amelyek a webalkalmazásoknak a gyakori támadások és biztonsági rések bejövő központi védelmet nyújt olyan szolgáltatása. Azure tűzfal az összes portok és protokollok a kimenő hálózati szintű védelmet és a kimenő HTTP/s alkalmazás szintű védelmet biztosít Bejövő védelmét nem HTTP/Https protokollok (például RDP, SSH-FTP) feltételesen tervezett Azure-tűzfal általánosan elérhető

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>Mi a különbség a hálózati biztonsági csoportok (NSG) és a tűzfal az Azure között?

A tűzfal az Azure szolgáltatás egészíti ki a meglévő hálózati biztonsági csoport funkciói, és együtt nagyobb biztonságot jellegű defense hálózati. Az NSG-k adja meg a hálózati réteg elosztott forgalom szűrése belül minden előfizetésben található virtuális hálózatok erőforrásokra irányuló forgalom korlátozásához.  Az Azure tűzfal egy teljes körűen állapotalapú, a központi hálózati tűzfal-szolgáltatásként, a hálózati és az szintű védelmet biztosít a különböző előfizetésekben találhatóak, és a virtuális hálózatok (Vnetek) között. 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Hogyan állíthatok be Azure-tűzfal saját szolgáltatásvégpontokkal?

Biztonságos hozzáférés PaaS-szolgáltatások javasoljuk, hogy a Szolgáltatásvégpontok. Az Azure tűzfal-ügyfelek az Azure-tűzfal alhálózat szolgáltatásvégpont engedélyezése és letiltása a csatlakoztatott küllő virtuális hálózatok a tárolásai mindkét funkciókat – szolgáltatás endpoint securityhez és a központi naplózás minden forgalom a választhat.

### <a name="what-are-the-known-service-limits"></a>Mik az ismert szolgáltatási korlátai?

* Azure-tűzfalon egy 1000 TB/tűzfal/hó enyhe korlátjának rendelkezik. 
* Azure-tűzfalon, amely futtatja a központi virtuális hálózaton van a virtuális hálózatok közötti társviszony-létesítési korlátozások vonatkoznak: 50 küllő virtuális hálózatok a maximális.  
* Az Azure tűzfal nem képes együttműködni az globális társhálózatok létesítésének, így az ügyfelek kell legalább egy központi telepítési a tűzfal régiónként.