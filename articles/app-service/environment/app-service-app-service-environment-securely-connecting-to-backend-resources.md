---
title: Biztonságos kapcsolódás háttér erőforrásokhoz az App Service-környezet
description: További tudnivalók a biztonságos kapcsolódás App Service-környezetből származó.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: a43d88d64710b95dd753c19f02582f22accac8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836524"
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Biztonságos kapcsolódás háttér erőforrásokhoz az App Service-környezet
## <a name="overview"></a>Áttekintés
Mivel az App Service-környezetek mindig jön létre **vagy** az Azure Resource Manager virtuális hálózati **vagy** klasszikus telepítési modell [virtuális hálózati][virtualnetwork], más háttér erőforrások az App Service-környezetek kimenő kapcsolatok áramolhasson az kizárólag a virtuális hálózaton.  2016. június végzett legutóbbi módosítását ASEs is is telepíthető az nyilvános címtartományt, vagy RFC1918 címterek (azaz Magáncímeket) használó virtuális hálózatok.  

Előfordulhat például, a virtuális gépek zárolva 1433-as port a fürtön futó SQL Server.  A végpont lehet ACLd, hogy csak más erőforrásokhoz való hozzáférést a virtuális hálózaton.  

Másik példaként, bizalmas végpontok előfordulhat, hogy működik a helyszíni és csatlakoztatni az Azure-bA vagy [pont-pont] [ SiteToSite] vagy [Azure ExpressRoute] [ ExpressRoute] kapcsolatok.  Ennek eredményeképpen a pont-pont vagy ExpressRoute-alagutak csatlakoztatott virtuális hálózatok csak erőforrások hozzáférhetnek a helyszíni végpont lesz.

Az összes forgatókönyvekben az App Service-környezetek futó alkalmazások tudnak biztonságosan csatlakozik a különböző kiszolgálók és az erőforrások.  A saját végpontokhoz ugyanazon virtuális hálózatban egy App Service Environment-környezetben futó alkalmazások kimenő forgalom (vagy ugyanahhoz a virtuális hálózathoz csatlakozik), csak a folyamat lesz a virtuális hálózaton keresztül.  Saját végpontokhoz kimenő forgalom nem halad a nyilvános interneten keresztül.

Egy szerint a végpontokat a virtuális hálózaton belül az App Service-környezetek vonatkozik a kimenő forgalom.  App Service Environment-környezetek nem érhető el a virtuális gépek találhatók végpontok a **azonos** az App Service Environment-környezet alhálózaton.  Általában ne legyen hiba, amíg az App Service Environment-környezetek telepít egy alhálózatba, csak az App Service Environment-környezet által kizárólagos használatra fenntartva.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolatok és a DNS-követelmények
Az App Service Environment-környezet működését, a különböző végpontok kimenő hozzáférést igényel. "A hálózati kapcsolat szükséges" szakaszában van a külső végpontok egy ASE által használt teljes listáját a [ExpressRoute tartozó fürthálózat-konfiguráció](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) cikk.

App Service-környezetek virtuális hálózat egy érvényes DNS-infrastruktúra szükséges.  Ha bármilyen okból az App Service Environment-környezet létrehozása után a DNS-konfiguráció módosul, a fejlesztők kényszerítheti az App Service-környezet az új DNS-konfiguráció érvényesítéséhez.  A működés közbeni környezet újra kell indítani a portálon az App Service Environment-felügyelet panel tetején található "Restart" ikonnal váltanak, akkor a a környezet az új DNS-konfiguráció érvényesítéséhez.

Ajánlott továbbá, hogy a virtuális hálózaton egyéni DNS-kiszolgálók kell-e a telepítő az App Service Environment-környezet létrehozása előtt időben.  Ha egy virtuális hálózat DNS-konfiguráció megváltozott egy App Service Environment-környezet létrehozása közben, az App Service Environment-környezet létrehozása folyamat sikertelen vezethet.  Egy hasonló tekintettel az egy egyéni DNS-kiszolgáló létezik a VPN-átjáró másik végén, és a DNS-kiszolgáló nem érhető el vagy nem érhető el, ha az App Service Environment-környezet létrehozása is sikertelen lesz.

## <a name="connecting-to-a-sql-server"></a>Kapcsolódás az SQL-kiszolgálóhoz
Egy közös SQL Server-konfigurációs az 1433-as portot figyelő végponttal rendelkezik:

![SQL Server-végpont][SqlServerEndpoint]

Kétféleképpen forgalom korlátozására ehhez a végponthoz:

* [Hálózati hozzáférés-vezérlési listák] [ NetworkAccessControlLists] (hálózati hozzáférés-vezérlési listák)
* [Hálózati biztonsági csoportok][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>A hálózati hozzáférés-vezérlési lista a hozzáférés korlátozása
1433-as port a hálózati hozzáférés-vezérlési lista segítségével biztosítható.  Whitelists ügyfél az alábbi példában egy virtuális hálózaton belül származó címek, és engedélyezi, hogy más ügyfelekkel.

![Hálózati hozzáférés vezérlő példa][NetworkAccessControlListExample]

Az SQL Server fog tudni csatlakozni az SQL Server példányát használja, az azonos virtuális hálózatban lévő App Service Environment-környezetben futó alkalmazások a **belső hálózatok** az SQL Server virtuális gép IP-címet.  

Az alábbi példa kapcsolati karakterláncot az SQL Server Privát IP-címére hivatkozik.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Habár a virtuális gép egy nyilvános végpontot, a nyilvános IP-cím használatával történő kapcsolódási kísérletek program elutasítja a hálózati hozzáférés-vezérlési lista miatt. 

## <a name="restricting-access-with-a-network-security-group"></a>Hálózati biztonsági csoport a hozzáférés korlátozása
Hálózati biztonsági csoport van egy másik módjáról hozzáférés biztosítása érdekében.  Hálózati biztonsági csoportok egyedi virtuális gépeket, vagy egy alhálózathoz, virtuális gépeket tartalmazó lehet alkalmazni.

Először egy hálózati biztonsági csoportot kell létrehozni:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Hozzáférés korlátozása csak a virtuális hálózat belső forgalom a hálózati biztonsági csoport nagyon egyszerű.  Az alapértelmezett szabályok a hálózati biztonsági csoport csak engedélyezik a hozzáférést, az azonos virtuális hálózatban lévő egyéb hálózati ügyfelekről.

Ennek eredményeképpen zárolás SQL-kiszolgálóhoz való hozzáférés, más dolga, mint egy hálózati biztonsági csoport az alapértelmezett szabály alkalmazása vagy a virtuális gépek futnak az SQL Server, vagy az alhálózatot a virtuális gépeket tartalmazó.

Az alábbi minta hálózati biztonsági csoport az tartalmazó alhálózat vonatkozik:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

A végeredménynek olyan szabályokat, amelyek külső hozzáférés, miközben lehetővé teszi a virtuális hálózat belső hozzáférés letiltása:

![Alapértelmezett hálózati biztonsági szabályok][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Bevezetés
App Service Environment-környezetek megkezdéséhez, lásd: [App Service Environment bemutatása][IntroToAppServiceEnvironment]

További részletek az App Service-környezet a bejövő forgalom vezérlése körül: [az App Service-környezetek bejövő forgalom szabályozása][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
