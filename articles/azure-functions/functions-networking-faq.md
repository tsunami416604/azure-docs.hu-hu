---
title: Gyakori kérdések az Azure Functions hálózatkezelésével kapcsolatban
description: Válaszok az Azure Functions-szel való hálózatépítés leggyakoribb kérdéseire és forgatókönyveire.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409531"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Gyakori kérdések az Azure Functions hálózatkezelésével kapcsolatban

Ez a cikk az Azure Functions hálózatkezelésével kapcsolatos gyakori kérdéseket sorolja fel. Átfogóbb áttekintést a [Functions hálózati beállítások című](functions-networking-options.md)témakörben talál.

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hogyan állíthatok be statikus IP-címet a Functions-ben?

Egy függvény telepítése egy App Service-környezetben jelenleg az egyetlen módja annak, hogy egy statikus bejövő és kimenő IP-t a függvény. Az App Service-környezet használatával kapcsolatos részletekért kezdje a [Belső terheléselosztó létrehozása és használata app szolgáltatáskörnyezettel című cikkével.](../app-service/environment/create-ilb-ase.md)

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hogyan korlátozhatom az internet-hozzáférést a funkciómhoz?

Az internet-hozzáférést többféleképpen is korlátozhatja:

* [IP-korlátozások:](../app-service/app-service-ip-restrictions.md)Korlátozza a bejövő forgalmat a függvényalkalmazásip-tartomány szerint.
    * IP-korlátozások alatt is konfigurálhatja [a szolgáltatásvégpontokat,](../virtual-network/virtual-network-service-endpoints-overview.md)amelyek korlátozzák a függvényt, hogy csak egy adott virtuális hálózatról érkező forgalmat fogadjon el.
* Az összes HTTP-eseményindító eltávolítása. Egyes alkalmazások esetében elegendő egyszerűen elkerülni a HTTP-eseményindítókat, és bármilyen más eseményforrást használni a funkció elindításához.

Ne feledje, hogy az Azure Portal szerkesztője közvetlen hozzáférést igényel a futó függvényhez. Az Azure Portalon keresztül végrehajtott kódmódosításokhoz szükség lesz a portál böngészéséhez használt eszközre, hogy az IP-cím szerepeljen. De továbbra is használhatja semmit a platform funkciók lapon hálózati korlátozások vannak érvényben.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Hogyan korlátozhatom a függvényalkalmazást egy virtuális hálózatra?

A [szolgáltatásvégpontok](./functions-networking-options.md#private-site-access)használatával korlátozhatja egy függvényalkalmazás **bejövő** forgalmát egy virtuális hálózatra. Ez a konfiguráció továbbra is lehetővé teszi, hogy a függvényalkalmazás kimenő hívásokat kezdeményezzen az internetre.

Az egyetlen módja annak, hogy teljesen korlátozza a függvényt, hogy minden forgalom egy virtuális hálózaton keresztül áramlik, hogy egy belső terheléselosztásos App Service-környezet használata. Ez a beállítás a webhelyet egy virtuális hálózaton belüli dedikált infrastruktúrára telepíti, és elküldi az összes eseményindítót és forgalmat a virtuális hálózaton keresztül. 

Az App Service-környezet használatával kapcsolatos részletekért kezdje a [Belső terheléselosztó létrehozása és használata app szolgáltatáskörnyezettel című cikkével.](../app-service/environment/create-ilb-ase.md)

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Hogyan érhetem el a virtuális hálózat erőforrásait egy függvényalkalmazásból?

A virtuális hálózat erőforrásait futó függvényből érheti el a virtuális hálózati integráció használatával. További információ: [Virtuális hálózati integráció](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hogyan férhetek hozzá a szolgáltatásvégpontok által védett erőforrásokhoz?

A virtuális hálózati integráció segítségével elérheti a szolgáltatás-végpont által védett erőforrásokat egy futó függvényből. További információt a [virtuális hálózatok integrációjáról](functions-networking-options.md#virtual-network-integration)talál.

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Hogyan aktiválhatok egy funkciót egy virtuális hálózat egyik erőforrásából?

Engedélyezheti, hogy a HTTP-eseményindítók at virtuális hálózatról hívják meg [a Szolgáltatásvégpontok](./functions-networking-options.md#private-site-access)használatával. 

A virtuális hálózat összes többi erőforrásából is elindíthat egy függvényt a függvényalkalmazás prémium csomagra, az App Service-csomagra vagy az App Service-környezetbe való üzembe helyezésével. További információ: [nem HTTP-s virtuális hálózati eseményindítók](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Hogyan telepíthetem a függvényalkalmazást egy virtuális hálózatban?

Az App Service-környezetben való üzembe helyezés az egyetlen módja annak, hogy hozzon létre egy függvényalkalmazást, amely teljes egészében egy virtuális hálózaton belül van. A belső terheléselosztó App Service-környezettel való használatával kapcsolatos részletekért kezdje a [Belső terheléselosztó létrehozása és használata app szolgáltatáskörnyezettel](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)című cikkével.

Olyan esetekben, amikor csak egyirányú hozzáférésre van szükség a virtuális hálózati erőforrásokhoz, vagy kevésbé átfogó hálózati elkülönítésre, olvassa el a [Functions hálózati áttekintéscímű témakört.](functions-networking-options.md)

## <a name="next-steps"></a>További lépések

További információ a hálózatépítésről és a funkciókról: 

* [Kövesse a virtuális hálózati integráció első lépéseiről szóló oktatóanyagot](./functions-create-vnet.md)
* [További információ az Azure Functions hálózati lehetőségeiről](./functions-networking-options.md)
* [További információ az App Service és functions virtuális hálózati integrációjáról](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatokról](../virtual-network/virtual-networks-overview.md)
* [További hálózati funkciók és vezérlés engedélyezése az App Service-környezetekkel](../app-service/environment/intro.md)
