---
title: "Az App Service Environment-környezetek hálózati architektúrájának áttekintése"
description: "Hálózati topológia ofApp Service-környezetek architektúra áttekintése."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: 3362a55524da42914681db06b8d2c0da8df773d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Az App Service Environment-környezetek hálózati architektúrájának áttekintése
## <a name="introduction"></a>Bevezetés
App Service Environment-környezetek mindig létrejönnek az alhálózat egy [virtuális hálózati] [ virtualnetwork] -egy App Service Environment-környezetben futó alkalmazások képes kommunikálni az azonos virtuális hálózati topológia belül található titkos végpontok.  Mivel az ügyfelek zárolását, előfordulhat, hogy a virtuális hálózati infrastruktúra részeit, fontos a ismerteti, amelyek egy App Service Environment-környezet a hálózati kommunikációs forgalom.

## <a name="general-network-flow"></a>Általános hálózati folyamata
Amikor egy App Service környezetben (ASE) alkalmazások a nyilvános virtuális IP-cím (VIP) használja, minden bejövő forgalom érkezik, a adott nyilvános VIP-e.  Ez magában foglalja az alkalmazások a HTTP és HTTPS-forgalom, valamint a típusú forgalom FTP, távoli hibakeresési szolgáltatás és az Azure felügyeleti műveleteket.  Az adott portok (szükséges és választható) a nyilvános VIP elérhető teljes listáját lásd: a cikk a [bejövő forgalom vezérlése] [ controllinginboundtraffic] az App Service-környezetek számára. 

App Service-környezetek is csak egy virtuális hálózat belső címet, más néven egy ILB (belső terheléselosztó) címet kötött futó alkalmazásokat támogatja.  Egy ILB a engedélyezett ASE, HTTP és HTTPS-forgalmat az alkalmazások, valamint a távoli hibakeresési hívások, a ILB cím ügyfélszámítógépekre érkeznek.  Leggyakoribb ILB-ASE konfigurációk FTP-/ FTPS-forgalmat is érkeznek ILB címre.  Az Azure felügyeleti műveletek még egy ILB a nyilvános VIP-454/455 portokká erdőtől áramolnak azonban engedélyezve van a ASE.

Az alábbi ábrán egy App Service-környezet, ahol az alkalmazások kötött nyilvános virtuális IP-címnek a a különböző bejövő és kimenő hálózati forgalom áttekintését tartalmazza:

![Általános hálózati forgalom][GeneralNetworkFlows]

Egy App Service Environment-környezet képes kommunikálni a saját felhasználói végpontok különböző.  Az App Service Environment-környezetben futó alkalmazások például az azonos virtuális hálózati topológia IaaS virtuális gépeken futó adatbázis-kiszolgálókat is elérheti.

> [!IMPORTANT]
> A hálózati diagram megnézi, az "egyéb számítási erőforrások" telepítése egy másik alhálózat az App Service-környezetet. A mértékéig ugyanazon alhálózaton lévő erőforrásokat üzembe helyezi letiltja ezeket az erőforrásokat (kivéve az adott helyen belüli-ASE Útválasztás) és ASE közötti kapcsolatot. Telepítheti egy másik alhálózat helyette (lévő ugyanazt a virtuális Hálózatot). Az App Service Environment-környezet majd kell kapcsolódnia. Nincs szükség további konfigurációra.
> 
> 

App Service-környezetek is kommunikálni az Sql-adatbázis és az Azure Storage felügyeletével és üzemeltetésével az App Service-környezetek szükséges erőforrásokat.  Az Sql- és tárolási erőforrások kommunikáló az App Service-környezetek találhatók az App Service Environment-környezet ugyanabban a régióban, míg mások távoli Azure-régiók találhatók.  Ennek eredményeképpen kimenő képes kapcsolódni az internetre mindig szükség egy App Service-környezet működését. 

Egy App Service Environment-környezet van telepítve egy alhálózatot, mert hálózati biztonsági csoportok segítségével szabályozhatja az alhálózat bejövő forgalmát.  A következő témakör az App Service-környezetek bejövő forgalom vezérlése [cikk][controllinginboundtraffic].

A következő cikk kezelésével kapcsolatos részleteket ismerteti, hogy engedélyezze a kimenő internetkapcsolat egy App Service Environment-környezet [Express Route][ExpressRoute].  Ugyanezt a megközelítést a cikkben ismertetett vonatkozik, amikor olyan hely-hely kapcsolatot, és használata a kényszerített bújtatást.

## <a name="outbound-network-addresses"></a>Kimenő hálózati címek
Az App Service-környezetek lehetővé teszi a kimenő hívásokat, amikor egy IP-cím mindig társítva a kimenő hívásokat.  A használt IP-cím függ, hogy a meghívott végpont található, a virtuális hálózati topológia belül vagy kívül a virtuális hálózati topológiától.

