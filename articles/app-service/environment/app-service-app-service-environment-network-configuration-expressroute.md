---
title: Hálózati konfiguráció részletei az Azure expressroute-hoz
description: A hálózati konfiguráció részletei az App Service Environment-környezet a powerapps szolgáltatásra az Azure ExpressRoute-kapcsolatcsoportokkal csatlakoztatott virtuális hálózatokban.
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
ms.custom: seodec18
ms.openlocfilehash: 8dc6f595f312326f4082af9f875fefddc3a5fb8d
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320709"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Hálózati konfiguráció részletei az App Service-környezet a powerapps szolgáltatásra, az Azure ExpressRoute használatával

Ügyfelek csatlakozhatnak egy [Azure ExpressRoute] [ ExpressRoute] kapcsolatcsoport számára azok helyszíni hálózat kiterjesztése az Azure-ban virtuális hálózati infrastruktúrán. App Service Environment-környezet jön létre, egy alhálózatot a [virtuális hálózat] [ virtualnetwork] infrastruktúra. App Service Environment-környezet alkalmazások biztonságos kapcsolatot háttérbeli erőforrásokhoz, amely csak az ExpressRoute-kapcsolaton keresztül érhető el.  

App Service Environment-környezet hozható létre a következő használati helyzetekben:
- Az Azure Resource Managerbeli virtuális hálózat.
- Klasszikus üzemi modell virtuális hálózatok.
- Virtuális hálózatok, amelyek nyilvános cím-tartományok vagy az RFC1918-címtereket (vagyis a magánhálózati címek). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Szükséges hálózati kapcsolat

Az App Service Environment hálózati kapcsolatokra vonatkozó követelményeket, azonban előfordulhat, hogy nem kell teljesülnie az expressroute-hoz csatlakoztatott virtuális hálózat rendelkezik.

App Service Environment-környezet az alábbi hálózati kapcsolati beállítások megfelelő működéséhez szükségesek:

* Kimenő hálózati kapcsolat a 80-as portot és a 443-as globális Azure Storage végpontokra. Ezeket a végpontokat és App Service Environment-környezet ugyanabban a régióban, és más Azure-régiókban találhatók. Azure Storage-beli oldja meg a következő DNS-tartományok alatt: table.core.windows.net, blob.core.windows.net, queue.core.windows.net és file.core.windows.net.  

* Az Azure Files szolgáltatáshoz a 445-ös porton a kimenő hálózati kapcsolat.

