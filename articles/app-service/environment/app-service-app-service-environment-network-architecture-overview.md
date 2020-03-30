---
title: Hálózati architektúra v1
description: Az App Service-környezetek hálózati topológiájának architekturális áttekintése. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243847"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Az App Service-környezetek hálózati architektúrájának áttekintése
Az App Service-környezetek mindig egy [virtuális hálózat][virtualnetwork] alhálózatán belül jönnek létre – az App Service-környezetben futó alkalmazások kommunikálhatnak az ugyanazon virtuális hálózati topológián belül található magánvégfelhasználói pontokkal.  Mivel az ügyfelek zárolhatják a virtuális hálózati infrastruktúrájuk egyes részeit, fontos megérteni, hogy milyen típusú hálózati kommunikációs folyamatok fordulnak elő az App Service-környezetben.

## <a name="general-network-flow"></a>Általános hálózati folyamat
Ha egy App Service-környezet (ASE) egy nyilvános virtuális IP-címet (VIP) használ az alkalmazásokhoz, az összes bejövő forgalom érkezik az adott nyilvános VIP-re.  Ez magában foglalja az alkalmazások HTTP- és HTTPS-forgalmát, valamint az FTP, a távoli hibakeresési funkciók és az Azure felügyeleti műveletek egyéb forgalmát.  A nyilvános virtuális ip-címeken elérhető portok (kötelező és nem kötelező) teljes listáját az App Service-környezetbe [irányuló bejövő forgalom szabályozásáról][controllinginboundtraffic] szóló cikk tartalmazza. 

Az App Service-környezetek támogatják azokat az alkalmazásokat is, amelyek csak egy virtuális hálózati belső címhez, más néven ILB-címhez (belső terheléselosztó) vannak kötve.  Egy ILB-kompatibilis ASE,HTTP és HTTPS-forgalom az alkalmazások, valamint a távoli hibakeresési hívások, érkezik az ILB-címet.  A leggyakoribb ILB-ASE konfigurációk esetén az FTP/FTPS-forgalom az ILB-címre is megérkezik.  Az Azure felügyeleti műveletei azonban továbbra is a 454/455-ös portokra kerülnek egy ILB-kompatibilis ASE nyilvános VIP-jével.

Az alábbi ábra áttekintést nyújt az App Service-környezet különböző bejövő és kimenő hálózati folyamatairól, ahol az alkalmazások nyilvános virtuális IP-címhez vannak kötve:

![Általános hálózati folyamatok][GeneralNetworkFlows]

Az App Service-környezet számos privát ügyfél-végponttal kommunikálhat.  Az App Service-környezetben futó alkalmazások például csatlakozhatnak az azonos virtuális hálózati topológiában lévő IaaS virtuális gépeken futó adatbázis-kiszolgáló(ka)khoz.

> [!IMPORTANT]
> A hálózati diagramot tekintve az "Egyéb számítási erőforrások" az App Service-környezettől eltérő alhálózatban vannak telepítve. Az erőforrások üzembe helyezése ugyanabban az alhálózatban az ASE-vel blokkolja az ASE-től az erőforrásokhoz való kapcsolódást (kivéve az adott ASE-műveletválasztást). Ehelyett egy másik alhálózatra (ugyanabban a virtuális hálózatban) telepítse. Az App Service-környezet ezután képes lesz csatlakozni. Nincs szükség további konfigurációra.
> 
> 

Az App Service-környezetek az Sql DB és az Azure Storage által az App Service-környezet kezeléséhez és működtetéséhez szükséges erőforrásokkal is kommunikálnak.  Az Sql Service-környezet által kommunikált sql- és tárolási erőforrások egy része ugyanabban a régióban található, mint az App Service-környezet, míg mások távoli Azure-régiókban találhatók.  Ennek eredményeképpen az Alkalmazásszolgáltatási környezet megfelelő működéséhez mindig szükség van az internethez való kimenő kapcsolatra. 

Mivel az App Service-környezet alhálózatban van telepítve, a hálózati biztonsági csoportok segítségével szabályozhatja az alhálózatba irányuló bejövő forgalmat.  Az App Service-környezetbe irányuló bejövő forgalom szabályozásáról az alábbi cikkben olvashat [részletesen.][controllinginboundtraffic]

Az App Service-környezetből érkező kimenő internetkapcsolat engedélyezéséről az expressz [útvonalról][ExpressRoute]szóló alábbi cikkben olvashat.  A cikkben ismertetett megközelítés a helyek közötti kapcsolat és a kényszerített bújtatás használata esetén is érvényes.

## <a name="outbound-network-addresses"></a>Kimenő hálózati címek
Amikor egy App Service-környezet kimenő hívásokat kezdeményez, az IP-cím mindig a kimenő hívásokhoz lesz társítva.  A használt adott IP-cím attól függ, hogy a hívott végpont a virtuális hálózati topológián belül vagy a virtuális hálózati topológián kívül található.The specific IP address that is used depends on whether the endpoint being called is located within the virtual network topology, or outside of the virtual network topology.

