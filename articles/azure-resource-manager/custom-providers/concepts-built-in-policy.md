---
title: Társítások telepítése házirendek használatával
description: Ismerje meg, hogyan telepíthet társításokat egyéni szolgáltatókhoz az Azure Policy szolgáltatás használatával.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650473"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Társítások üzembe helyezése egyéni szolgáltatóhoz az Azure-szabályzat használatával

Az Azure-szabályzatok segítségével társítások erőforrásokat társíthat egy egyéni szolgáltató. Ebben a cikkben egy beépített szabályzatot ismertetünk, amely társításokat telepít, és hogyan használhatja ezt a szabályzatot.

## <a name="built-in-policy-to-deploy-associations"></a>Beépített házirend társítások üzembe helyezéséhez

Az egyéni szolgáltatók társításai olyan beépített házirend, amely egy erőforrás egyéni szolgáltatóhoz való társítására használható társítások üzembe helyezésére. A házirend három paramétert fogad el:

- Egyéni szolgáltatóazonosító – Ez az azonosító annak az egyéni szolgáltatónak az erőforrás-azonosítója, amelyhez az erőforrásokat társkell társozni.
- Társítandó erőforrástípusok – Ezek az erőforrástípusok az egyéni szolgáltatóhoz társítható erőforrástípusok listája. Ugyanazzal a házirenddel több erőforrástípust is társíthat egy egyéni szolgáltatóhoz.
- Társítási név előtag – Ez a karakterlánc a létrehozandó társítási erőforrás nevéhez hozzáadandó előtag. Az alapértelmezett érték a "DeployedByPolicy".

A házirend deployIfnotExists kiértékelést használ. Azt követően fut, hogy egy erőforrás-szolgáltató kezelt egy létrehozási vagy frissítési erőforrás-kérelmet, és a kiértékelés sikeres állapotkódot adott vissza. Ezt követően a társítási erőforrás egy sablon központi telepítése használatával lesz telepítve.
A társításokról az [Azure Custom Providers erőforrás bevezetéscímű témaköre.](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>A beépített házirendek üzembe helyezése 

### <a name="prerequisites"></a>Előfeltételek
Ha az egyéni szolgáltatónak engedélyekre van szüksége a házirend hatóköréhez egy művelet végrehajtásához, a társítási erőforrás házirend-telepítése nem működne az engedélyek megadása nélkül.

### <a name="policy-assignment"></a>Szabályzat-hozzárendelés
A beépített házirend használatához hozzon létre egy házirend-hozzárendelést, és rendelje hozzá a központi telepítés társításokat egy egyéni szolgáltatóházirendhez. A házirend ezután azonosítja a nem megfelelő erőforrásokat, és telepíti a társítást az erőforrásokhoz.

![Beépített házirend hozzárendelése](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Segítség kérése

Ha kérdése van az Azure egyéni erőforrás-szolgáltatók fejlesztésével kapcsolatban, próbálja meg feltenni őket a [Veremtúlcsordulás szolgáltatásban.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Lehet, hogy egy hasonló kérdésre már válaszoltak, ezért először a feladás előtt ellenőrizze. Add hozzá ```azure-custom-providers``` a címkét, hogy gyors választ kapj!

## <a name="next-steps"></a>További lépések

Ebben a cikkben a beépített szabályzat használatával kapcsolatban is olvashat. További információ:

- [Fogalmak: Az Azure egyéni szolgáltatók erőforrás bevezetés](./concepts-resource-onboarding.md)
- [Oktatóanyag: Erőforrás-bevezetés egyéni szolgáltatókkal](./tutorial-resource-onboarding.md)
- [Oktatóanyag: Egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Rövid útmutató: Egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Útmutató: Egyéni műveletek hozzáadása azure REST API-hoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása Azure REST API-hoz](./custom-providers-resources-endpoint-how-to.md)
