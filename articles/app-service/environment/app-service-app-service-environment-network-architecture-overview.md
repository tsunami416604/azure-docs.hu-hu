---
title: Az App Service Environment-környezetek hálózati architektúrájának áttekintése
description: Az architektúra áttekintése a hálózati topológia ofApp Service-környezetek.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: 8b6e251e4fd1b5786af0adc0f9477c08374266f2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957725"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Az App Service Environment-környezetek hálózati architektúrájának áttekintése
## <a name="introduction"></a>Bevezetés
App Service Environment-környezetek mindig jönnek létre, egy alhálózaton belül egy [virtuális hálózat] [ virtualnetwork] – App Service Environment-környezetben futó alkalmazások és található belül az ugyanazon a virtuális magánhálózati végpontok közötti kommunikációhoz hálózati topológia.  Ügyfelek előfordulhat, hogy zárolását a virtuális hálózati infrastruktúra részeit, mivel fontos megismerni az App Service Environment hálózati kommunikációs folyamatok típusait.

## <a name="general-network-flow"></a>Általános hálózati forgalom
Amikor egy App Service Environment (ASE) for apps nyilvános virtuális IP-címet (VIP) használ, minden bejövő forgalom érkezik a nyilvános virtuális IP-cím.  Ez magában foglalja az alkalmazások a HTTP és HTTPS-forgalom, valamint az egyéb adatforgalom FTP, a távoli hibakeresési funkció és az Azure felügyeleti műveleteket.  Az adott portok (kötelező és nem kötelező) a nyilvános virtuális IP-cím elérhető teljes listáját lásd: a cikk a [bejövő forgalom szabályozása] [ controllinginboundtraffic] az App Service-környezet. 

App Service Environment-környezetek is támogatja a futó alkalmazások csak egy virtuális hálózat belső cím, más néven (belső load balancer) ILB-címmel vannak kötve.  Az ILB engedélyezett ASE, HTTP és HTTPS-forgalom az alkalmazások, valamint a távoli hibakeresési hívások, az ILB-címmel ügyfélszámítógépekre érkeznek.  A leggyakoribb konfigurációk ILB ASE-környezetben FTP-/ FTPS-forgalmat is érkezik az ILB-címét.  Azonban Azure kezelési műveleteket továbbra is átkerülnek 454/455 portjait a nyilvános virtuális IP-cím egy ilb ASE engedélyezve.

Az alábbi ábrán a különböző bejövő és kimenő hálózati forgalom áttekintése látható, az App Service-környezet az alkalmazások hol vannak kötve egy nyilvános virtuális IP-cím:

![Általános hálózati folyamatok][GeneralNetworkFlows]

App Service-környezet különféle privát ügyfél-végpontok kommunikálhat.  Például az App Service Environment környezetben futó alkalmazások csatlakozhatnak az azonos virtuális hálózati topológia az IaaS virtuális gépeken futó adatbázis-kiszolgálók.

> [!IMPORTANT]
> A hálózati diagram megnézzük, az "egyéb számítási erőforrás" vannak üzembe helyezve, az App Service-környezet egy másik alhálózatot. Üzembe helyezni az ASE ugyanabban az alhálózatban található erőforrásokat letiltja a kapcsolat ASE és ezeket az erőforrásokat (kivéve az adott intra-ASE Útválasztás). Alatt üzembe helyezhet egy másik alhálózathoz inkább (ugyanazon a VNETEN). Az App Service Environment tudnak csatlakozni. További konfiguráció nélkül nem szükséges.
> 
> 

App Service Environment-környezetek is kommunikálnak az Sql DB és az Azure Storage felügyeletével és üzemeltetésével az App Service-környezet számára szükséges erőforrások.  Egyes, amely egy App Service Environment-környezet kommunikál az Sql- és tárolási erőforrások találhatók az App Service-környezet ugyanabban a régióban, míg mások távoli Azure-régióban található.  Ennek eredményeképpen az internetre irányuló kimenő kapcsolattal mindig szükség egy App Service-környezet megfelelő működéséhez. 

Mivel az App Service-környezet egy alhálózaton van üzembe helyezve, a hálózati biztonsági csoportok segítségével az alhálózatra bejövő forgalom szabályozása.  Az App Service Environment bejövő forgalom szabályozása a részleteket a következő [cikk][controllinginboundtraffic].

A kimenő Internet-kapcsolat engedélyezése az App Service-környezet, részletek: használatával kapcsolatban a következő cikkben [Express Route][ExpressRoute].  Ugyanezzel a módszerrel a cikkben ismertetett érvényes, ha a helyek közötti kapcsolat használata, és a kényszerített bújtatás használata.

## <a name="outbound-network-addresses"></a>Kimenő hálózati címek
App Service-környezet kimenő-hívást hajt végre, amikor egy IP-cím mindig társítva a kimenő hívások.  A hívott végpont-e a virtuális hálózati topológiától belül vagy kívül esik a virtuális hálózati topológiától található az adott IP-cím használt függ.

