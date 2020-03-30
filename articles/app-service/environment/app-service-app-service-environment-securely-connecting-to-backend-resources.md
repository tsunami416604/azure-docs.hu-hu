---
title: Csatlakozás az 1-es háttér-véghez
description: Megtudhatja, hogyan csatlakozhat biztonságosan háttér-erőforrásokhoz egy App Service-környezetből. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687298"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Csatlakozás biztonságosan háttérerőforrásokhoz egy App Service-környezetből
Mivel az App Service-környezet **either** mindig egy Azure Resource Manager virtuális hálózaton **vagy** egy klasszikus üzembe helyezési modell [virtuális hálózatban][virtualnetwork]jön létre, az App Service-környezetből más háttérerőforrásokhoz való kimenő kapcsolatok kizárólag a virtuális hálózaton keresztül folyhatnak.  A 2016 júniusában történt legutóbbi módosítással az ASE-k olyan virtuális hálózatokba is telepíthetők, amelyek vagy nyilvános címtartományokat, vagy RFC1918 címtereket (azaz magáncímeket) használnak.  

Előfordulhat például, hogy egy SQL Server egy 1433-as porttal zárolt virtuális gépekből álló fürtön fut.  A végpont lehet ACLd, hogy csak a hozzáférést más erőforrások ugyanazon a virtuális hálózaton.  

Egy másik példa: a bizalmas végpontok a helyszínen futtathatók, és a helyek közötti vagy [az Azure ExpressRoute-kapcsolatokon][ExpressRoute] keresztül kapcsolódhatnak az [Azure-hoz.][SiteToSite]  Ennek eredményeképpen csak a helyek közötti vagy expressroute-alagutakhoz kapcsolódó virtuális hálózatok erőforrásai férhetnek hozzá a helyszíni végpontokhoz.

Az összes ilyen forgatókönyv esetén az App Service-környezetben futó alkalmazások biztonságosan csatlakozhatnak a különböző kiszolgálókhoz és erőforrásokhoz.  Az App Service-környezetben futó alkalmazásokból az azonos virtuális hálózat (vagy ugyanahhoz a virtuális hálózathoz kapcsolódó) privát végpontok közötti kimenő forgalom csak a virtuális hálózaton keresztül fog áramlani.  A privát végpontokra irányuló kimenő forgalom nem fog a nyilvános interneten keresztül áramlani.

Egy kikötés vonatkozik az App Service-környezetből érkező kimenő forgalom a virtuális hálózaton belüli végpontok.  Az App Service-környezetek nem érik el az App Service-környezettel **azonos** alhálózatban található virtuális gépek végpontjait.  Ez általában nem lehet probléma, amíg az App Service-környezetek vannak telepítve egy alhálózat számára fenntartott kizárólagos használatra csak az App Service-környezetben.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolat és DNS-követelmények
Ahhoz, hogy egy App Service-környezet megfelelően működjön, több végponthoz kimenő hozzáférést igényel. Az ASE által használt külső végpontok teljes listája az [ExpressRoute hálózati konfigurációja](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) című cikk "Szükséges hálózati kapcsolat" című részében található.

Az App Service-környezetek hez érvényes DNS-infrastruktúra szükséges a virtuális hálózathoz konfigurálva.  Ha az App Service-környezet létrehozása után bármilyen okból megváltozik a DNS-konfiguráció, a fejlesztők kényszeríthetik az App Service-környezetet az új DNS-konfiguráció felvételére.  A működés közbeni használatújraindítás a portálon az App Service-környezet felügyeleti paneljének tetején található "Újraindítás" ikonnal a környezet új DNS-konfigurációjának felvételéhez.

Azt is javasoljuk, hogy a virtuális hálózat minden egyéni DNS-kiszolgálóit az App Service-környezet létrehozása előtt előzetesen állítsa be.  Ha egy virtuális hálózat DNS-konfigurációja megváltozik az App Service-környezet létrehozása közben, az az App Service-környezet létrehozási folyamatának sikertelensítését eredményezi.  Hasonló ha egy VPN-átjáró másik végén létezik egyéni DNS-kiszolgáló, és a DNS-kiszolgáló nem érhető el vagy nem érhető el, az App Service-környezet létrehozási folyamata is sikertelen lesz.

## <a name="connecting-to-a-sql-server"></a>Csatlakozás SQL Server kiszolgálóhoz
Egy általános SQL Server-konfigurációvégpont figyel az 1433-as porton:

![SQL Server végpont][SqlServerEndpoint]

Két módszer létezik a forgalom ezen végpontra történő korlátozásához:

* [Hálózati hozzáférés-vezérlési listák][NetworkAccessControlLists] (hálózati Hozzáférés-vezérlési listák)
* [Hálózati biztonsági csoportok][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Hozzáférés korlátozása hálózati hozzáférés-hozzáférés-hozzáférési lid-rel
Az 1433-as port hálózati hozzáférés-vezérlési listával biztosítható.  Az alábbi példa a virtuális hálózaton belülről származó ügyfélcímeket listázza, és letiltja az összes többi ügyfél elérését.

![Példa a hálózati hozzáférés-vezérlési listára][NetworkAccessControlListExample]

Az SQL Server kiszolgálóval azonos virtuális hálózatban futó appok az SQL Server virtuális gép virtuális gép virtuális gép **virtuális** ip-címének virtuális ip-címével csatlakozhatnak az SQL Server-példányhoz.  

Az alábbi példa kapcsolati karakterlánc az SQL Server privát IP-címét használja.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Bár a virtuális gép nyilvános végponttal is rendelkezik, a nyilvános IP-címet használó csatlakozási kísérleteket a rendszer elutasítja a hálózati acl miatt. 

## <a name="restricting-access-with-a-network-security-group"></a>Hozzáférés korlátozása hálózati biztonsági csoporttal
A hozzáférés biztonságossá tétele egy hálózati biztonsági csoport.  A hálózati biztonsági csoportok alkalmazhatók egyes virtuális gépekre vagy a virtuális gépeket tartalmazó alhálózatra.

Először létre kell hozni egy hálózati biztonsági csoportot:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

A hálózati biztonsági csoport okkal nagyon egyszerű, ha csak a virtuális hálózat belső forgalmára korlátozza a hozzáférést.  A hálózati biztonsági csoport alapértelmezett szabályai csak az ugyanazon virtuális hálózat más hálózati ügyfeleitől engedélyezik a hozzáférést.

Ennek eredményeképpen az SQL Server hez való hozzáférés zárolása olyan egyszerű, mint egy hálózati biztonsági csoport alkalmazása az alapértelmezett szabályokkal az SQL Server t futtató virtuális gépekre vagy a virtuális gépeket tartalmazó alhálózatra.

Az alábbi minta egy hálózati biztonsági csoportot alkalmaz az alhálózatra:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

A végeredmény olyan biztonsági szabályok készlete, amelyek blokkolják a külső hozzáférést, miközben engedélyezik a virtuális hálózat belső hozzáférését:

![Alapértelmezett hálózati biztonsági szabályok][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Első lépések
Az App Service-környezetek ismerkedése az [App Service-környezet bemutatása című témakörben][IntroToAppServiceEnvironment]

Az App Service-környezetbe irányuló bejövő forgalom szabályozásáról az [App Service-környezetbe irányuló bejövő forgalom szabályozása című][ControlInboundASE] témakörben talál további információt.

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
