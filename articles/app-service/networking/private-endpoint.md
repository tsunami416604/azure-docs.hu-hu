---
title: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
description: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/25/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 2151b72e647726a075e11bbaea968ccf30cdc78a
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196396"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Privát végpontok használata az Azure Web App-hoz (előzetes verzió)

> [!Note]
> Az előzetes verzió frissítésével megjelent a kiszűrése Protection szolgáltatás.
> Az előzetes verzió az USA keleti régiójában és az USA 2. nyugati régiójában érhető el az összes PremiumV2 Windows-és Linux-Web Apps, valamint a rugalmas prémium szintű funkciókhoz. 

Az Azure-webalkalmazás privát végpontjának használatával engedélyezheti a magánhálózaton található ügyfelek számára, hogy biztonságosan hozzáférjenek az alkalmazáshoz privát kapcsolaton keresztül. A privát végpont az Azure VNet IP-címét használja. A magánhálózaton lévő ügyfél és a webalkalmazás közötti hálózati forgalom a VNet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül történik, ami kiküszöböli a nyilvános internetről való kitettséget.

A webalkalmazás privát végpontjának használata lehetővé teszi a következőket:

- A webalkalmazás biztonságossá tételéhez konfigurálja a privát végpontot a nyilvános expozíció kiküszöbölése érdekében.
- Biztonságosan csatlakozhat a webalkalmazáshoz olyan helyszíni hálózatokból, amelyek VPN-vagy ExpressRoute-alapú privát kapcsolattal csatlakoznak a VNet.
- Kerülje a VNet származó adatok kiszűrése. 

Ha csak biztonságos kapcsolatra van szüksége a VNet és a webalkalmazás között, a legegyszerűbb megoldás a szolgáltatási végpont. Ha a webalkalmazást egy Azure-átjárón keresztül is el kell érnie, egy regionálisan felhasználható VNet vagy egy globálisan felhasználható VNet, a magánhálózati végpont a megoldás.  

További információ: szolgáltatás- [végpontok][serviceendpoint].

## <a name="conceptual-overview"></a>Fogalmi áttekintés

A privát végpont egy speciális hálózati adapter (NIC) az Azure-webalkalmazáshoz a Virtual Network (VNet) egyik alhálózatában.
Amikor létrehoz egy privát végpontot a webalkalmazáshoz, biztonságos kapcsolatot biztosít a magánhálózaton és a webalkalmazásban található ügyfelek között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához.
A magánhálózati végpont és a webalkalmazás közötti kapcsolat biztonságos [privát hivatkozást][privatelink]használ. A magánhálózati végpont csak a webalkalmazásba érkező bejövő folyamatokhoz használható. A kimenő folyamatok nem fogják használni ezt a magánhálózati végpontot, de a kimenő folyamatokat egy másik alhálózaton is beinjektálhatja a [VNet-integrációs szolgáltatáson][vnetintegrationfeature]keresztül.

Az alhálózatot, amelyhez a privát végpontot csatlakoztatja, más erőforrásokkal is rendelkezhet, nincs szükség dedikált üres alhálózatra.
A privát végpontot a webalkalmazástól eltérő régióban is üzembe helyezheti. 

> [!Note]
>A VNet-integrációs szolgáltatás nem használhatja ugyanazt az alhálózatot, mint a privát végpont, ez a VNet-integrációs szolgáltatás korlátozása.

Biztonsági szempontból:

- Ha engedélyezi a privát végpontokat a webalkalmazáshoz, az összes nyilvános hozzáférés le van tiltva.
- Több privát végpontot is engedélyezhet más virtuális hálózatok és alhálózatokban, beleértve a más régiókban található virtuális hálózatok is.
- A magánhálózati végpont hálózati adapterének IP-címének dinamikusnak kell lennie, de addig marad, amíg nem törli a privát végpontot.
- A privát végpont hálózati adaptere nem rendelkezhet társított NSG.
- A privát végpontot futtató alhálózathoz NSG társítható, de le kell tiltania a hálózati házirendek kényszerítését a privát végponton: a [hálózati házirendek letiltása privát végpontok esetén][disablesecuritype]című témakört. Ennek eredményeképpen egyetlen NSG sem tudja szűrni a privát végponthoz való hozzáférést.
- Ha engedélyezi a privát végpontot a webalkalmazáshoz, a rendszer nem értékeli ki a webalkalmazás [hozzáférési korlátozásait][accessrestrictions] .
- Az adatok kiszűrése kockázatát a VNet távolíthatja el az összes olyan NSG-szabály eltávolításával, ahol a cél az Internet vagy az Azure-szolgáltatások címkéje. Ha privát végpontot telepít egy webalkalmazáshoz, akkor csak a privát végponton keresztül érheti el ezt az adott webalkalmazást. Ha van egy másik webalkalmazása, egy másik dedikált privát végpontot kell telepítenie ehhez a webalkalmazáshoz.

A webalkalmazás webes HTTP-naplóiban megtalálja az ügyfél forrásának IP-címét. Ez a TCP proxy protokoll használatával valósul meg, amely a-ügyfél IP-tulajdonságát továbbítja a webalkalmazásnak. További információ: a [kapcsolatok adatainak beszerzése a TCP proxy v2 használatával][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![A Web App Private Endpoint globális áttekintése](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Mivel ez a funkció előzetes verzióban érhető el, nem változtatjuk meg a DNS-bejegyzést az előzetes verzióban. A DNS-bejegyzést a saját DNS-kiszolgálójában vagy saját maga Azure DNS saját zónájában kell kezelnie.
Ha egyéni DNS-nevet kell használnia, hozzá kell adnia az egyéni nevet a webalkalmazásban. Az előzetes verzióban az egyéni nevet a nyilvános DNS-feloldást használó bármely egyéni névnek hasonlóan kell érvényesíteni. További információ: [Egyéni DNS-ellenőrzés][dnsvalidation].

Ha például a kudu-konzolt kell használnia, vagy a kudu REST API (az Azure DevOps saját üzemeltetésű ügynökökkel való üzembe helyezése), két rekordot kell létrehoznia a saját Azure DNS saját zónájában vagy az egyéni DNS-kiszolgálón. 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása][pricing].

## <a name="limitations"></a>Korlátozások

Ha az Azure-függvényt privát végponttal rendelkező rugalmas prémium szintű csomagban használja, a függvény futtatásához vagy végrehajtásához az Azure web Portalon közvetlen hálózati hozzáférésre van szükség, vagy HTTP 403-hibaüzenetet fog kapni. Más szóval a böngészőnek képesnek kell lennie arra, hogy elérje a privát végpontot, hogy végrehajtsa a függvényt az Azure web Portalon. 

Az előzetes verzió ideje alatt a rendszer csak az üzemi tárolóhelyet teszi elérhetővé a privát végpont mögött, a többi tárolóhelyet nyilvános végpontnak kell elérnie.

Rendszeresen fejlesztjük a privát kapcsolat funkciót és a privát végpontot, és a korlátozásokról a [jelen cikkben][pllimitations] tájékozódhat.

## <a name="next-steps"></a>További lépések

Privát végpont üzembe helyezése a webalkalmazáshoz a portálon keresztül: a [webalkalmazásokhoz való magánhálózati kapcsolódás][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
