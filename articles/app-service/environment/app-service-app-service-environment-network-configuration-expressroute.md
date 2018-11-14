---
title: Hálózati konfiguráció részletei az Express route-tal dolgozik
description: Hálózati konfiguráció részletei az App Service Environment-környezetek futtatásához egy virtuális hálózatot egy ExpressRoute-Kapcsolatcsoporthoz csatlakozik.
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
ms.openlocfilehash: 7873192e4a66cd2faed5a1a1255377139d33d750
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616061"
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Az ExpressRoute-kapcsolattal rendelkező App Service Environments-környezetek hálózati konfigurációjának részletei
## <a name="overview"></a>Áttekintés
Ügyfelek csatlakozhatnak egy [Azure ExpressRoute] [ ExpressRoute] kapcsolatcsoport virtuális hálózati infrastruktúrán, így kiterjesztése a helyszíni hálózat az Azure-bA.  App Service-környezet hozható létre, ez alhálózatán [virtuális hálózat] [ virtualnetwork] infrastruktúra.  Az App Service-környezetben futó alkalmazások majd csak az ExpressRoute-kapcsolaton keresztül elérhető háttér-erőforrásokhoz való biztonságos kapcsolatot is létrehozhat.  

Az App Service-környezet hozható létre **vagy** egy Azure Resource Managerbeli virtuális hálózat **vagy** a klasszikus üzemi modellt használó virtuális hálózatokhoz.  Egy friss változás 2016. június végzett, az ASE is most már üzembe helyezhető nyilvános címtartományok, vagy az RFC1918 címterek (azaz a magánhálózati címek) használó virtuális hálózatokra. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Szükséges hálózati kapcsolat
Nincsenek App Service Environment-környezetek, amelyeket lehet, hogy nem kezdetben teljesíteni egy virtuális hálózaton, expressroute-csatlakoztatott hálózati kapcsolatokra vonatkozó követelményeket.  App Service Environment-környezetek megfelelő működéséhez szükséges a következő lépéseket:

