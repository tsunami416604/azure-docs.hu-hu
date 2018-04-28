---
title: Hálózati konfiguráció adatai Expressroute használata
description: Hálózati konfiguráció részletei App Service Environment-környezetek futó virtuális hálózatok ExpressRoute-Kapcsolatcsoportot csatlakozik.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: fcb9fa9004039205fa49f63c50d5907a8029a079
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Az ExpressRoute-kapcsolattal rendelkező App Service Environments-környezetek hálózati konfigurációjának részletei
## <a name="overview"></a>Áttekintés
Az ügyfelek kapcsolódhatnak egy [Azure ExpressRoute] [ ExpressRoute] kapcsolat a virtuális hálózati infrastruktúrára, így kiterjesztése a helyszíni hálózat az Azure-bA.  Az App Service-környezetek hozhatók létre az alhálózatot [virtuális hálózati] [ virtualnetwork] infrastruktúra.  Az App Service-környezetben futó alkalmazások tud majd biztonságos kapcsolat létrehozása a háttér-erőforrások elérhető csak az ExpressRoute-kapcsolaton keresztül.  

Az App Service-környezetek hozhatók létre **vagy** az Azure Resource Manager virtuális hálózati **vagy** klasszikus telepítési modell virtuális hálózatot.  2016. június végzett legutóbbi módosítását ASEs is most is telepíthető az nyilvános címtartományt, vagy RFC1918 címterek (azaz Magáncímeket) használó virtuális hálózatok. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Szükséges hálózati kapcsolat
Nincsenek hálózati kapcsolat az App Service Environment-környezetek, előfordulhat, hogy kezdetben jutott egy ExpressRoute csatlakoztatott virtuális hálózatban.  App Service-környezetek megfelelő működéséhez szükséges a következő lépéseket:

