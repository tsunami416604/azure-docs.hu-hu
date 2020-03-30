---
title: Az Azure ExpressRoute 1-es v1-es v1-es gépei
description: Az Azure ExpressRoute alkalmazásszolgáltatási környezetéhez szükséges hálózati konfiguráció. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 8a83c2f6ac7599ff37237834a85b7771cf4ee502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243873"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Az Azure ExpressRoute-alapú App Service-környezet hálózati konfigurációs adatai a PowerApps alkalmazáskörnyezetéhez

Az ügyfelek csatlakoztathatnak egy [Azure ExpressRoute-áramkört][ExpressRoute] a virtuális hálózati infrastruktúrájukhoz, hogy kibővíthessék helyszíni hálózatukat az Azure-ra. Az App Service-környezet a [virtuális hálózati][virtualnetwork] infrastruktúra alhálózatában jön létre. Az App Service-környezetben futó alkalmazások biztonságos kapcsolatokat hoznak létre olyan háttér-erőforrásokkal, amelyek csak az ExpressRoute-kapcsolaton keresztül érhetők el.  

Az App Service-környezet a következő esetekben hozható létre:
- Az Azure Resource Manager virtuális hálózatai.
- Klasszikus üzembe helyezési modell virtuális hálózatok.
- Nyilvános címtartományokat vagy RFC1918 címtereket (azaz magáncímeket) használó virtuális hálózatok. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Szükséges hálózati kapcsolat

Az App Service-környezet hálózati kapcsolati követelményekkel rendelkezik, amelyek kezdetben nem teljesülnek az ExpressRoute-hoz csatlakoztatott virtuális hálózatban.

Az App Service-környezet megfelelő működéséhez a következő hálózati kapcsolati beállítások szükségesek:

* Kimenő hálózati kapcsolat az Azure Storage végpontjaihoz világszerte a 80-as és a 443-as porton is. Ezek a végpontok ugyanabban a régióban találhatók, mint az App Service Environment és más Azure-régiók. Az Azure Storage-végpontok feloldása a következő DNS-tartományok alatt: table.core.windows.net, blob.core.windows.net, queue.core.windows.net és file.core.windows.net.  

* Kimenő hálózati kapcsolat az Azure Files szolgáltatáshoz a 445-ös porton.

* Kimenő hálózati kapcsolat az Azure SQL Database végpontok, amelyek ugyanabban a régióban található, mint az App Service-környezet. Az SQL Database végpontjai a database.windows.net tartomány ban oldódnak fel, amely nyílt hozzáférést igényel az 1433-as, 11000-11999-es és 14000-14999-es portokhoz. Az SQL Database V12 porthasználatáról a [Portok 1433-nál ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)című témakörben talál további információt.

* Kimenő hálózati kapcsolat az Azure felügyeleti sík végpontjaihoz (mind az Azure klasszikus üzembe helyezési modelljéhez, mind az Azure Resource Manager végpontjaihoz). Ezekhez a végpontokhoz való kapcsolódás magában foglalja a management.core.windows.net és management.azure.com tartományok. 

* Kimenő hálózati kapcsolat a ocsp.msocsp.com, mscrl.microsoft.com és crl.microsoft.com tartományokhoz. Az SSL-funkciók támogatásához ezekhez a tartományokhoz való kapcsolódás szükséges.

* A virtuális hálózat DNS-konfigurációjának képesnek kell lennie a jelen cikkben említett összes végpont és tartomány feloldására. Ha a végpontok nem oldhatók fel, az App Service-környezet létrehozása sikertelen lesz. Minden meglévő App Service-környezet sérültként van megjelölve.

* Az 53-as portkimenő hozzáférése szükséges a DNS-kiszolgálókkal való kommunikációhoz.

* Ha egy VPN-átjáró másik végén létezik egyéni DNS-kiszolgáló, a DNS-kiszolgálónak elérhetőnek kell lennie az App Service-környezetet tartalmazó alhálózatról. 

* A kimenő hálózati elérési út nem tud utazni a belső vállalati proxykon keresztül, és nem lehet a helyszínen bújtatni. Ezek a műveletek módosítják az App Service-környezetből érkező kimenő hálózati forgalom tényleges NAT-címét. Az App Service-környezet kimenő hálózati forgalmának NAT-címének módosításai számos végpont kapcsolódási hibáját okozzák. Az App Service-környezet létrehozása sikertelen. Minden meglévő App Service-környezet sérültként van megjelölve.