* Azure Storage végpontokra világszerte mindkét 80-as és 443-as porton a kimenő hálózati kapcsolat.  Ez magában foglalja a végpontok és az App Service-környezet ugyanabban a régióban található, valamint a tárolási végpontok található **más** Azure-régióban.  Azure Storage-beli oldja meg a következő DNS-tartományok alatt: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* és *file.core.windows.net*.  
* Az Azure Files szolgáltatáshoz a 445-ös porton a kimenő hálózati kapcsolat.
* Kimenő hálózati kapcsolat és az Sql DB végpont és az App Service-környezet ugyanabban a régióban található.  Az SQL DB végpontok oldja meg a következő tartományba: *database.windows.net*.  Ehhez nyissa meg az 1433-as, 11000-11999 és 14000-14999 portok a hozzáférést.  További információ: [Ez a cikk az Sql Database V12-es port-használati](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Kimenő hálózati kapcsolat az Azure felügyeleti sík végpontjaira (ASM és ARM is végpontok).  Ez magában foglalja a kimenő kapcsolat mindkét *management.core.windows.net* és *management.azure.com*. 
* A kimenő hálózati kapcsolat *ocsp.msocsp.com*, *mscrl.microsoft.com* és *crl.microsoft.com*.  Erre azért van szükség az SSL-funkciót.
* A DNS-konfiguráció a virtuális hálózat összes végpontok és a korábbi abban tartományokat megoldásának képesnek kell lennie.  A végpontok nem oldható fel, ha az App Service Environment-környezet létrehozása kísérletek sikertelenek lesznek, és meglévő App Service Environment-környezetek lesznek megjelölve nem megfelelő.
* 53-as porton a kimenő hozzáférést a DNS-kiszolgálókkal való kommunikációhoz szükséges.
* Ha egyéni DNS-kiszolgálót a VPN-átjáró másik végén, a DNS-kiszolgáló az alhálózatot, amely tartalmazza az App Service Environment elérhetőnek kell lennie. 
* A kimenő hálózati elérési út keresztül belső vállalati proxyk nem utaznak, és nem is lehet kényszerített bújtatással jut el a helyszíni.  Ezzel módosítja az App Service-környezet kimenő hálózati forgalom hatékony NAT-címét.  Egy App Service-környezet kimenő hálózati forgalmat, NAT-címének módosítása esetén a fenti végpontok számos csatlakozási hibák.  Ennek eredményeképpen az App Service Environment-környezet létrehozása sikertelen kísérletek, valamint a korábban megfelelő App Service-környezetek nem megfelelő állapotát folyamatban van megjelölve.  
* Bejövő hálózati hozzáférés szükséges portokat az App Service Environment-környezetek engedélyezni kell a leírtak szerint [cikk][requiredports].

A DNS-követelmények úgy, hogy egy érvényes DNS-infrastruktúra karbantartása a virtuális hálózat és konfigurált érheti el.  Ha bármilyen okból a DNS-konfigurációt egy App Service Environment-környezet létrehozása után módosul, a fejlesztők kényszerítheti egy App Service Environment folytattuk a munkát az új DNS-konfiguráció.  Az az App Service Environment-felügyelet panel tetején található "Restart" ikonra működés közbeni környezet újraindítás elindítása a [az Azure portal] [ NewPortal] okoz az új DNS-t választja ki a környezet konfiguráció.

A bejövő hálózati hozzáférési követelmények is kielégíthetők konfigurálása egy [hálózati biztonsági csoport] [ NetworkSecurityGroups] az App Service Environment-alhálózaton, hogy a szükséges hozzáféréssel, ez leírtak szerint [ a cikk][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Az App Service-környezet kimenő hálózati kapcsolat engedélyezése
Alapértelmezés szerint egy újonnan létrehozott ExpressRoute-kapcsolatcsoport hirdeti meg egy alapértelmezett útvonalat, amely lehetővé teszi a kimenő internetkapcsolat.  Ez a konfiguráció az App Service Environment tud csatlakozni a többi Azure-beli lesz.

Viszont egy közös ügyfél-konfigurációt, hogy adja meg a saját alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a kimenő internetes forgalmat inkább a helyszíni.  A forgalom áramlását tüntetnek működésképtelenné válik az App Service Environment-környezetek, mert a kimenő forgalmat a letiltott helyi vagy NAT-címek, amelyek többé nem működnek együtt a különböző Azure-beli felismerhetetlen készletéhez lenne.

A megoldás, ha egy (vagy több) felhasználó által megadott útvonalakat (udr-EK) határozzák meg az alhálózatot, amelyet az App Service-környezet tartalmazza.  Egy UDR határozza meg az alhálózat-specifikus útvonalakat, amely az alapértelmezett útvonal helyett lesznek érvényesek.

Ha lehetséges javasoljuk, hogy az alábbi konfigurációt használja:

* Az ExpressRoute-konfiguráció hirdet 0.0.0.0/0, és alapértelmezés szerint kényszerített alagutak minden kimenő forgalmat a helyszíni.
* Az udr-t az alhálózathoz, amely tartalmazza az App Service-környezet a alkalmazni egy következő ugrási típusú internettel (ilyen például a brazíliáénál le ebben a cikkben) a 0.0.0.0/0 határozza meg.

Ezeket a lépéseket a kombinált hatását, hogy az alhálózat-szintű UDR elsőbbséget élvez az expressroute-on keresztül kényszerített bújtatást végez, ezáltal biztosítható az App Service Environment-környezet kimenő Internet-hozzáférést.

> [!IMPORTANT]
> Egy UDR-ben meghatározott **kell** lenniük, hogy az ExpressRoute-konfiguráció által meghirdetett bármely útvonallal szemben elsőbbséget.  Az alábbi példa a széles 0.0.0.0/0 címtartományt használja, és mint ilyen potenciálisan véletlenül felülbírálhatja használatával pontosabb címtartományok útvonallal kapcsolatos hirdetéseket.
> 
> App Service Environment-környezetek nem támogatottak olyan ExpressRoute-konfigurációk, amelyek **keresztbe hirdetnek útvonalakat a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonal**.  ExpressRoute-konfigurációk, amelyek rendelkeznek, a konfigurált nyilvános társviszonyt fog útvonalhirdetéseket kapnak a Microsoft számára a Microsoft Azure IP-címtartományok nagy készletét.  Ha a címtartományokat keresztbe hirdetik a privát társviszony-létesítési útvonal, a végeredmény, hogy összes az App Service-környezet alhálózatából származó kimenő hálózati csomag kényszerített bújtatással az ügyfél helyszíni hálózati infrastruktúrájába lesz.  Ezt a hálózati kialakítást jelenleg nem támogatott az App Service-környezetek.  Egy megoldás erre a problémára, ha leállítja az útvonalak keresztbe hirdetését a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési elérési utat.
> 
> 

Háttér-információkat a felhasználó által megadott útvonalakat érhető el a jelen [áttekintése][UDROverview].  

Részletes létrehozása és konfigurálása a felhasználó által megadott útvonalakat érhető el a jelen [hogyan az útmutató][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Példa UDR konfigurációja App Service-környezet
**Előfeltételek**

1. Az Azure Powershell telepítéséhez a [Azure letöltőoldala] [ AzureDownloads] (dátummal ellátott a június 2015 vagy újabb).  Az "Parancssori eszközök" nincs "Windows Powershell", amely telepíti a legújabb Powershell-parancsmagok egy "Telepítés" hivatkozásra kattintva.
2. Javasoljuk, hogy egyedi alhálózatot kizárólagos használatra létrejött az App Service Environment által.  Ez biztosítja, hogy az udr-eket a alhálózatra alkalmazott fogja csak nyissa meg a kimenő forgalmat az App Service-környezetben.
3. **Fontos**: ne telepítse az App Service Environment-ig **után** teljesülnek az alábbi konfigurációs lépéseket.  Ez biztosítja, hogy kimenő hálózati kapcsolat elérhető App Service-környezet telepítése előtt.

**1. lépés: A megnevezett útválasztási táblázat létrehozása**

Az alábbi kódrészlet létrehoz egy útvonaltáblát, úgynevezett "DirectInternetRouteTable" West US Azure régióban:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**2. lépés: Hozzon létre egy vagy több útvonalat az útvonaltáblában lévő**

Szüksége lesz egy vagy több útvonal hozzáadása az útválasztási táblázatot, és engedélyezze a kimenő Internet-hozzáférést.  

Az ajánlott módszer a kimenő Internet-hozzáférés konfigurálása, hogy egy útvonalat a 0.0.0.0/0 alább látható módon.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Ne feledje, hogy a 0.0.0.0/0 széles körű címtartomány, és mint ilyen felülbírálják az ExpressRoute hirdeti meg konkrétabb címtartományok.  Újra újrafuttathatja a korábbi ajánlás, egy udr-t a 0.0.0.0/0 útvonalat egy ExpressRoute-konfigurációt, amely csak a 0.0.0.0/0 is meghirdeti együtt használandó. 

Alternatív megoldásként letöltheti a CIDR-tartományt használja az Azure átfogó és frissített listáját.  Az Xml-fájlt tartalmazó összes az Azure IP-címtartományok érhető el a [Microsoft Download Center][DownloadCenterAddressRanges].  

Vegye figyelembe ugyanakkor, hogy ezek a tartományok időbeli változásait, állásához való szinkronizálásához a felhasználó által megadott útvonalakat rendszeres manuális frissítést.  Ezenkívül mivel 100 útvonal egy UDR az alapértelmezett felső korlát, "Összegzés" méretéhez igazítja a 100 útvonal korlátot, az Azure IP-címtartományokat kell, hogy UDR definiált útvonalakat kell lennie, mint az útvonalak pontosabb szem által hirdetett a ExpressRo ute.  

**3. lépés: Az útválasztási táblázatot az alhálózathoz, amely tartalmazza az App Service Environment társítása**

Az utolsó konfigurációs lépéssel, hogy az alhálózathoz társít, ahol az App Service-környezet üzembe helyezve.  A következő parancsot a "DirectInternetRouteTable" a "ASESubnet", amely végül fogja tartalmazni az App Service-környezet a társítja.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**4. lépés: Utolsó lépéseit**

Miután az útválasztási táblázatot az alhálózathoz van kötve, ajánlott először teszteléséhez, és erősítse meg a kívánt hatást.  Ha például egy virtuális gépet helyezze üzembe az alhálózaton, és ellenőrizze, hogy:

* Azure és a nem Azure-végpontokra irányuló kimenő forgalom korábban említettük, ez a cikk **nem** le az ExpressRoute-kapcsolatcsoport tárfiókba.  Nagyon fontos, ellenőrizze ezt a viselkedést, mivel az alhálózatról kimenő forgalom továbbra is kényszerített bújtatása a helyszínen, App Service-környezet létrehozása mindig sikertelen lesz. 
* A végpontok DNS-keresések korábban említettük, az összes feloldása megfelelően. 

Miután a fenti lépések megerősítik, szüksége lesz törölni a virtuális gép, mert az alhálózat üresnek kell lennie "" az App Service-környezet létrehozásakor.

Majd folytassa az App Service Environment létrehozása!

## <a name="getting-started"></a>Első lépések
Első lépések az App Service Environment-környezetek, lásd: [App Service Environment bemutatása][IntroToAppServiceEnvironment]

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
