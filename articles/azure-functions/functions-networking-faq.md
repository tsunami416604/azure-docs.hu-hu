---
title: Az Azure Functions a hálózatkezelés kapcsolatos gyakori kérdések
description: Adott válaszokat a leggyakoribb kérdéseket és az Azure Functions használatával hálózati forgatókönyvek.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548637"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Hálózatkezelés az Azure Functions szolgáltatásban – gyakori kérdések

Alább felsoroljuk a hálózati kapcsolatos gyakori kérdésekre. Egy átfogóbb áttekintése, olvassa el a [hálózati beállításokat a dokumentum-függvények](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hogyan állíthatok be egy statikus IP-címet a függvények?

A függvény egy App Service Environment (ASE) üzembe helyezése jelenleg az egyetlen módja annak, hogy egy statikus bejövő és kimenő IP-címet a függvény. Az ASE használatával kapcsolatos részletekért indítsa el a cikk itt: [Létrehozásáról és használatáról az ILB ASE](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hogyan korlátozhatom internet-hozzáférés a függvényt?

Számos módon, az alábbiakban az internet-hozzáférés is korlátozható.

* [Az IP-korlátozások](../app-service/app-service-ip-restrictions.md): korlátozzák a bejövő forgalmat a függvényalkalmazás által IP-címtartomány.
* Távolítsa el az összes HTTP-eseményindítók. Egyes alkalmazások esetében is használhatók, egyszerűen csak HTTP-eseményindítók elkerüléséhez, és elindítsa a függvényt más eseményforrás használatával.

Ebben a legfontosabb szempont, hogy ne feledje, hogy az Azure portal szerkesztő használata a futó függvény közvetlen hozzáférést igényel. Az Azure Portalon keresztül kódváltozások az eszköz használatával keresse meg a portálon az IP-alkalmazásátjárónál lesz szükség. Továbbra is, azonban használhatja a platform szolgáltatásai lapon semmit a korlátozásait a hálózati helyen.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>Hogyan korlátozhatom a függvényalkalmazás egy virtuális hálózaton?

Úgy, hogy minden forgalom egy virtuális hálózaton keresztül teljes mértékben korlátozni a függvény csak úgy, hogy használjon egy belső terheléselosztásos (ILB) App Service Environment (ASE). Ezt a beállítást helyez üzembe egy virtuális hálózaton belül dedikált infrastruktúra webhelyét, és elküldi az összes eseményindítók és a virtuális hálózat forgalmához. Az ASE használatával kapcsolatos részletekért indítsa el a cikk itt: [Létrehozásáról és használatáról az ILB ASE](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>Hogyan érhetem el a virtuális Hálózatban található erőforrások a függvényalkalmazás?

Egy virtuális Hálózatban található erőforrások egy futó függvény VNET-integráció használatával érheti el. További információkért lásd: [VNET-integráció](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hogyan érhetem el Szolgáltatásvégpontok által védett erőforrások?

Az új VNET-integráció használatával (jelenleg előzetes verzióban érhető el), védett erőforrásokhoz egy futó függvény szolgáltatásvégpont is elérheti. További információkért lásd: [VNET-integráció előzetes](functions-networking-options.md#preview-vnet-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>Hogyan kiválthatja egy függvény egy erőforráshoz egy virtuális hálózaton?

A függvényalkalmazás az App Service-környezet üzembe helyezésével csak egy függvényt a virtuális hálózat az erőforráshoz is indíthat. Az ASE használatával kapcsolatos részletekért lásd: [létrehozása és a egy ILB ASE használatával](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>Hogyan telepítheti a függvényalkalmazás, egy virtuális hálózaton?

App Service-környezet üzembe helyezése az egyetlen módja hozzon létre egy függvényalkalmazást, amely teljes egészében belül egy virtuális hálózathoz tartozó információk ILB ASE környezetben, kezdje Itt a cikket: [Létrehozásáról és használatáról az ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Olyan esetekben, ahol csak hozzáférésre van szüksége egyirányú VNET-erőforrások, illetve kisebb átfogó hálózatelkülönítés, tekintse meg a [Functions hálózati áttekintése](functions-networking-options.md).

## <a name="next-steps"></a>További lépések

További információ a hálózatkezelés és funkciók: 

* [Hajtsa végre a virtuális hálózatok közötti integráció oktatóanyagban](./functions-create-vnet.md)
* [További információ a hálózatkezelési funkciók itt beállításait](./functions-networking-options.md)
* [További tudnivalók a VNET-integráció az App Service szolgáltatással / ide-függvények](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatok](../virtual-network/virtual-networks-overview.md)
* [További hálózati funkciókat és hozzáférés-vezérlés az App Service Environment-környezetek engedélyezése](../app-service/environment/intro.md)
