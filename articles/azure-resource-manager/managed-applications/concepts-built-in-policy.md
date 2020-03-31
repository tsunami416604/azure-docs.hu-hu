---
title: Társítások telepítése felügyelt alkalmazáshoz házirend használatával
description: Ismerje meg, hogyan telepíthet társításokat egy felügyelt alkalmazáshoz az Azure Policy szolgáltatás használatával.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650941"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Társítások üzembe helyezése felügyelt alkalmazásokhoz az Azure Policy használatával

Az Azure-szabályzatok segítségével társítások erőforrásokat társíthat egy felügyelt alkalmazáshoz. Ebben a cikkben egy beépített szabályzatot ismertetünk, amely társításokat telepít, és hogyan használhatja ezt a szabályzatot.

## <a name="built-in-policy-to-deploy-associations"></a>Beépített házirend társítások üzembe helyezéséhez

A felügyelt alkalmazások társításai egy beépített házirend, amely egy erőforrás felügyelt alkalmazáshoz társítására használható társítások üzembe helyezéséhez használható. A házirend három paramétert fogad el:

- Felügyelt alkalmazásazonosító – Ez az azonosító annak a felügyelt alkalmazásnak az erőforrásazonosítója, amelyhez az erőforrásokat társkell társozni.
- Társítandó erőforrástípusok – Ezek az erőforrástípusok a felügyelt alkalmazáshoz társítható erőforrástípusok listája. Ugyanazzal a házirenddel több erőforrástípust is társíthat egy felügyelt alkalmazáshoz.
- Társítási név előtag – Ez a karakterlánc a létrehozandó társítási erőforrás nevéhez hozzáadandó előtag. Az alapértelmezett érték a "DeployedByPolicy".

A házirend deployIfnotExists kiértékelést használ. Azt követően fut, hogy egy erőforrás-szolgáltató kezelte a kiválasztott erőforrástípus(ok) létrehozási vagy frissítési erőforráskérelmét, és a kiértékelés sikeres állapotkódot adott vissza. Ezt követően a társítási erőforrás egy sablon központi telepítése használatával lesz telepítve.
A társításokról az [Azure Custom Providers erőforrás bevezetéscímű témaköre.](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>A beépített házirendek üzembe helyezése 

### <a name="prerequisites"></a>Előfeltételek
Ha a felügyelt alkalmazásnak engedélyekre van szüksége az előfizetéshez egy művelet végrehajtásához, a társítási erőforrás házirend-telepítése nem működne az engedélyek megadása nélkül.

### <a name="policy-assignment"></a>Szabályzat-hozzárendelés
A beépített házirend használatához hozzon létre egy házirend-hozzárendelést, és rendelje hozzá a felügyelt alkalmazásházirend-társítások központi telepítését. Miután a házirend sikeresen hozzá lett rendelve, a házirend azonosítja a nem megfelelő erőforrásokat, és telepíti az erőforrások társítását.

![Beépített házirend hozzárendelése](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Segítség kérése

Ha kérdése van az Azure egyéni erőforrás-szolgáltatók fejlesztésével kapcsolatban, próbálja meg feltenni őket a [Veremtúlcsordulás szolgáltatásban.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Lehet, hogy egy hasonló kérdésre már válaszoltak, ezért először a feladás előtt ellenőrizze. Add hozzá ```azure-custom-providers``` a címkét, hogy gyors választ kapj!

## <a name="next-steps"></a>További lépések

Ebben a cikkben a beépített szabályzat használatával kapcsolatban is olvashat. További információ:

- [Fogalmak: Az Azure egyéni szolgáltatók erőforrás bevezetés](../custom-providers/concepts-resource-onboarding.md)
- [Oktatóanyag: Erőforrás-bevezetés egyéni szolgáltatókkal](../custom-providers/tutorial-resource-onboarding.md)
- [Oktatóanyag: Egyéni műveletek és erőforrások létrehozása az Azure-ban](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Rövid útmutató: Egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](../custom-providers/create-custom-provider.md)
- [Útmutató: Egyéni műveletek hozzáadása azure REST API-hoz](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása Azure REST API-hoz](../custom-providers/custom-providers-resources-endpoint-how-to.md)
