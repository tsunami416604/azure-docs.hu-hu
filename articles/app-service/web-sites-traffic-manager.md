---
title: Forgalom szabályozása a Traffic Managerrel
description: Az Azure Traffic Manager konfigurálásával kapcsolatos gyakorlati tanácsok, amikor integrálja az Azure App Service szolgáltatással.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437903"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Az Azure App Service-forgalom szabályozása az Azure Traffic Managerrel
> [!NOTE]
> Ez a cikk összefoglaló információkat tartalmaz a Microsoft Azure Traffic Manager számára az Azure App Service-hez kapcsolódóan. További információ az Azure Traffic Manager magáról megtalálható a cikk végén található hivatkozásokat.
> 
> 

## <a name="introduction"></a>Introduction (Bevezetés)
Az Azure Traffic Manager használatával irányíthatja, hogyan legyenek elosztva a webes ügyfelektől származó kérések az alkalmazások között az Azure App Service-ben. Amikor App Service-végpontokat ad egy Azure Traffic Manager-profilhoz, az Azure Traffic Manager nyomon követi az App Service-alkalmazások állapotát (fut, leállítva vagy törölve), így eldöntheti, hogy melyik végpont kapja a forgalmat.

## <a name="routing-methods"></a>Útválasztási módszerek
Az Azure Traffic Manager négy különböző útválasztási módszert használ. Ezek a módszerek az alábbi listában az Azure App Service-hez tartozók.

* ** [Prioritás:](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)** használjon elsődleges alkalmazást az összes forgalomhoz, és készítsen biztonsági mentést arra az esetre, ha az elsődleges vagy a biztonsági mentési alkalmazások nem érhetők el.
* ** [Súlyozott:](../traffic-manager/traffic-manager-routing-methods.md#weighted)** a forgalmat egyenletesen vagy súlyok szerint osztja el az alkalmazások között, amelyet ön határoz meg.
* ** [Teljesítmény:](../traffic-manager/traffic-manager-routing-methods.md#performance)** ha különböző földrajzi helyeken vannak alkalmazások, használja a "legközelebbi" alkalmazást a legalacsonyabb hálózati késés szempontjából.
* ** [Földrajzi](../traffic-manager/traffic-manager-routing-methods.md#geographic):** a felhasználókat adott alkalmazásokhoz irányítja, attól függően, hogy a DNS-lekérdezésük melyik földrajzi helyről származik. 

További információt a [Traffic Manager útválasztási módszerei című](../traffic-manager/traffic-manager-routing-methods.md)témakörben talál.

## <a name="app-service-and-traffic-manager-profiles"></a>App Service- és Forgalomkezelő-profilok
Az App Service-alkalmazásforgalom vezérlésének konfigurálásához hozzon létre egy profilt az Azure Traffic Managerben, amely a korábban leírt négy terheléselosztási módszer egyikét használja, majd adja hozzá azokat a végpontokat (ebben az esetben az App Service-t), amelyek hez szabályozni szeretné a forgalmat a profilhoz. Az alkalmazás állapota (futás, leállított vagy törölt) rendszeresen közlik a profilt, hogy az Azure Traffic Manager ennek megfelelően irányíthassa a forgalmat.

Ha az Azure Traffic Managert használja az Azure-ral, tartsa szem előtt a következő pontokat:

* Az alkalmazás csak az ugyanabban a régióban lévő központi telepítések, App Service már rendelkezik feladatátvételi és ciklikus multiplexelés funkció alkalmazás módtól függetlenül.
* Az ugyanabban a régióban, hogy az App Service-t egy másik Azure-felhőszolgáltatással együtt, a hibrid forgatókönyvek engedélyezéséhez kombinálhatja mindkét típusú végpontok.
* Régiónként csak egy App Service-végpontot adhat meg egy profilban. Amikor kiválaszt egy alkalmazást egy régió végpontjaként, a régió többi alkalmazása nem lesz elérhető az adott profil kiválasztásához.
* Az Azure Traffic Manager-profilban megadott App Service-végpontok a **tartománynevek** szakaszban jelennek meg a profilban az alkalmazás konfigurálása lapján, de ott nem konfigurálhatók.
* Miután hozzáadott egy alkalmazást egy profilhoz, az alkalmazás portállapjának irányítópultján található **webhely URL-címe** megjeleníti az alkalmazás egyéni tartományURL-címét, ha beállított egyet. Ellenkező esetben megjeleníti a Traffic Manager profil `contoso.trafficmanager.net`URL-címét (például ). Az alkalmazás közvetlen tartományneve és a Forgalomkezelő URL-címe is látható az alkalmazás Konfigurálás lapján a **Tartománynevek** szakaszban.
* Az egyéni tartománynevek a várt módon működnek, de az alkalmazásokhoz való hozzáadásmellett a DNS-leképezést is úgy kell konfigurálnia, hogy az a Traffic Manager URL-címére mutasson. Az App Service-alkalmazások egyéni tartományának beállításáról az [Egyéni tartománynév konfigurálása az Azure App Service-ben a Traffic Manager-integrációval](configure-domain-traffic-manager.md)című témakörben talál.
* Csak normál vagy prémium módban lévő alkalmazásokat adhat hozzá egy Azure Traffic Manager-profilhoz.
* Ha egy alkalmazást hozzáad egy Traffic Manager-profilhoz, az alkalmazás újraindul.

## <a name="next-steps"></a>Következő lépések
Az Azure Traffic Manager fogalmi és technikai áttekintését a [Traffic Manager áttekintése című témakörben találja.](../traffic-manager/traffic-manager-overview.md)