* Engedélyezni kell az App Service Environment szükséges portjaihoz való bejövő hálózati hozzáférést. További információt az [App Service-környezetbe irányuló bejövő forgalom szabályozása című témakörben talál.][requiredports]

A DNS-követelmények teljesítéséhez győződjön meg arról, hogy érvényes DNS-infrastruktúra van konfigurálva és karbantartva a virtuális hálózathoz. Ha az App Service Environment létrehozása után megváltozik a DNS-konfiguráció, a fejlesztők kényszeríthetik az App Service Environment alkalmazást az új DNS-konfiguráció felvételére. A működés közbeni környezet újraindítása az Azure-környezet kezelése az Azure Portalon az **Újraindítás** ikonhasználatával indítható [el.][NewPortal] Az újraindítás hatására a környezet felveszi az új DNS-konfigurációt.

A bejövő hálózati hozzáférési követelmények teljesítéséhez konfiguráljon egy [hálózati biztonsági csoportot (NSG)][NetworkSecurityGroups] az App Service-környezet alhálózatán. Az NSG lehetővé teszi a szükséges hozzáférést [az App Service-környezetbe irányuló bejövő forgalom szabályozásához.][requiredports]

## <a name="outbound-network-connectivity"></a>Kimenő hálózati kapcsolat

Alapértelmezés szerint egy újonnan létrehozott ExpressRoute-kapcsolat egy alapértelmezett útvonalat hirdet, amely lehetővé teszi a kimenő internetkapcsolatot. Az App Service Environment ezt a konfigurációt használhatja más Azure-végpontokhoz való csatlakozáshoz.

A közös ügyfélkonfiguráció saját alapértelmezett útvonaluk (0.0.0.0/0) meghatározása, amely a kimenő internetes forgalmat a helyszíni forgalomra kényszeríti. Ez a forgalom mindig megtöri az App Service-környezetet. A kimenő forgalom vagy le van tiltva a helyszínen, vagy NAT'd egy felismerhetetlen címkészlet, amely már nem működik a különböző Azure-végpontok.

A megoldás egy (vagy több) felhasználó által definiált útvonal (UDRs) definiálása az App Service-környezetet tartalmazó alhálózaton. Az UDR az alapértelmezett útvonal helyett a betartott alhálózati útvonalakat határozza meg.

Ha lehetséges, használja a következő konfigurációt:

* Az ExpressRoute-konfiguráció 0.0.0.0/0-t hirdet. Alapértelmezés szerint a konfigurációs kényszerítés iarati összes kimenő forgalmat a helyszínen.
* Az App Service-környezetet tartalmazó alhálózatra alkalmazott UDR 0.0.0.0/0-t határoz meg a következő ugrástípusú internettel. Erre a konfigurációra a cikk későbbi részében ismertetünk egy példát.

Ennek a konfigurációnak a kombinált hatása az, hogy az alhálózati szintű UDR elsőbbséget élvez az ExpressRoute kényszerítési bújtatásával szemben. Az App Service-környezetből származó kimenő internet-hozzáférés garantált.

> [!IMPORTANT]
> Az UDR-ben definiált útvonalaknak elég konkrétnak kell lenniük ahhoz, hogy elsőbbséget élvezzenek az ExpressRoute-konfiguráció által hirdetett útvonalakkal szemben. A következő szakaszban ismertetett példa a széles 0.0.0.0/0 címtartományt használja. Ezt a tartományt véletlenül felülbírálhatják az útvonalhirdetések, amelyek konkrétabb címtartományokat használnak.
> 
> Az App Service-környezet nem támogatott az ExpressRoute-konfigurációk, amelyek kereszthirdetik az útvonalakat a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonalra. A nyilvános társviszony-létesítést konfigurált ExpressRoute-konfigurációk útvonalhirdetéseket kapnak a Microsofttól a Microsoft Azure IP-címtartományok nagy készletéhez. Ha ezeket a címtartományokat kereszthirdeti a privát társviszony-létesítési útvonalon, az App Service-környezet alhálózatából érkező összes kimenő hálózati csomag az ügyfél helyszíni hálózati infrastruktúrájának létesítési kényszerbújtatása. Ez a hálózati folyamat jelenleg nem támogatott az App Service-környezetben. Az egyik megoldás az, hogy állítsa le a kereszt-hirdetési útvonalak a nyilvános társviszony-létesítési útvonala a privát társviszony-létesítési útvonal.
> 
> 

A felhasználó által definiált útvonalakkal kapcsolatos háttér-információkat a [Virtuális hálózati forgalom útválasztása][UDROverview]című témakörben talál.  