Ha a meghívott végpont **kívül** a virtuális hálózati topológia, majd a kimenő (más néven a kimenő NAT-cím) használt cím az App Service Environment-környezet nyilvános VIP.  Ez a cím az App Service-környezet a Tulajdonságok panelére léphet a portál felhasználói felületen található.

![Kimenő IP-cím][OutboundIPAddress]

Ezt a címet is meg lehet határozni a ASEs, amelyek csak egy nyilvános VIP létrehozásával egy alkalmazást az App Service Environment-környezetben, és majd végrehajtása egy *nslookup* meg a webalkalmazás URL-címe. Az eredményül kapott IP-cím, mind a nyilvános VIP, valamint az App Service Environment-kimenő NAT-cím.

Ha a meghívott végpont **belül** a virtuális hálózati topológia a hívó alkalmazás kimenő címét a belső IP-cím az egyes számítási erőforrás az alkalmazást futtatva lesz.  Azonban nincs virtuális hálózat belső IP-címek és alkalmazások állandó leképezéseket.  Alkalmazások mozgathatók különböző számítási erőforrásokat, és a készlet elérhető számítási erőforrások az App Service-környezetek módosíthatja skálázás műveletek miatt.

Azonban mivel az App Service-környezetek mindig egy alhálózaton belül található, akkor garantált, hogy a belső IP-cím egy alkalmazást futtató számítási erőforrás lesz mindig találhatók, az alhálózat CIDR-tartomány.  Ennek eredményeképpen amikor a részletes ACL-ek vagy a hálózati biztonsági csoportok segítségével biztonságos hozzáférés a többi végpont a virtuális hálózaton belül, az App Service-környezet tartalmazó alhálózat tartományának kell számára hozzáférést kell biztosítani.

Az alábbi ábrán látható ezekről a fogalmakról részletesebben:

![Kimenő hálózati címek][OutboundNetworkAddresses]

A fenti ábrán:

* Mivel az App Service Environment-környezet nyilvános VIP 192.23.1.2, a kimenő IP-cím használható, ha hívása "Internet" végpontok esetén.
* Az App Service Environment-környezet tartalmazó alhálózat CIDR-tartomány 10.0.1.0/26.  Belül az azonos virtuális hálózati infrastruktúra többi végpont a címtartományán belül valahol az származó alkalmazásokból hívások jelenik meg.

## <a name="calls-between-app-service-environments"></a>App Service-környezetek közötti hívások
Egy összetettebb forgatókönyv akkor fordulhat elő, ha több App Service Environment-környezetek az azonos virtuális hálózatban lévő telepíti, és egy másik App Service Environment-környezet egy App Service Environment-környezet a kimenő meghíváshoz.  Az ilyen típusú kereszt-App Service Environment-környezet is hívások az "Internet" hívások tekinteni.

Az alábbi ábrán látható példát alkalmazásokkal architektúra a egy App Service Environment-környezet (pl. "Bejárati ajtón" webalkalmazások) hívása a következő alkalmazásokat egy másik App Service-környezet (pl. belső háttér-API-alkalmazások nem feltétlenül érhető el az internetről). 

![App Service-környezetek közötti hívások][CallsBetweenAppServiceEnvironments] 

A fenti példában az App Service Environment-környezet "ASE egy" 192.23.1.2 kimenő IP-címmel rendelkezik.  Ha a futó alkalmazások App Service-környezet elérhetővé válnak az azonos virtuális hálózatban, a kimenő hívás található egy másik App Service Environment-környezet ("ASE két") futó alkalmazások számára kimenő hívást kell hívnia a "Internet" számít.  Ennek eredményeképpen a második érkező hálózati forgalom App Service Environment-környezet megjeleníti a 192.23.1.2 (azaz nem a alhálózati címtartományt az első App Service Environment-környezet) származó.

Annak ellenére, hogy másik App Service Environment-környezetek közötti hívások tekintendők "Internet" hívások, ha mindkét App Service Environment-környezetek azonos Azure-régióban található a hálózati forgalmat a regionális Azure-hálózatot a marad, és nem halad fizikailag a nyilvános interneten keresztül.  Ennek eredményeképpen segítségével a hálózati biztonsági csoport az alhálózat, a második App Service Environment-környezet csak lehetővé teszi a bejövő hívások az első App Service-környezet (amelynek kimenő IP-cím 192.23.1.2), biztosítva ezzel az App Service Environment-környezetek közötti biztonságos kommunikációhoz.

## <a name="additional-links-and-information"></a>További hivatkozások és információk
Részletek bejövő App Service Environment-környezetek által használt portok és a hálózati biztonsági csoportok segítségével vezérelheti, bejövő forgalom érhető [Itt][controllinginboundtraffic].

Információk felhasználó által megadott útvonalak megadása az App Service Environment-környezetek kimenő Internet-hozzáférést érhető el ezen [cikk][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