* Azure Storage végpontok világszerte mindkét porton 80-as és 443-as kimenő hálózati kapcsolatot.  Ez magában foglalja az App Service Environment-környezet ugyanabban a régióban található végpontok, valamint tárolási végpontok található **más** Azure-régiók.  Azure Storage-végpontok oldja meg a következő DNS-tartományok alatt: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* és *file.core.windows.net*.  
* Kimenő hálózati kapcsolat az Azure-fájlok szolgáltatás 445-ös porton.
* Az App Service Environment-környezet ugyanabban a régióban található Sql-adatbázis a végpontok kimenő hálózati kapcsolatot.  SQL-adatbázis a végpontok oldja meg a következő tartományi: *database.windows.net*.  Ehhez szükséges portok 1433-as számú 11000-11999 és 14000-14999 hozzáférését megnyitása.  További információ: [Ez a cikk a Sql Database 12-es port használati](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Kimenő hálózati kapcsolat az Azure felügyeleti vezérlősík végpontok (ASM, mind az ARM-végpontok).  Ez magában foglalja a kimenő kapcsolat mindkét *management.core.windows.net* és *management.azure.com*. 
* Kimenő hálózati kapcsolattal *ocsp.msocsp.com*, *mscrl.microsoft.com* és *crl.microsoft.com*.  Ez az SSL-funkciók támogatásához szükséges.
* A DNS-beállításait a virtuális hálózat összes végpontok és a korábbi pontban tartományok megoldásának képesnek kell lennie.  Ezeket a végpontokat nem oldható fel, ha az App Service Environment-környezet létrehozása kísérletek sikertelenek lesznek, és meglévő App Service Environment-környezetek sérültként lesz megjelölve.
* Kimenő hozzáférést 53-as porton szükség, DNS-kiszolgálókkal való kommunikációhoz.
* Ha egy egyéni DNS-kiszolgáló létezik a VPN-átjáró másik végén, a DNS-kiszolgáló elérhető-e az alhálózatból az App Service Environment-környezet tartalmazó kell lennie. 
* A kimenő hálózati elérési út nem haladnak keresztül belső vállalati proxyk, sem annak a helyszíni bújtatott hatályba.  Az App Service Environment-környezet így módosítja a kimenő hálózati forgalom hatékony NAT-cím.  A NAT-cím egy App Service Environment kimenő hálózati forgalom megváltoztatásakor kapcsolathibái sok a fenti végpontok.  Az eredmény App Service Environment-környezet létrehozása sikertelen kísérleteket, valamint a korábban kifogástalan App Service Environment-környezetek megfelelő állapotúként lett megjelölve.  
* Az App Service Environment-környezetek lehetővé kell tenni ezt a bejövő forgalmat kezelő hálózati eléréséhez szükséges portok [cikk][requiredports].

A DNS-követelmények érheti el, egy érvényes DNS-infrastruktúra van beállítva, és a virtuális hálózat megmarad biztosításával.  Ha bármilyen okból az App Service Environment-környezet létrehozása után a DNS-konfiguráció módosul, a fejlesztők kényszerítheti az App Service-környezet az új DNS-konfiguráció érvényesítéséhez.  Az az App Service Environment-felügyelet panel tetején található indítására, a "Restart" ikonnal működés közbeni környezet újraindítás a [Azure-portálon] [ NewPortal] okoz a környezetben, az új DNS-konfiguráció érvényesítéséhez.

A bejövő hálózati hozzáférési követelmények érheti el, úgy konfigurálja a [hálózati biztonsági csoport] [ NetworkSecurityGroups] hozzáférést a szükséges a Ez az App Service Environment alhálózaton [cikk][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Az App Service Environment-környezet kimenő hálózati kapcsolat engedélyezése
Alapértelmezés szerint egy újonnan létrehozott ExpressRoute-kapcsolatcsoportot hirdeti egy alapértelmezett útvonalat, amely lehetővé teszi a kimenő internetkapcsolat.  Ez a konfiguráció az App Service-környezetek fog tudni csatlakozni más Azure-végpontok.

Azonban egy közös felhasználói konfigurálás az, hogy a saját alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a kimenő Internet forgalmat inkább a helyszínen.  A forgalom áramlását tüntetnek megsérti App Service Environment-környezetek, mert a kimenő adatforgalmat a letiltott helyi, vagy NAT-címek, amelyek már nem használhatók a különböző Azure-végpontok egy felismerhetetlen készletéhez lenne.

A megoldás, hogy egy (vagy több) felhasználó által megadott útvonalakat (udr-EK) adja meg az alhálózatot, amely tartalmazza az App Service Environment-környezet.  Egy UDR helyett az alapértelmezett útvonal szembeni szerződéses kötelezettségeket vonatkozó alhálózati útvonalakat határozza meg.

Ha lehetséges javasoljuk, hogy az alábbi konfigurációt használja:

* Az ExpressRoute konfigurációs hirdeti 0.0.0.0/0, és alapértelmezés szerint kényszerített bújtatja minden kimenő forgalom helyszíni.
* Az App Service Environment-környezet tartalmazó alkalmazva UDR 0.0.0.0/0 az interneten (például az az ebben a cikkben le megtalálhatók) a következő ugrás típusa határozza meg.

A kombinált hatását, hogy ezeket a lépéseket az, hogy az alhálózat-szintű UDR elsőbbséget élvez az ExpressRoute kényszerített bújtatás, biztosítva ezzel az App Service Environment-környezet a kimenő Internet-hozzáféréssel.

> [!IMPORTANT]
> Egy UDR definiált útvonalak **kell** kellően specifikus elsőbbséget élveznek a bármely az ExpressRoute-konfiguráció által hirdetett útvonalakat.  Az alábbi példában a széles körű 0.0.0.0/0 címtartományt használja, és ilyen potenciálisan véletlenül felülbírálhatja útvonal-hirdetéseinek pontosabb címtartományai használatával.
> 
> App Service Environment-környezetek ExpressRoute-konfigurációk nem támogatottak, amelyek **cross-hirdetményt a magánhálózati társviszony-létesítési elérési utat a nyilvános társviszony-létesítési elérési útvonalak**.  ExpressRoute-konfigurációk, amelyek rendelkeznek a nyilvános társviszony konfigurálva, a Microsoft Azure IP-címtartományok számos útvonal-hirdetéseinek kap Microsoft.  Ha ezen címtartomány határokon meghirdetett a magánhálózati társviszony-létesítési elérési úton, a befejezési eredménye, hogy minden kimenő hálózati csomag az App Service Environment-alhálózatból kényszerített bújtatott az ügyfél a helyi hálózati infrastruktúrára lesz.  A hálózati folyamat jelenleg nem támogatott az App Service Environment-környezetek.  Egy megoldást a problémára, hogy állítsa le a kereszt-hirdetési útvonalak a nyilvános társviszony-létesítési elérési útról a magánhálózati társviszony-létesítési elérési utat.
> 
> 

Háttér-információkat a felhasználó által megadott útvonalakat érhető el ezen [áttekintése][UDROverview].  

Létrehozása és konfigurálása a felhasználó által megadott útvonalakat érhető el ezen [útmutató hogyan való][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Példa UDR konfiguráció egy App Service Environment-környezet
**Előfeltételek**

1. Az Azure Powershell telepítése az [Azure letöltőoldala] [ AzureDownloads] (dátummal June 2015-ös vagy újabb).  A "Parancssori eszközök" a "Windows Powershell", amely telepíti a legújabb Powershell-parancsmagok egy "Telepítés" kapcsolat van.
2. Javasoljuk, hogy egyedi alhálózatot kizárólagos használatra létrejön egy App Service Environment-környezet által.  Ez biztosítja, hogy a udr-EK alkalmazva lesz csak nyissa meg a kimenő forgalom az App Service Environment-környezet.
3. **Fontos**: nem kell telepítenie az App Service-környezet csak **után** követi az alábbi konfigurációs lépéseket.  Ez biztosítja, hogy kimenő hálózati kapcsolattal elérhető egy App Service Environment-környezet telepítésének megkísérlése előtt.

**1. lépés:, Hozzon létre egy elnevezett útválasztási táblázatot**

A következő kódrészletet a nyugati Velünk Azure azon régióját "DirectInternetRouteTable" nevű útvonaltábla létrehozása:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**2. lépés: Az útválasztási táblázatot hozhat létre egy vagy több útvonalakat**

Szüksége lesz egy vagy több útvonal hozzáadása az útvonaltábla kimenő Internet-hozzáférés engedélyezéséhez.  

Az ajánlott megoldás kimenő Internet-hozzáférés konfigurálásához, egy útvonalat a 0.0.0.0/0 alább látható módon.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Ne feledje, hogy a 0.0.0.0/0 a széles körű címtartományt, és így felülbírálják az ExpressRoute által hirdetett pontosabb címtartományok.  A korábbi ajánlás újra felépítésének, 0.0.0.0/0 útvonal egy UDR csak a 0.0.0.0/0 is hirdeti ExressRoute konfigurációjával együtt használhatók. 

Alternatív megoldásként letöltheti CIDR tartományok Azure használja az átfogó és frissített listáját.  Az összes, az Azure IP-címtartományokat tartalmazó XML-fájl megtalálható a a [Microsoft Download Center][DownloadCenterAddressRanges].  

Vegye figyelembe, hogy ezek a tartományok változnak az idők, állásához a felhasználó által megadott útvonalakat szinkronizálásához rendszeres manuális frissítéseket.  Is mivel az egyetlen UDR 100 útvonalat egy alapértelmezett felső korlátja, szüksége lesz a "összefoglalója" az Azure IP-címtartományok méretéhez igazítja a 100 útvonal korlátot figyelembe vételével, hogy UDR definiált útvonalak kell lenniük a pontosabb, mint az útvonalak által hirdetett az ExpressRoute.  

**3. lépés: Az útválasztási táblázatot az alhálózathoz, az App Service Environment-környezet tartalmazó társítása**

Az utolsó konfigurációs lépés a útválasztási táblázatot az alhálózathoz hozzárendelni, hova szeretné telepíteni az App Service Environment-környezet.  A következő parancsot a "DirectInternetRouteTable" a "ASESubnet", amely végül fogja tartalmazni az App Service-környezetek számára társítja.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**4. lépés: Utolsó lépéseit**

Miután az útvonaltáblát az alhálózaton van kötve, javasoljuk, hogy először tesztelése, és erősítse meg a kívánt hatást.  Például egy virtuális gép üzembe helyezés az alhálózatot, és ellenőrizze, hogy:

* Az Azure és az-Azure végpontok kimenő forgalmat ez a cikk a korábban említett **nem** le az ExpressRoute-kapcsolatcsoport továbbítására.  Nagyon fontos, hogy ellenőrizze ezt a viselkedést, mert ha az alhálózatból kimenő forgalom továbbra is kényszerített bújtatott a helyszíni App Service Environment-környezet létrehozása mindig sikertelen lesz. 
* DNS-keresések a végpontok a korábban említett, az összes megoldásán megfelelően. 

Miután a fenti lépések megerősítjük, szüksége lesz a törölje a virtuális gépet, mert az alhálózat kell "empty" az App Service Environment-környezet létrehozásakor.

Ezután folytathatja az App Service-környezetek létrehozása a!

## <a name="getting-started"></a>Első lépések
App Service Environment-környezetek megkezdéséhez, lásd: [App Service Environment bemutatása][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
