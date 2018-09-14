---
title: Biztonságos csatlakozás a Háttérerőforrásokhoz egy App Service Environment-környezet a
description: Ismerje meg való biztonságos kapcsolódás háttérerőforrásokhoz egy App Service Environment-környezet a.
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
ms.openlocfilehash: 1732e6778febac60a25da74c330cb3d3da94154d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580051"
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Biztonságos csatlakozás a Háttérerőforrásokhoz egy App Service Environment-környezet a
## <a name="overview"></a>Áttekintés
Mivel az App Service-környezet mindig létrejön **vagy** egy Azure Resource Managerbeli virtuális hálózat **vagy** klasszikus telepítési modell [virtuális hálózat] [ virtualnetwork], más háttérerőforrásokhoz egy App Service Environment-környezet a kimenő kapcsolatokat áramolhasson kizárólag a virtuális hálózaton.  Egy friss változás 2016. június végzett, az ASE is üzembe helyezhetők a címtartomány nyilvános, vagy az RFC1918 címterek (azaz a magánhálózati címek) használó virtuális hálózatokra.  

Előfordulhat például, a virtuális gépek zárolja 1433-as port a fürtön futó SQL Server.  A végpont lehet ACLd, más erőforrásokhoz való hozzáférés engedélyezése csak az azonos virtuális hálózaton.  

Másik példaként bizalmas végpontok futtathatnak a helyszínen, és csatlakoztatni az Azure-bA vagy [Site-to-Site] [ SiteToSite] vagy [Azure ExpressRoute] [ ExpressRoute] kapcsolatok.  Ennek eredményeképpen a Site-to-Site- vagy ExpressRoute-alagutak csatlakoztatott virtuális hálózatok csak erőforrásokat érhetik el a helyszíni végpontokig lesz.

Az összes forgatókönyv esetében egy App Service-környezetben futó alkalmazások fogja tudni biztonságosan csatlakozhat a különböző kiszolgálókon és erőforrásokat.  Kimenő forgalom az azonos virtuális hálózatba privát végpontokra App Service Environment-környezetben futó alkalmazások (vagy az azonos virtuális hálózathoz csatlakozó), csak a flow lesz a virtuális hálózaton keresztül.  Privát-végpontokra irányuló kimenő forgalom nem a nyilvános interneten keresztül fog haladni.

Az App Service-környezet virtuális hálózaton belüli-végpontokra irányuló kimenő forgalom egyik kikötés vonatkozik.  App Service Environment-környezetek nem érhető el a végpontok található virtuális gépek a **ugyanazon** az App Service Environment alhálózatban.  Ez általában nem lehet probléma mindaddig, amíg az App Service Environment-környezetek a rendszer üzembe helyezi egy alhálózat számára csak az App Service-környezet kizárólagos használatra fenntartva.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolat és DNS-követelmények
Az App Service-környezet megfelelő működéséhez különböző végpontok a kimenő hozzáférést igényel. Az "A hálózati kapcsolat szükséges" szakaszában szerepel a külső végpontokat az ASE által használt teljes listáját a [az ExpressRoute hálózati konfiguráció](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) cikk.

App Service Environment-környezetek egy érvényes DNS-infrastruktúra a virtuális hálózat szükséges.  Ha bármilyen okból a DNS-konfigurációt egy App Service Environment-környezet létrehozása után módosul, a fejlesztők kényszerítheti egy App Service Environment folytattuk a munkát az új DNS-konfiguráció.  Elindítása a működés közbeni környezet újra kell indítani az App Service Environment-felügyelet panel a portál tetején található "Restart" ikonra a környezet átvételéhez az új DNS-konfiguráció miatt.

Emellett javasoljuk, hogy minden egyéni DNS-kiszolgáló a virtuális hálózaton kell-e időben App Service-környezet létrehozása előtt a telepítő.  Ha egy virtuális hálózat DNS-konfiguráció módosul, míg az App Service-környezet létrehozása folyamatban van, az App Service Environment-környezet létrehozása folyamat sikertelen, amely fog eredményezni.  Egy hasonló tekintettel az egyéni DNS-kiszolgáló létezik VPN gateway másik végén, és a DNS-kiszolgáló nem érhető el vagy nem érhető el, ha az App Service Environment-környezet létrehozása is sikertelen lesz.

## <a name="connecting-to-a-sql-server"></a>Kapcsolódás az SQL-kiszolgálóhoz
Egy közös SQL Server-konfiguráció az 1433-as porton figyeli a végpont rendelkezik:

![SQL-kiszolgálói végpont][SqlServerEndpoint]

Kétféleképpen az ehhez a végponthoz való forgalom korlátozása:

* [Hálózati hozzáférés-vezérlési listák] [ NetworkAccessControlLists] (hálózati hozzáférés-vezérlési listák)
* [Hálózati biztonsági csoportok][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Az ACL-t hálózati hozzáférés korlátozása
1433-as port a hálózati hozzáférés-vezérlési lista használatával kell védeni.  Listáinak ügyfél az alábbi példában egy virtuális gépen belüli – származó címeket, és letiltja a hozzáférést minden más ügyfelek számára.

![Hálózati hozzáférés ellenőrzési lista például][NetworkAccessControlListExample]

Az SQL Server fog tudni kapcsolódni az SQL Server példány használatával, az azonos virtuális hálózatba App Service Environment környezetben futó alkalmazások a **belső virtuális hálózat** az SQL Server virtuális gép IP-címet.  

Az alábbi példa kapcsolati karakterláncot az SQL Server használatával a magánhálózati IP-címére hivatkozik.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Habár a virtuális gép egy nyilvános végpontot, a nyilvános IP-cím használatával történő kapcsolódási kísérletek a rendszer elutasítja a hálózati hozzáférés-szabályozási miatt. 

## <a name="restricting-access-with-a-network-security-group"></a>Egy hálózati biztonsági csoporttal való hozzáférés korlátozása
Egy másik módszert a hozzáférés biztonságossá tétele hálózati biztonsági csoport van.  Hálózati biztonsági csoportok az egyes virtuális gépekhez, vagy virtual machines gépet tartalmazó alhálózathoz alkalmazhatók.

Először egy hálózati biztonsági csoportot kell létrehozni:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Hozzáférés korlátozása csak a virtuális hálózatok közötti belső forgalom használata nagyon egyszerű egy hálózati biztonsági csoporttal.  Az alapértelmezett szabályok a hálózati biztonsági csoport hozzáférés engedélyezése csak az azonos virtuális hálózatban található hálózati ügyfelekről.

Ennek eredményeképpen sémákra hozzáférése az SQL Serverhez rendkívül egyszerű, vagy a virtuális gépek futnak, az SQL Server vagy a virtuális gépeket tartalmazó alhálózatról egy hálózati biztonsági csoport az alapértelmezett szabályok alkalmazása.

Az alábbi minta egy hálózati biztonsági csoportot tartalmazó alhálózathoz vonatkozik:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

A végeredmény a következő biztonsági szabályok, amelyek külső hozzáférés, miközben lehetővé teszi a virtuális hálózatok közötti belső hozzáférés letiltása:

![Alapértelmezett hálózati biztonsági szabályok][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Első lépések
Első lépések az App Service Environment-környezetek, lásd: [App Service Environment bemutatása][IntroToAppServiceEnvironment]

További információ az App Service-környezet bejövő forgalom szabályozása körül: [az App Service-környezet bejövő forgalom szabályozása][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