A felhasználó által definiált útvonalak létrehozásáról és konfigurálásáról a [Hálózati forgalom útvonaltáblával történő irányítása a PowerShell használatával című témakörben olvashat.][UDRHowTo]

## <a name="udr-configuration"></a>UDR-konfiguráció

Ez a szakasz egy udr-konfigurációt mutat be az App Service-környezetben.

### <a name="prerequisites"></a>Előfeltételek

* Telepítse az Azure PowerShellt az [Azure Letöltések lapról.][AzureDownloads] Válasszon egy 2015 júniusi vagy újabb dátumot. A**Windows PowerShell** **parancssori eszközök** > csoportban válassza a Telepítés **lehetőséget** a legújabb PowerShell-parancsmagok telepítéséhez.

* Hozzon létre egy egyedi alhálózatot az App Service Environment kizárólagos használatára. Az egyedi alhálózat biztosítja, hogy az alhálózatra alkalmazott udrs csak az App Service-környezet számára nyitott kimenő forgalmat alkalmazva.

> [!IMPORTANT]
> Csak az App Service-környezet telepítése a konfigurációs lépések befejezése után. A lépések biztosítják, hogy a kimenő hálózati kapcsolat elérhető legyen az App Service-környezet telepítése előtt.

### <a name="step-1-create-a-route-table"></a>1. lépés: Útvonaltábla létrehozása

Hozzon létre egy **DirectInternetRouteTable** nevű útvonaltáblát az USA nyugati régiójában, ahogy az a kódrészletben látható:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>2. lépés: Útvonalak létrehozása a táblázatban

Adja hozzá az útvonalakat az útvonaltáblához a kimenő internet-hozzáférés engedélyezéséhez.  

Konfigurálja a kimenő internet-hozzáférést. Adjon meg egy útvonalat a 0.0.0.0/0-hoz, ahogy az ebben a kódrészletben látható:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

A 0.0.0.0/0 a címtartomány széles tartománya. A tartományt az ExpressRoute által hirdetett, pontosabb címtartományok bírálják felül. A 0.0.0.0/0 útvonallal rendelkező UDR-t egy expressroute-konfigurációval együtt kell használni, amely csak 0.0.0.0/0-t hirdet. 

Alternatív megoldásként töltse le az Azure által használt CIDR-tartományok aktuális, átfogó listáját. Az összes Azure IP-címtartomány XML-fájlja a [Microsoft letöltőközpontjából][DownloadCenterAddressRanges]érhető el.  

> [!NOTE]
>
> Az Azure IP-címtartományok idővel változnak. A felhasználó által definiált útvonalakon rendszeres manuális frissítésekre van szükség a szinkronizáláshoz.
>
> Egyetlen UDR alapértelmezett felső határa 100. Az Azure IP-címtartományokat "összegeznie" kell a 100 útvonalkorláton belül. Az UDR által definiált útvonalaknak specifikusabbnak kell lenniük, mint az ExpressRoute-kapcsolat által hirdetett útvonalaknak.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>3. lépés: A tábla társítása az alhálózathoz

Társítsa az útvonaltáblát ahhoz az alhálózathoz, amelyen telepíteni kívánja az App Service-környezetet. Ez a parancs a **DirectInternetRouteTable** táblát az App Service-környezetet tartalmazó **ASESubnet** alhálózathoz társítja.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>4. lépés: Az útvonal tesztelése és megerősítése

Miután az útvonaltábla az alhálózathoz van kötve, tesztelje és erősítse meg az útvonalat.

Virtuális gép üzembe helyezése az alhálózatba, és erősítse meg ezeket a feltételeket:

* Az Azure-ba és a nem Azure-végpontok ebben a cikkben ismertetett kimenő forgalom **nem** folyik le az ExpressRoute-kapcsolaton. Ha az alhálózatról érkező kimenő forgalom a helyszíni, a kontalárendelt kényszerítésű, az App Service-környezet létrehozása mindig sikertelen lesz.
* A jelen cikkben ismertetett végpontok DNS-keresései mind megfelelően oldódnak fel. 

Miután elvégezte a konfigurációs lépéseket, és megerősítette az útvonalat, törölje a virtuális gépet. Az alhálózatnak "üresnek" kell lennie az App Service-környezet létrehozásakor.

Most már készen áll az App Service Environment telepítésére!

## <a name="next-steps"></a>További lépések

A PowerApps App Service-környezettel való ismerkedésről [az App Service-környezet – Bevezetés című][IntroToAppServiceEnvironment]témakörben található.

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