* App Service Environment-környezet ugyanabban a régióban található Azure SQL Database-végpontokra irányuló kimenő hálózati kapcsolat. SQL Database végpontjainak feloldása az database.windows.net tartományba, amelyhez szükséges portokat: 1433-as, 11000-11999 és 14000-14999 nyissa meg a hozzáférést. Az SQL Database V12-es port használatával kapcsolatos részletekért lásd: [az ADO.NET 4.5 szoftverrel 1433-Ason túli](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Kimenő hálózati kapcsolat és az Azure felügyeleti sík-végpontok (klasszikus Azure üzemi modell és az Azure Resource Manager-végpontok). Ezek a végpontokra irányuló a management.core.windows.net és management.azure.com tartományt tartalmaz. 

* Kimenő hálózati kapcsolat a ocsp.msocsp.com mscrl.microsoft.com és crl.microsoft.com tartományokhoz. Ezekből a tartományokból való kapcsolódás az SSL-funkciók támogatásához van szükség.

* A virtuális hálózat DNS-konfigurációját kell tudni oldania minden végpontok és a jelen cikkben említett tartomány. A végpontok nem oldható fel, ha az App Service-környezet létrehozása sikertelen lesz. Minden olyan meglévő App Service Environment-környezet állapotúként van megjelölve.

* 53-as porton a kimenő hozzáférést a DNS-kiszolgálókkal való kommunikációhoz szükséges.

* Ha egyéni DNS-kiszolgálót a VPN-átjáró másik végén, a DNS-kiszolgáló elérhető, az alhálózatról, amely tartalmazza az App Service Environment-környezet kell lennie. 

* A kimenő hálózati elérési út keresztül belső vállalati proxyk nem utaznak, és nem lehet a kényszerített bújtatásos helyszíni. Ezeket a műveleteket az App Service Environment-környezet kimenő hálózati forgalom hatékony NAT-címének módosítása. Az App Service Environment-környezet kimenő hálózati forgalom a NAT-címének módosítása hatására a csatlakozási hibák nagy része a végpontok. Az App Service Environment-környezet létrehozása sikertelen lesz. Minden olyan meglévő App Service Environment-környezet állapotúként van megjelölve.

* Engedélyezni kell az App Service Environment-környezet számára szükséges portok bejövő hálózati hozzáférést. További információkért lásd: [az App Service Environment-környezet bejövő forgalom szabályozása][requiredports].

A DNS-követelmények teljesítéséhez, győződjön meg arról, hogy egy érvényes DNS-infrastruktúra van konfigurálva, és a virtuális hálózat karbantartása. Ha a DNS-konfigurációt az App Service Environment-környezet létrehozása után módosul, a fejlesztők kényszerítheti az App Service-környezet folytattuk a munkát az új DNS-konfiguráció. Működés közbeni környezet újraindítás használatával is aktiválhatja a **indítsa újra a** ikon mellett az App Service Environment-kezelés az [Azure Portalról] [NewPortal]. Az újraindítás a környezet átvételéhez az új DNS-konfiguráció okozza.

A bejövő hálózati hozzáférési követelmények teljesítéséhez, állítson be egy [hálózati biztonsági csoport (NSG)][NetworkSecurityGroups] App Service Environment-környezet az alhálózaton. Az NSG-t lehetővé teszi, hogy a szükséges hozzáféréssel [az App Service Environment-környezet bejövő forgalom szabályozása][requiredports].

## <a name="outbound-network-connectivity"></a>Kimenő hálózati kapcsolat

Alapértelmezés szerint egy újonnan létrehozott ExpressRoute-kapcsolatcsoport hirdeti meg egy alapértelmezett útvonalat, amely lehetővé teszi a kimenő internetkapcsolat. App Service Environment-környezet használhatja ezt a konfigurációt szeretne csatlakozni a többi Azure-beli.

Egy közös ügyfél-konfigurációt, hogy a saját alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a kimenő internetes forgalmat a helyszíni folyamat meghatározása. A forgalom áramlását tüntetnek megsérti az App Service Environment-környezet. A kimenő forgalmat a letiltott helyi vagy a NAT-címek, amelyek többé nem működnek együtt a különböző Azure-beli felismerhetetlen készletéhez lenne.

A megoldás, ha egy (vagy több) felhasználó által megadott útvonalak (udr-EK) meg az App Service-környezetet tartalmazó alhálózat. Egy udr-t, amely az összes régió megfelel a helyett az alapértelmezett útvonal alhálózati-specifikus útvonalak határozza meg.

Ha lehetséges használja a következő konfigurációt:

* Az ExpressRoute-konfiguráció hirdeti meg a 0.0.0.0/0. A konfiguráció hatályos alapértelmezés szerint minden kimenő forgalmat a helyszíni-alagutak.
* A alkalmazni az alhálózatra, amely tartalmazza az App Service Environment-környezet udr-t a 0.0.0.0/0, internet következő ugrási típusú az határozza meg. Ez a konfiguráció egy példát a cikk későbbi részében leírt.

Ez a konfiguráció kombinált hatását, hogy az alhálózat-szintű UDR elsőbbséget élvez az ExpressRoute kényszerített bújtatást. Garantált az App Service Environment-környezet kimenő internet-hozzáférést.

> [!IMPORTANT]
> Egy UDR-ben meghatározott kell lenniük, hogy az ExpressRoute-konfiguráció által meghirdetett bármely útvonallal szemben elsőbbséget. A következő szakaszban ismertetett példa a széles 0.0.0.0/0 címtartományt. További címtartományok használó útvonalhirdetést véletlenül felülbírálhatja ezt a tartományt.
> 
> App Service Environment-környezet ExpressRoute-konfigurációk, amelyek keresztbe hirdetnek útvonalakat a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonal nem támogatott. A konfigurált nyilvános társviszonyt rendelkezik ExpressRoute-konfigurációk útvonalhirdetéseket kapnak a Microsoft számára a Microsoft Azure IP-címtartományok nagy készletét. Ha a címtartományokat keresztbe hirdetik a privát társviszony-létesítési útvonal, az összes az App Service Environment-környezet alhálózatából származó kimenő hálózati csomag kényszerített bújtatással jut el az ügyfél helyszíni hálózati infrastruktúrájába. Ezt a hálózati kialakítást jelenleg nem támogatott az App Service Environment-környezet. Egy megoldás, ha leállítja az útvonalak keresztbe hirdetését a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési elérési utat.
> 
> 

Felhasználó által megadott útvonalakkal kapcsolatos háttér-információkat lásd: [virtuális hálózat forgalmának útválasztása][UDROverview].  

Ismerje meg, hogyan hozhat létre és konfigurálja a felhasználó által megadott útvonalakat, lásd: [hálózati forgalom továbbítása PowerShell-lel útválasztási táblázattal] [UDRHowTo].

## <a name="udr-configuration"></a>UDR-konfiguráció

Ez a szakasz bemutatja UDR konfiguráció például az App Service Environment-környezet.

### <a name="prerequisites"></a>Előfeltételek

* Azure PowerShell telepítése az [Azure letöltések lapon] [AzureDownloads]. Válassza ki a letölteni egy dátummal June 2015 vagy újabb verzió. Alatt **parancssori eszközök** > **Windows PowerShell**válassza **telepítése** , telepítse a legújabb PowerShell-parancsmagokat.

* Hozzon létre egy App Service Environment-környezet használja kizárólagos egyedi alhálózatot. Az egyedi alhálózati biztosítja, hogy a alkalmazni az alhálózat nyitott kimenő forgalom App Service-környezetben csak udr-EK.

> [!IMPORTANT]
> App Service Environment-környezet csak telepíteni a konfigurációs lépések végrehajtása után. A lépések győződjön meg arról, kimenő hálózati kapcsolat érhető el, mielőtt megpróbálná üzembe helyezése az App Service Environment-környezet.

### <a name="step-1-create-a-route-table"></a>1. lépés: Útválasztási táblázat létrehozása

Hozzon létre egy útválasztási táblázatot nevű **DirectInternetRouteTable** a West US Azure-régióban, ebben a kódrészletben látható módon:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>2. lépés: A tábla útvonalak létrehozása

Adja hozzá az útvonalakat az útvonaltáblához kimenő internet-hozzáférés engedélyezéséhez.  

Kimenő internet-hozzáférés konfigurálása. Adja meg egy útvonalat a 0.0.0.0/0, ebben a kódrészletben látható módon:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 széles körű címtartomány. A tartomány címtartományok ExpressRoute hirdeti meg, hogy a rendszer felülbírálja. Egy udr-t a 0.0.0.0/0 útvonalat egy ExpressRoute-konfigurációt, amelynek segítségével meghirdetheti 0.0.0.0/0 csak együtt kell használni. 

Alternatív megoldásként letöltheti a CIDR-tartományt használja az Azure aktuális, átfogó listáját. Az összes Azure IP-címtartományok esetében az XML-fájlt a [Microsoft Download Center] [DownloadCenterAddressRanges] érhető el.  

> [!NOTE]
>
> Az Azure IP-címtartományok idővel változni. Felhasználó által megadott útvonalak szinkronizálásához rendszeres manuális frissítést kell.
>
> Egyetlen UDR 100 útvonal alapértelmezett felső határral rendelkezik. "Összegzés" méretéhez igazítja a 100-route korlát Azure IP-címtartományokat kell. UDR által megadott útvonalak kell lennie, mint az ExpressRoute-kapcsolat által meghirdetett útvonalak pontosabb.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>3. lépés: A táblázat az alhálózat társítása

Társítsa az útválasztási táblázatot az alhálózathoz, ahol szeretné telepíteni az App Service Environment-környezet. Ezzel a paranccsal összekapcsolja a **DirectInternetRouteTable** táblázatból a **ASESubnet** alhálózatot, amelyet az App Service Environment-környezet tartalmazza.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>4. lépés: Tesztelje, és ellenőrizze az útvonal

Miután az útválasztási táblázatot az alhálózathoz van kötve, tesztelje, és ellenőrizze az útvonal.

Egy virtuális gépet helyezze üzembe az alhálózaton, és ezeknek a feltételeknek megerősítéséhez:

* Az Azure és a nem Azure-beli végpontokat ebben a cikkben leírt irányuló kimenő forgalom does **nem** folyamat az ExpressRoute-kapcsolatcsoport le. Ha az alhálózatról kimenő forgalom kényszerített bújtatásos a helyszínen, mindig az App Service-környezet létrehozása sikertelen lesz.
* DNS-keresések az összes megfelelően feloldani ebben a cikkben leírt végpontokhoz. 

Miután a konfigurációs lépéseket, és erősítse meg az útvonalat, törölje a virtuális gépet. Az alhálózat üresnek kell lennie "" App Service Environment-környezet létrehozásakor.

Most már üzembe helyezése az App Service Environment-környezet készen áll!

## <a name="next-steps"></a>További lépések

Ismerkedés az App Service Environment-környezet a PowerApps, lásd: [App Service Environment bemutatása] [IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[networkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
<!-- Old link -- [UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ --> [UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell [HowToCreateAnAppServiceEnvironment]: [AzureDownloads] app-service-web-how-to-create-an-app-service-environment.md: https://azure.microsoft.com/downloads/ [DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ [IntroToAppServiceEnvironment]: [NewPortal] app-service-app-service-environment-intro.md:  https://portal.azure.com


<!-- IMAGES -->