Ha a végpont hívott **kívül** a virtuális hálózati topológiát, majd a kimenő (más néven a kimenő NAT-cím) használt cím az az App Service Environment nyilvános virtuális IP.  Ez a cím az App Service-környezet tulajdonságai panelen található a portál felhasználói felületén.

![Kimenő IP-cím][OutboundIPAddress]

Ezt a címet is meg lehet határozni az ASE, amelyek csak egy nyilvános virtuális IP-cím létrehozásával egy alkalmazást az App Service-környezetben és végrehajtásával egy *nslookup* a az alkalmazás címére. Az eredő IP-cím mind a nyilvános VIP-címet, valamint az App Service-környezet kimenő NAT-cím.

Ha a végpont hívott **belül** a virtuális hálózati topológia a hívó alkalmazás kimenő címét az egyes számítási erőforrás az alkalmazást futtató belső IP-címe lesz.  Azonban nem szerepel a virtuális hálózat belső IP-címek és alkalmazások állandó hozzárendelés.  Alkalmazások helyezheti át a különböző számítási erőforrásokhoz és a készlet elérhető számítási erőforrások App Service Environment-környezetben módosíthatja a méretezési műveletek miatt.

Azonban mivel az App Service Environment mindig egy alhálózaton belül található, akkor garantáltan lesz a belső IP-cím-alkalmazást futtató számítási erőforrás mindig essen az alhálózat CIDR-tartományt.  Ennek eredményeképpen részletes hozzáférés-vezérlési listák vagy a hálózati biztonsági csoportok segítségével biztonságos hozzáférés a más végpontok a virtuális hálózaton belül, amikor az App Service-környezetet tartalmazó alhálózat tartományra kell hozzáférést biztosítanak.

A következő ábrán ezek a fogalmak részletesebben:

![Kimenő hálózati címek][OutboundNetworkAddresses]

A fenti ábrán:

* Mivel az App Service Environment nyilvános virtuális IP-192.23.1.2, ez az "Internet" végpontok hívása esetén használt kimenő IP-cím.
* Az App Service-környezetben a tartalmazó alhálózat CIDR-tartományt 10.0.1.0/26.  Más végpontok ugyanazon virtuális hálózati infrastruktúrán belül, a címtartományán belül valahol származó alkalmazásoktól érkező hívások jelenik meg.

## <a name="calls-between-app-service-environments"></a>App Service Environment-környezetek közötti hívások
Egy összetettebb forgatókönyvet akkor fordulhat elő, ha több App Service-környezetek ugyanazon a virtuális hálózaton üzembe, és győződjön meg arról, a másik App Service Environment-környezet egy App Service Environment-környezet kimenő hívásait.  Az ilyen típusú adatbázisközi App Service-környezet is meghívja az "Internet" hívások tekinteni.

Az alábbi ábrán látható szintekre osztott architektúrával rendelkező alkalmazások például az egy App Service Environment (például) "Bejárati ajtajának" a webalkalmazások) alkalmazásokat egy másik App Service-környezet (pl. belső háttérrendszeri API-alkalmazások nem feltétlenül az interneten) hívása. 

![App Service Environment-környezetek közötti hívások][CallsBetweenAppServiceEnvironments] 

A fenti példában az App Service-környezet "Egy ASE" 192.23.1.2 kimenő IP-címmel rendelkezik.  Ha a gépen futó App Service-környezet kimenő hívnia egy alkalmazást futtat egy másik App Service Environment-környezet ("ASE két") található, az azonos virtuális hálózatba, a kimenő hívás teszi lesz kezelni az "Internet" hívásakor.  Ennek eredményeképpen a második érkező hálózati forgalmat App Service Environment-környezet megjelenik, forrásszámítógép: 192.23.1.2 (azaz nem az alhálózat címtartományának, az első App Service-környezet).

Annak ellenére, hogy másik App Service Environment-környezetek közötti hívások számít "Internet" hívás, ha mindkét App Service Environment-környezetek Fájlmegosztáséval azonos régióban találhatók a hálózati forgalmat a regionális Azure-beli hálózaton maradnak, és nem hajtja végre ténylegesen a folyamat során a nyilvános interneten.  Ennek eredményeképpen segítségével egy hálózati biztonsági csoportot a második App Service Environment-környezet alhálózatában található engedélyezése csak az első App Service Environment (amelynek kimenő IP-cím a 192.23.1.2), biztosítva ezzel az alkalmazás közötti biztonságos kommunikáció a bejövő hívások Service-környezetek.

## <a name="additional-links-and-information"></a>További hivatkozások és információk
A részletek bejövő App Service Environment-környezetek által használt portok és hálózati biztonsági csoportok használata a bejövő forgalom szabályozása érhető el [Itt][controllinginboundtraffic].

Felhasználói információk, által megadott útvonalak kimenő Internet-hozzáférést az App Service Environmentet érhető el a jelen megadását [cikk][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

