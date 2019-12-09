---
title: Társítások üzembe helyezése az Azure-beli egyéni szolgáltatóhoz házirend használatával
description: További információ az egyéni szolgáltatók hozzárendeléseinek üzembe helyezéséről Azure Policy szolgáltatás használatával.
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 94f7128a473266824242f52a9c50f09b2c24f1d4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932644"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Egyéni szolgáltatóhoz tartozó társítások üzembe helyezése az Azure Policy használatával

Az Azure-szabályzatok olyan társítások telepítésére használhatók, amelyek az erőforrásokat egy egyéni szolgáltatóhoz rendelik. Ebben a cikkben egy olyan beépített szabályzatot ismertetünk, amely a társításokat telepíti, és hogyan használhatja ezt a házirendet.

## <a name="built-in-policy-to-deploy-associations"></a>Beépített szabályzat a társítások telepítéséhez

Az egyéni szolgáltatóhoz tartozó társítások üzembe helyezése egy beépített szabályzat, amely az erőforrások egyéni szolgáltatóhoz társítására használható. A szabályzat három paramétert fogad el:

- Egyéni szolgáltató azonosítója – ez az azonosító annak az egyéni szolgáltatónak az erőforrás-azonosítója, amelyhez hozzá kell rendelni az erőforrásokat.
- Társítandó erőforrástípusok – ezek az erőforrástípusok az egyéni szolgáltatóhoz társítandó erőforrástípusok listája. Ugyanahhoz a Szabályzathoz több erőforrástípust is hozzárendelhet egy egyéni szolgáltatóhoz.
- Társítás neve előtag – ez a karakterlánc a létrehozandó társítási erőforrás neveként hozzáadandó előtag. Az alapértelmezett érték a "DeployedByPolicy".

A szabályzat DeployIfNotExists-értékelést használ. Azt követően fut, hogy egy erőforrás-szolgáltató kezelt egy Create vagy Update típusú erőforrás-kérelmet, és a kiértékelés sikeres állapotkódot adott vissza. Ezt követően a társítási erőforrás üzembe helyezése sablon-telepítés használatával történik.
További információ a társításokról: [Azure Custom Providers erőforrás](./concepts-custom-providers-resourceonboarding.md) bevezetése

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>A beépített társítási szabályzat használata 

### <a name="prerequisites"></a>Előfeltételek
Ha az egyéni szolgáltatónak engedélyre van szüksége a szabályzat hatóköréhez a művelet elvégzéséhez, a társítási erőforrás házirend-telepítése nem fog működni az engedélyek megadása nélkül.

### <a name="policy-assignment"></a>Szabályzat-hozzárendelés
A beépített házirend használatához hozzon létre egy szabályzat-hozzárendelést, és rendelje hozzá az egyéni szolgáltatói házirendhez tartozó központi telepítéseket. A házirend ezután azonosítja a nem megfelelő erőforrásokat, és a társítást az adott erőforrásokhoz is telepíti.

![Beépített szabályzat kiosztása](media/builtin-policy/assign-builtin-policy-customprovider.png)

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
