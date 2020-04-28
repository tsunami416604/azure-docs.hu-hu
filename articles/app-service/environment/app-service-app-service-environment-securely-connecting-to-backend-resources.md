---
title: Kapcsolódás a back end V1-hez
description: További információ a háttérbeli erőforrásokhoz való biztonságos kapcsolódásról egy App Service Environment. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687298"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Biztonságos kapcsolódás App Service környezetből a végponti erőforrásokhoz
Mivel a App Service Environment **mindig egy Azure Resource Manager** virtuális hálózatban, **vagy** egy klasszikus üzemi modell [virtuális hálózatban][virtualnetwork]jön létre, a app Service Environmentból más háttér-erőforrások felé irányuló kimenő kapcsolatok kizárólag a virtuális hálózaton keresztül áramlanak.  A 2016 júniusában történt legutóbbi módosítással a ASE olyan virtuális hálózatokon is üzembe helyezhetők, amelyek nyilvános címtartományt vagy RFC1918 (például magánhálózati címeket) használnak.  

Előfordulhat például, hogy egy olyan SQL Server fut a virtuális gépek fürtjén, amelyen a 1433-es port le van zárva.  Előfordulhat, hogy a végpont ACLd csak ugyanazon a virtuális hálózaton lévő más erőforrásokból való hozzáférést engedélyezi.  

Egy másik példa, hogy a bizalmas végpontok a helyszínen futnak, és a [helyek közötti][SiteToSite] vagy az [Azure ExpressRoute][ExpressRoute] -kapcsolatokon keresztül csatlakoznak az Azure-hoz.  Ennek eredményeképpen csak a helyek közötti vagy ExpressRoute-alagutakhoz csatlakozó virtuális hálózatok erőforrásai férhetnek hozzá a helyszíni végpontokhoz.

Ezen forgatókönyvek esetében a App Service Environment futó alkalmazások képesek lesznek biztonságosan csatlakozni a különböző kiszolgálókhoz és erőforrásokhoz.  A App Service Environmentban futó alkalmazások kimenő forgalma ugyanazon a virtuális hálózaton (vagy ugyanahhoz a virtuális hálózathoz csatlakoztatva) lévő privát végpontokon fut, csak a virtuális hálózatra kerül.  A privát végpontok felé irányuló kimenő forgalom nem a nyilvános interneten keresztül történik.

Az egyik kikötés a virtuális hálózaton belüli végpontokra App Service Environment kimenő forgalomra vonatkozik.  App Service környezetek nem érhetik el a virtuális gépek azon végpontját, amely **ugyanabban** az alhálózatban található, mint a app Service Environment.  Ez általában nem jelent problémát, ha App Service környezetek csak a App Service Environment által kizárólagos használatra fenntartott alhálózatba vannak telepítve.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolat és DNS-követelmények
Ahhoz, hogy egy App Service Environment megfelelően működjön, a különböző végpontokhoz kimenő hozzáférést igényel. A szolgáltató által használt külső végpontok teljes listája a [ExpressRoute hálózati konfigurációjának](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) "szükséges hálózati kapcsolat" szakaszában található.

App Service környezetekhez a virtuális hálózathoz konfigurált érvényes DNS-infrastruktúra szükséges.  Ha bármilyen okból a DNS-konfiguráció megváltozik egy App Service Environment létrehozása után, a fejlesztők kényszerítheti az App Service Environment az új DNS-konfiguráció felvételére.  A működés közbeni környezet újraindításának elindítása a portál App Service Environment felügyelet paneljének tetején található újraindítás ikon használatával a környezet az új DNS-konfigurációt fogja felvenni.

Azt is javasoljuk, hogy a vnet lévő egyéni DNS-kiszolgálókat a App Service Environment létrehozása előtt előre be kell állítani.  Ha a virtuális hálózat DNS-konfigurációja megváltozik egy App Service Environment létrehozásakor, az App Service Environment létrehozási folyamata sikertelen lesz.  Hasonló módon, ha egy egyéni DNS-kiszolgáló létezik egy VPN-átjáró másik végén, és a DNS-kiszolgáló nem érhető el, vagy nem érhető el, a App Service Environment létrehozási folyamata is sikertelen lesz.

## <a name="connecting-to-a-sql-server"></a>Csatlakozás egy SQL Serverhoz
Egy közös SQL Server konfiguráció egy végpontot figyel a 1433-as porton:

![SQL Server végpont][SqlServerEndpoint]

A végpontra irányuló forgalom korlátozásának két módja van:

* [Hálózati Access Control listák][NetworkAccessControlLists] (hálózati ACL-ek)
* [Hálózati biztonsági csoportok][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Hozzáférés korlátozása hálózati ACL-sel
A 1433-es portot hálózati hozzáférés-vezérlési lista használatával lehet biztosítani.  Az alábbi példa a virtuális hálózatból származó ügyfél-címeket, valamint az összes többi ügyfél hozzáférését blokkolja.

![Példa hálózati Access Control listára][NetworkAccessControlListExample]

A App Service Environmentban futó összes alkalmazás, amely ugyanabban a virtuális hálózaton fut, mint a SQL Server, a SQL Server virtuális gép **VNet belső** IP-címének használatával tud csatlakozni a SQL Server példányhoz.  

Az alábbi példában szereplő kapcsolati sztring a saját magánhálózati IP-címére hivatkozik a SQL Server.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Bár a virtuális gép nyilvános végponttal is rendelkezik, a nyilvános IP-címet használó csatlakozási kísérletek a hálózati ACL miatt el lesznek utasítva. 

## <a name="restricting-access-with-a-network-security-group"></a>Hozzáférés korlátozása hálózati biztonsági csoporttal
A hozzáférés biztonságossá tételének másik módja egy hálózati biztonsági csoport.  A hálózati biztonsági csoportok az egyes virtuális gépekre, illetve a virtuális gépeket tartalmazó alhálózatra is alkalmazhatók.

Először létre kell hozni egy hálózati biztonsági csoportot:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

A hozzáférés korlátozása csak a VNet belső forgalmára nagyon egyszerű hálózati biztonsági csoporttal.  A hálózati biztonsági csoport alapértelmezett szabályai csak az azonos virtuális hálózatban lévő más hálózati ügyfelek hozzáférését teszik lehetővé.

Ennek eredményeképpen a SQL Serverhoz való hozzáférés zárolása olyan egyszerű, mint a hálózati biztonsági csoport alapértelmezett szabályainak alkalmazása a SQL Servert futtató virtuális gépek vagy a virtuális gépeket tartalmazó alhálózat számára.

Az alábbi minta egy hálózati biztonsági csoportot alkalmaz az azt tartalmazó alhálózatra:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

A végeredmény olyan biztonsági szabályok összessége, amelyek letiltják a külső hozzáférést, miközben lehetővé teszik a VNet belső hozzáférését:

![Alapértelmezett hálózati biztonsági szabályok][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Első lépések
App Service környezetek használatának megkezdéséhez tekintse [meg a app Service Environment bemutatása][IntroToAppServiceEnvironment] című témakört.

A App Service Environment bejövő forgalmának szabályozásával kapcsolatban lásd: a [Bejövő forgalom vezérlése egy app Service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