Ha a hívott végpont **kívül** esik a virtuális hálózati topológián, majd a kimenő cím (más néven a kimenő NAT-cím) használt a nyilvános VIP az App Service-környezetben.  Ez a cím megtalálható a portál felhasználói felülete az App Service-környezet tulajdonságok panelen.

![Kimenő IP-cím][OutboundIPAddress]

Ez a cím is meghatározható az ASEs, amelyek csak egy nyilvános VIRTUÁLIS IP-cím létrehozásával egy alkalmazást az App Service-környezetben, majd egy *nslookup* az alkalmazás címét. Az eredő IP-cím a nyilvános VIRTUÁLIS IP-cím, valamint az App Service-környezet kimenő NAT-címe.

Ha a hívott végpont a virtuális hálózati topológián **belül** van, a hívó alkalmazás kimenő címe az alkalmazást futtató egyes számítási erőforrások belső IP-címe lesz.  A virtuális hálózati belső IP-címek azonban nem állandóleképezése az alkalmazásokhoz.  Az alkalmazások különböző számítási erőforrások között mozoghatnak, és az App Service-környezetben rendelkezésre álló számítási erőforrások készlete a méretezési műveletek miatt változhat.

Mivel azonban az App Service-környezet mindig egy alhálózaton belül található, garantált, hogy az alkalmazást futtató számítási erőforrás belső IP-címe mindig az alhálózat CIDR-tartományán belül lesz.  Ennek eredményeképpen, ha a részletes ACL-k vagy hálózati biztonsági csoportok segítségével biztonságos hozzáférést biztosít a virtuális hálózaton belül más végpontokhoz, az App Service-környezetet tartalmazó alhálózati tartományhozzáférést kell biztosítani.

Az alábbi ábra részletesebben bemutatja ezeket a fogalmakat:

![Kimenő hálózati címek][OutboundNetworkAddresses]

A fenti ábrán:

* Mivel az App Service-környezet nyilvános VIP-je 192.23.1.2, ez az "Internet" végpontok hívásakor használt kimenő IP-cím.
* Az App Service-környezet ben a tartalmazó alhálózat CIDR-tartománya 10.0.1.0/26.  Az ugyanazon a virtuális hálózati infrastruktúrán belüli többi végpont az alkalmazásokból származó hívásokat látja, amelyek a címtartományon belülről származnak.

## <a name="calls-between-app-service-environments"></a>Hívások az App Service-környezetek között
Összetettebb forgatókönyv akkor fordulhat elő, ha több App Service-környezetet telepít ugyanabban a virtuális hálózatban, és kimenő hívásokat kezdeményez az egyik App Service-környezetből egy másik App Service-környezetbe.  Az ilyen típusú cross App Service Environment hívások is kezelik az "Internet" hívásokat.

Az alábbi ábrán egy réteges architektúra egy app service-környezetben (pl. "Bejárati ajtó" webalkalmazások) alkalmazások hívás a második App Service-környezetben (például a belső háttér-API-alkalmazások nem célja, hogy elérhető az internetről). 

![Hívások az App Service-környezetek között][CallsBetweenAppServiceEnvironments] 

Az App Service-környezet feletti példában az "ASE One" kimenő IP-címe 192.23.1.2.  Ha egy alkalmazás fut ezen az App Service-környezetben egy kimenő hívást egy alkalmazás fut egy másik App Service-környezet ("ASE Two") ugyanazon a virtuális hálózatban található, a kimenő hívás lesz "Internet" hívás.  Ennek eredményeképpen a második App Service-környezetbe érkező hálózati forgalom a 192.23.1.2-ről származóként jelenik meg (azaz nem az első App Service-környezet alhálózati címtartománya).

Annak ellenére, hogy a különböző App Service-környezetek közötti hívásokat "Internet" hívásokként kezeli a program, ha mindkét App Service-környezet ugyanabban az Azure-régióban található, a hálózati forgalom a regionális Azure-hálózaton marad, és fizikailag nem fog átfolyni a nyilvános interneten.  Ennek eredményeképpen a második App Service-környezet alhálózatán lévő hálózati biztonsági csoportot csak az első App Service-környezetből érkező bejövő hívások engedélyezésére használhatja (amelynek kimenő IP-címe 192.23.1.2), így biztosítva az alkalmazás közötti biztonságos kommunikációt. Szolgáltatási környezetek.

## <a name="additional-links-and-information"></a>További linkek és információk
Az App Service-környezetek által használt bejövő portokról és a bejövő forgalom szabályozására hálózati biztonsági csoportok használatával kapcsolatos részletek [itt][controllinginboundtraffic]érhetők el.

A felhasználó által definiált útvonalak használatával kapcsolatban az App Service-környezetek kimenő internet-hozzáférésének engedélyezéséről ebben a [cikkben][ExpressRoute]olvashat. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

