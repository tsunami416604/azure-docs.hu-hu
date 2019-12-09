---
title: Társítások üzembe helyezése az Azure által felügyelt alkalmazáshoz házirend használatával
description: Ismerje meg, hogyan telepíthet társításokat felügyelt alkalmazásokhoz Azure Policy szolgáltatás használatával.
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: e3dda343c1d5664e188b434c54e4c288b515d420
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932527"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Felügyelt alkalmazáshoz tartozó társítások telepítése Azure Policy használatával

Az Azure-szabályzatok segítségével társításokat helyezhet üzembe az erőforrások felügyelt alkalmazásokhoz való társításához. Ebben a cikkben egy olyan beépített szabályzatot ismertetünk, amely a társításokat telepíti, és hogyan használhatja ezt a házirendet.

## <a name="built-in-policy-to-deploy-associations"></a>Beépített szabályzat a társítások telepítéséhez

A felügyelt alkalmazásokhoz tartozó társítások üzembe helyezése egy beépített szabályzat, amely az erőforrások felügyelt alkalmazásokhoz társítására használható. A szabályzat három paramétert fogad el:

- Felügyelt alkalmazás azonosítója – ez az azonosító annak a felügyelt alkalmazásnak az erőforrás-azonosítója, amelyhez az erőforrásokat társítani kell.
- Társítandó erőforrástípusok – ezek az erőforrástípusok a felügyelt alkalmazáshoz társítandó erőforrástípusok listája. Több erőforrástípust is hozzárendelhet egy felügyelt alkalmazáshoz ugyanazzal a házirenddel.
- Társítás neve előtag – ez a karakterlánc a létrehozandó társítási erőforrás neveként hozzáadandó előtag. Az alapértelmezett érték a "DeployedByPolicy".

A szabályzat DeployIfNotExists-értékelést használ. Azt követően fut, hogy az erőforrás-szolgáltató a kiválasztott erőforrástípus (ok) hoz létre vagy frissít erőforrás-kérelmet, és a kiértékelés sikeres állapotkódot adott vissza. Ezt követően a társítási erőforrás üzembe helyezése sablon-telepítés használatával történik.
További információ a társításokról: [Azure Custom Providers erőforrás](./concepts-custom-providers-resourceonboarding.md) bevezetése

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>A beépített társítási szabályzat használata 

### <a name="prerequisites"></a>Előfeltételek
Ha a felügyelt alkalmazásnak engedélyre van szüksége az előfizetéshez, a művelet végrehajtásához a társítási erőforrás házirend-telepítése nem fog működni az engedélyek megadása nélkül.

### <a name="policy-assignment"></a>Szabályzat-hozzárendelés
A beépített házirend használatához hozzon létre egy szabályzat-hozzárendelést, és rendelje hozzá a felügyelt alkalmazás-házirendhez tartozó központi telepítéseket. Miután a szabályzat sikeresen hozzá lett rendelve, a szabályzat azonosítja a nem megfelelő erőforrásokat, és nem telepíti a társítást ezekhez az erőforrásokhoz.

![Beépített szabályzat kiosztása](media/builtin-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Segítség kérése

Ha kérdése van az Azure-beli egyéni erőforrás-szolgáltatók fejlesztésével kapcsolatban, próbálja meg megkérdezni őket [stack Overflowon](https://stackoverflow.com/questions/tagged/azure-custom-providers). Egy hasonló kérdés megválaszolása már megtörtént, ezért először A feladás előtt érdemes megnéznie. A címke ```azure-custom-providers``` hozzáadásával gyorsan választ kaphat!

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtanulta, hogyan telepíthet a beépített szabályzatot a társítások üzembe helyezésére. További információt a következő cikkekben talál:

- [Fogalmak: Azure Custom Providers erőforrás-előkészítés](./concepts-custom-providers-resourceonboarding.md)
- [Oktatóanyag: erőforrás-előkészítés egyéni szolgáltatókkal](./tutorial-custom-providers-resource-onboarding.md)
- [Oktatóanyag: egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-custom-providers-101.md)
- [Gyors útmutató: egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Útmutató: egyéni műveletek hozzáadása egy Azure-REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: egyéni erőforrások hozzáadása Azure-REST APIhoz](./custom-providers-resources-endpoint-how-to.md)
