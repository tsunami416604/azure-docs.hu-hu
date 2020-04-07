---
title: Csatlakozás privát módon egy webalkalmazáshoz az Azure Private Endpoint használatával
description: Csatlakozás privát módon egy webalkalmazáshoz az Azure Private Endpoint használatával
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4d139cfa50afa94621066995314737fac70bbafe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756280"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Privát végpontok használata az Azure Web Apphoz (előzetes verzió)

> [!Note]
> Az előzetes verzió az USA keleti régiójában és az USA nyugati régiójában 2 régióban érhető el az összes PremiumV2 Windows és Linux webalkalmazáshoz és rugalmas prémium funkcióhoz. 

Az Azure Web App privát végpontjával engedélyezheti, hogy a magánhálózatban található ügyfelek biztonságosan hozzáférjenek az alkalmazáshoz a Privát kapcsolaton keresztül. A privát végpont egy IP-címet használ az Azure virtuális hálózat címterületéről. A magánhálózaton lévő ügyfél és a webalkalmazás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinchálózaton lévő privát kapcsolaton, így kiküszöböli a nyilvános internetről való kitettséget.

A Web App privát végpontjának használata lehetővé teszi a következőket:

- A webalkalmazás biztonságossá tétele a privát végpont konfigurálásával, a nyilvános expozíció kiküszöbölésével.
- Biztonságosan csatlakozhat a Web Apphoz olyan helyszíni hálózatokról, amelyek VPN- vagy ExpressRoute-alapú privát társviszony-létesítéssel csatlakoznak a virtuális hálózathoz.

Ha csak egy biztonságos kapcsolatra van szüksége a virtuális hálózat és a webalkalmazás között, a legegyszerűbb megoldás a Service Endpoint. Ha a webalkalmazást a helyszíni azure-átjárón, egy regionálistársviszony-létesítési virtuális hálózaton vagy egy globálisan társviszonyt, a privát végpont a megoldás.  

További információ: [Service Endpoints][serviceendpoint].

## <a name="conceptual-overview"></a>Fogalmi áttekintés

A privát végpont egy speciális hálózati adapter (NIC) az Azure Web App egy alhálózat a virtuális hálózat (VNet).
Amikor létrehoz egy privát végpontot a webalkalmazáshoz, biztonságos kapcsolatot biztosít a magánhálózat és a webalkalmazás ügyfelei között. A privát végponthoz egy IP-cím van rendelve a virtuális hálózat IP-címtartományából.
A privát végpont és a webalkalmazás közötti kapcsolat biztonságos [privát kapcsolatot][privatelink]használ. A privát végpont csak a webalkalmazás bejövő folyamataihoz használatos. A kimenő folyamatok nem fogják használni ezt a privát végpontot, de a [virtuális hálózat integrációs szolgáltatásán][vnetintegrationfeature]keresztül egy másik alhálózatban is beadhat kimenő folyamatokat a hálózatba.

Az alhálózat, ahol csatlakoztatja a privát végpont lehetnek más erőforrások is, nincs szükség egy dedikált üres alhálózat.
A privát végpontot a webalkalmazástól eltérő régióban is telepítheti. 

> [!Note]
>A virtuális hálózat integrációs szolgáltatás a Private Endpoint szolgáltatással megegyező alhálózatot nem használhatja, ez a virtuális hálózat integrációs szolgáltatásának korlátozása.

Biztonsági szempontból:

- Ha engedélyezi a privát végpontokat a webalkalmazáshoz, letiltja az összes nyilvános hozzáférést.
- Más virtuális hálózatokban és alhálózatokban több privát végpontot is engedélyezhet, beleértve a más régiókban lévő virtuális hálózatokat is.
- A privát végponti hálózati adapter IP-címének dinamikusnak kell lennie, de ugyanaz marad, amíg nem törli a privát végpontot.
- A privát végpont hálózati adapterének nem társítható NSG-kapcsolat.
- A magánvégpontot tartalmazó alhálózathoz nsg-társítható, de le kell tiltania a hálózati házirendek kényszerítését a magánvégpontok esetében: [Lásd: A hálózati házirendek letiltása a magánvégpontokhoz.][disablesecuritype] Ennek eredményeképpen nem szűrheti az NSG a hozzáférést a privát végponthoz.
- Ha engedélyezi a privát végpontot a webalkalmazásban, a webalkalmazás [hozzáférési korlátozások][accessrestrictions] konfigurációja nem lesz kiértékelve.
- Csökkentheti az adatok kiszivárgási kockázatát a virtuális hálózatból az összes NSG-szabály eltávolításával, ahol a cél az internet vagy az Azure-szolgáltatások címkézése. A Web App private-végpont alhálózatba való hozzáadása azonban lehetővé teszi, hogy az azonos telepítési bélyegzővel üzemeltetett és az internetnek elérhető webalkalmazást elérje.

A Web App webes HTTP-naplóiban megtalálja az ügyfélforrás IP-címét. Ez a TCP-proxy protokoll használatával valósul meg, és az ügyfél IP-tulajdonságát a Web Appba továbbítja. További információt a [Kapcsolatinformáció beszerzése a TCP-proxy 2-es v2 használatával][tcpproxy]című témakörben talál.


  > [!div class="mx-imgBorder"]
  > ![A Web App Private Endpoint globális áttekintése](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Mivel ez a szolgáltatás előzetes verzióban érhető el, nem módosítjuk a DNS-bejegyzést az előzetes verzió során. A DNS-bejegyzést saját kezűleg kell kezelnie a privát DNS-kiszolgálón vagy az Azure DNS privát zónájában.
Ha egyéni DNS-nevet kell használnia, hozzá kell adnia az egyéni nevet a Web Appban. Az előzetes verzió során az egyéni nevet ugyanúgy ellenőrizni kell, mint bármely egyéni nevet, nyilvános DNS-feloldás használatával. További információt az [egyéni DNS-érvényesítés][dnsvalidation] című témakörben talál.

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos részletekről az [Azure Private Link díjszabása][pricing].

## <a name="limitations"></a>Korlátozások

Rendszeresen fejlesztjük a Privát hivatkozás funkciót és a Privát végpontot, tekintse meg ebben a [cikkben][pllimitations] a korlátozásokkal kapcsolatos naprakész információkat.

## <a name="next-steps"></a>További lépések

Privát végpont üzembe helyezése a webalkalmazáshoz a portálon keresztül: [Hogyan csatlakozhat privátan egy webalkalmazáshoz][howtoguide]




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
