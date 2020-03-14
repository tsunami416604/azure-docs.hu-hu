---
title: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
description: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136672"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Privát végpontok használata az Azure Web App-hoz (előzetes verzió)

Az Azure-webalkalmazás privát végpontjának használatával engedélyezheti a magánhálózaton található ügyfelek számára, hogy biztonságosan hozzáférjenek az alkalmazáshoz privát kapcsolaton keresztül. A privát végpont az Azure VNet IP-címét használja. A magánhálózaton lévő ügyfél és a webalkalmazás közötti hálózati forgalom a vnet és a Microsoft gerinc hálózatán található privát kapcsolaton keresztül történik, ami kiküszöböli a nyilvános internetről való kitettséget.

A webalkalmazás privát végpontjának használata lehetővé teszi a következőket:

- A webalkalmazás biztonságossá tétele a szolgáltatási végpont konfigurálásával, a nyilvános kitettség megszüntetésével
- Biztonságosan csatlakozhat a webalkalmazáshoz olyan helyszíni hálózatokból, amelyek VPN-vagy ExpressRoute-alapú privát kapcsolattal csatlakoznak a vnet.

Ha csak biztonságos kapcsolatra van szüksége a vnet és a webalkalmazás között, a szolgáltatás végpontja a legegyszerűbb megoldás. Ha a webalkalmazást a helyszíni Azure-átjárón keresztül is el kell érnie, egy regionálisan felhasználható vnet vagy egy globálisan megválasztott vnet, a magánhálózati végpont a megoldás.  

További információ a [szolgáltatási végpontról][serviceendpoint]

## <a name="conceptual-overview"></a>Fogalmi áttekintés

A privát végpont egy speciális hálózati adapter (NIC) az Azure-webalkalmazáshoz a Virtual Network (vnet) alhálózatában.
Amikor létrehoz egy privát végpontot a webalkalmazáshoz, biztonságos kapcsolatot biztosít a magánhálózaton és a webalkalmazásban található ügyfelek között. A magánhálózati végpont IP-címet kap a vnet IP-címének tartományához.
A magánhálózati végpont és a webalkalmazás közötti kapcsolat biztonságos [privát hivatkozást][privatelink]használ. A magánhálózati végpont csak a webalkalmazásba érkező bejövő folyamatokhoz használható. A kimenő folyamatok nem fogják használni ezt a magánhálózati végpontot, de a kimenő folyamatokat egy másik alhálózaton is beinjektálhatja a [vnet-integrációs szolgáltatáson][vnetintegrationfeature]keresztül.

Az alhálózatot, amelyhez a privát végpontot csatlakoztatja, más erőforrásokkal is rendelkezhet, nincs szükség dedikált üres alhálózatra.
A privát végpontokat a webalkalmazástól eltérő régióban is telepítheti. 

> [!Note]
>A vnet integrációs funkciója nem használhatja ugyanazt az alhálózatot, mint a privát végpont, ez a vnet-integrációs szolgáltatás korlátozása.

A biztonsági szempontból:

- Ha engedélyezi a szolgáltatási végpontot a webalkalmazáshoz, az összes nyilvános hozzáférés le van tiltva
- Több privát végpont is engedélyezhető más virtuális hálózatok és alhálózatokban, beleértve a virtuális hálózatok más régiókban is
- A magánhálózati végpont hálózati adapterének IP-címének dinamikusnak kell lennie, de csak akkor marad, ha törli a privát végpontot.
- A privát végpont hálózati adapteréhez nem tartozhat NSG társítva
- A privát végpontot futtató alhálózathoz NSG társítható, de a magánhálózati házirendek kényszerítését le kell tiltania a privát végponton. lásd [ezt a cikket][disablesecuritype]. Ennek eredményeképpen egyetlen NSG sem tudja szűrni a privát végponthoz való hozzáférést.
- Ha engedélyezi a privát végpontot a webalkalmazáshoz, a rendszer nem értékeli ki a webalkalmazás [hozzáférési korlátozásait][accessrestrictions] .
- Csökkentheti az adatok kiszűrése kockázatát a vnet az összes olyan NSG-szabály eltávolításával, ahol a cél az Internet vagy az Azure-szolgáltatások címkéje. Ha azonban egy webes App Service végpontot ad hozzá az alhálózathoz, azzal lehetővé teszi, hogy elérje az azonos bélyegzőben üzemeltetett webalkalmazásokat, és az interneten keresztül is elérhetővé válik.

A webalkalmazás privát végpontja elérhető a PremiumV2, és egy külső kiegészítő szolgáltatással elkülönítették.

A webalkalmazás webes http-naplóiban megtalálja az ügyfél forrásának IP-címét. Megvalósítjuk a TCP-proxy protokollt, amely a webalkalmazásnak az ügyfél IP-tulajdonságával való továbbítását. További információkért tekintse meg [ezt a cikket][tcpproxy].

![Globális áttekintés][1]


## <a name="dns"></a>DNS

Mivel ez a funkció előzetes verzióban érhető el, nem változtatjuk meg a DNS-bejegyzést az előzetes verzióban. A DNS-bejegyzést a saját DNS-kiszolgálójában vagy Azure DNS privát zónában kell kezelnie. Ha egyéni DNS-nevet kell használnia, hozzá kell adnia az egyéni nevet a webalkalmazásban. Az előzetes verzióban az egyéni nevet a nyilvános DNS-feloldást használó bármely egyéni névnek hasonlóan kell érvényesíteni. [Egyéni DNS-érvényesítés – technikai útmutató][dnsvalidation]

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása][pricing].

## <a name="limitations"></a>Korlátozások

Rendszeresen fejlesztjük a privát kapcsolat funkciót és a privát végpontot, és a korlátozásokról a [jelen cikkben][pllimitations] tájékozódhat.

## <a name="next-steps"></a>További lépések

Privát végpont üzembe helyezése a webalkalmazáshoz a portálon keresztül: a [webalkalmazásokhoz való magánhálózati kapcsolódás][howtoguide]


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

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
