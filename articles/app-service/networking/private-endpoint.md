---
title: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
description: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 06/26/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: b9cf0467829425003a33ef806d8e7028e7f27add
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413399"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Privát végpontok használata az Azure Web App-hoz (előzetes verzió)

> [!Note]
> Az előzetes verzió frissítésével megjelent a kiszűrése Protection szolgáltatás.
>
> Az előzetes verzió az összes nyilvános régióban elérhető a Windows-és Linux-Web Apps és a rugalmas prémium szintű PremiumV2. 

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
>A VNet-integrációs szolgáltatás nem tudja használni a magánhálózati végponttal megegyező alhálózatot, ez a VNet-integrációs szolgáltatás korlátozása.

Biztonsági szempontból:

- Ha engedélyezi a privát végpontokat a webalkalmazáshoz, az összes nyilvános hozzáférés le van tiltva.
- Több privát végpontot is engedélyezhet más virtuális hálózatok és alhálózatokban, beleértve a más régiókban található virtuális hálózatok is.
- A magánhálózati végpont hálózati adapterének IP-címének dinamikusnak kell lennie, de addig marad, amíg nem törli a privát végpontot.
- A privát végpont hálózati adaptere nem rendelkezhet társított NSG.
- A privát végpontot futtató alhálózathoz NSG társítható, de le kell tiltania a hálózati házirendek kényszerítését a privát végponton: a [hálózati házirendek letiltása privát végpontok esetén][disablesecuritype]című témakört. Ennek eredményeképpen egyetlen NSG sem tudja szűrni a privát végponthoz való hozzáférést.
- Ha engedélyezi a privát végpontot a webalkalmazáshoz, a rendszer nem értékeli ki a webalkalmazás [hozzáférési korlátozásait][accessrestrictions] .
- Az adatok kiszűrése kockázatát a VNet távolíthatja el az összes olyan NSG-szabály eltávolításával, ahol a cél az Internet vagy az Azure-szolgáltatások címkéje. Ha privát végpontot telepít egy webalkalmazáshoz, akkor csak a privát végponton keresztül érheti el ezt az adott webalkalmazást. Ha van egy másik webalkalmazása, egy másik dedikált privát végpontot kell telepítenie ehhez a webalkalmazáshoz.

A webalkalmazás webes HTTP-naplóiban megtalálja az ügyfél forrásának IP-címét. Ez a funkció a TCP proxy protokoll használatával valósul meg, amely a-ügyfél IP-tulajdonságát továbbítja a webalkalmazásnak. További információ: a [kapcsolatok adatainak beszerzése a TCP proxy v2 használatával][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![A Web App Private Endpoint globális áttekintése](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Ha privát végpontot használ a webalkalmazáshoz, a kért URL-címnek meg kell egyeznie a webalkalmazás nevével. Alapértelmezés szerint mywebappname.azurewebsites.net.

Alapértelmezés szerint privát végpont nélkül a webalkalmazás nyilvános neve a fürt kanonikus neve.
A névfeloldás például a következő lesz:

|Name |Típus |Érték |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


Privát végpont telepítésekor a DNS-bejegyzést úgy frissítjük, hogy a kanonikus név mywebapp.privatelink.azurewebsites.net mutasson.
A névfeloldás például a következő lesz:

|Name |Típus |Érték |Megjegyzés |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|< – ez a nyilvános IP-cím nem az Ön privát végpontja, 503-as hibát fog kapni|

Egy privát DNS-kiszolgálót vagy egy Azure DNS privát zónát kell beállítania, hogy tesztelje a számítógép gazdagépének bejegyzését.
A létrehozandó DNS-zóna a következő: **privatelink.azurewebsites.net**. Regisztrálja a webalkalmazás rekordját egy rekorddal és a magánhálózati végpont IP-címével.
A névfeloldás például a következő lesz:

|Name |Típus |Érték |Megjegyzés |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|< – ezt a bejegyzést a DNS-rendszerben úgy kezelheti, hogy a magánhálózati végpont IP-címére mutasson.|

A DNS-konfiguráció után a webalkalmazást a mywebappname.azurewebsites.net alapértelmezett névvel együtt érheti el.


Ha egyéni DNS-nevet kell használnia, hozzá kell adnia az egyéni nevet a webalkalmazásban. Az előzetes verzióban az egyéni nevet a nyilvános DNS-feloldást használó bármely egyéni névnek hasonlóan kell érvényesíteni. További információ: [Egyéni DNS-ellenőrzés][dnsvalidation].

A kudu-konzol vagy a kudu REST API (az Azure DevOps saját üzemeltetésű ügynökökkel való üzembe helyezése) esetében két rekordot kell létrehoznia a Azure DNS saját zónájában vagy az egyéni DNS-kiszolgálón. 

| Name | Típus | Érték |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 

> [!TIP]
> Ezt a két rekordot automatikusan kitölti a rendszer, ha rendelkezik egy privatelink.azurewebsites.net nevű privát DNS-zónával ahhoz a VNet, amelyhez létrehozta a privát végpontot.


## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása][pricing].

## <a name="limitations"></a>Korlátozások

Ha az Azure-függvényt privát végponttal rendelkező rugalmas prémium szintű csomagban használja, a függvény futtatásához vagy végrehajtásához az Azure web Portalon közvetlen hálózati hozzáférésre van szükség, vagy HTTP 403-hibaüzenetet fog kapni. Más szóval a böngészőnek képesnek kell lennie arra, hogy elérje a privát végpontot, hogy végrehajtsa a függvényt az Azure web Portalon. 

Az előzetes verzió ideje alatt a rendszer csak az üzemi tárolóhelyet teszi elérhetővé a privát végpont mögött, a többi tárolóhelyet nyilvános végpontnak kell elérnie.

Rendszeresen fejlesztjük a privát kapcsolat funkciót és a privát végpontot, és a korlátozásokról a [jelen cikkben][pllimitations] tájékozódhat.

## <a name="next-steps"></a>További lépések

Ha privát végpontot szeretne telepíteni a webalkalmazáshoz a portálon keresztül, tekintse meg a következő témakört: [privát kapcsolódás webalkalmazáshoz][howtoguide]




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
