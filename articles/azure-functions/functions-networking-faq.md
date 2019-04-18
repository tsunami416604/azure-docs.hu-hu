---
title: Hálózatkezelés az Azure Functions szolgáltatásban – gyakori kérdések
description: Adott válaszokat a leggyakoribb kérdéseket és az Azure Functions használatával hálózati forgatókönyvek.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 3cf6a0d080e2d8cafcab8e69a614b59a470c7aba
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682202"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Hálózatkezelés az Azure Functions szolgáltatásban – gyakori kérdések

Ez a cikk az Azure Functions hálózat – gyakori kérdések listája. Egy átfogóbb áttekintése: [hálózatkezelési lehetőségeket függvények](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hogyan állíthatok be egy statikus IP-címet a függvények?

App Service Environment-környezetben függvény üzembe helyezése jelenleg az egyetlen módja annak, hogy egy statikus bejövő és kimenő IP-címet a függvény. App Service-környezet használata részletesen, indítsa el a cikkel [létrehozása és használata az App Service-környezet belső terheléselosztó](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hogyan korlátozhatom internet-hozzáférés a függvényt?

Internet-hozzáférés a többféleképpen korlátozhatja:

* [Az IP-korlátozások](../app-service/app-service-ip-restrictions.md): Bejövő forgalom korlátozása IP-címtartomány a függvényalkalmazást.
* Az összes HTTP-eseményindítók eltávolítása. Egyes alkalmazások esetében, ez elegendő egyszerűen elkerülése érdekében a HTTP-eseményindítók és minden más esemény forrása használatával elindítsa a függvényt.

Ne feledje, hogy az Azure portal szerkesztő a futó függvény közvetlen hozzáférést igényel. Az Azure Portalon keresztül kódváltozások az eszköz használatával keresse meg a portálon az IP-alkalmazásátjárónál lesz szükség. De továbbra is használhatja a platform szolgáltatásai lapon semmit a korlátozásait a hálózati helyen.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Hogyan korlátozhatom a függvényalkalmazás egy virtuális hálózaton?

Úgy, hogy minden forgalom egy virtuális hálózaton keresztül teljes mértékben korlátozni a függvény csak úgy, hogy egy belső elosztott terhelésű App Service-környezet. Ezt a beállítást helyez üzembe egy dedikált infrastruktúra egy virtuális hálózaton belül a hely, és elküldi az eseményindítók és a forgalom a virtuális hálózaton keresztül. 

App Service-környezet használata részletesen, indítsa el a cikkel [létrehozása és használata az App Service-környezet belső terheléselosztó](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Hogyan érhetem el a virtuális hálózatban lévő erőforrásokra a függvényalkalmazás?

Egy virtuális hálózatban lévő erőforrásokra futó függvény hozzáférhet a virtuális hálózati integráció használatával. További információkért lásd: [virtuális hálózati integráció](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hogyan érhetem el Szolgáltatásvégpontok által védett erőforrások?

(Jelenleg előzetes verzióban érhető el) virtuális hálózati integráció révén egy futó függvény hozzáférhetnek a service-végpont védett erőforrásokhoz. További információkért lásd: [virtuális hálózati integráció előzetes](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Hogyan kiválthatja egy függvény egy erőforráshoz egy virtuális hálózaton?

App Service-környezet üzembe helyezése a függvényalkalmazás által csak egy függvényt a virtuális hálózatban lévő erőforrás is indíthat. App Service-környezet használatával kapcsolatos részletekért lásd: [létrehozása és használata az App Service-környezet belső terheléselosztó](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Hogyan telepítheti a függvényalkalmazás, egy virtuális hálózaton?

App Service-környezet üzembe helyezése az egyetlen módja, hozzon létre egy függvényalkalmazást, amely teljes egészében egy virtuális hálózaton belül. Részletek a belső terheléselosztó használata App Service-környezet, indítsa el a cikkel [létrehozása és használata az App Service-környezet belső terheléselosztó](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Olyan esetekben, ahol virtuális hálózati erőforrások, illetve kisebb átfogó hálózatelkülönítés csak egyirányú hozzáférésre van szüksége, tekintse meg a [Functions hálózati áttekintése](functions-networking-options.md).

## <a name="next-steps"></a>További lépések

További információ a hálózatkezelés és funkciók további: 

* [A virtuális hálózati integráció használatának első lépéseivel kapcsolatos oktatóanyag](./functions-create-vnet.md)
* [További információ a hálózati beállításokat az Azure Functions szolgáltatásban](./functions-networking-options.md)
* [További tudnivalók a virtuális hálózati integráció az App Service és Functions](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatok](../virtual-network/virtual-networks-overview.md)
* [További hálózati funkciókat és hozzáférés-vezérlés az App Service Environment-környezetek engedélyezése](../app-service/environment/intro.md)
