---
title: Egyéni műveletek és erőforrások létrehozása az Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyéni műveleteket és erőforrásokat a Azure Resource Managerban. Azt is bemutatja, hogyan működnek együtt az egyéni munkafolyamatok a Azure Resource Manager sablonokkal, az Azure CLI-vel, az Azure Policy és az Azure-beli tevékenységekkel.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172937"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Egyéni műveletek és erőforrások létrehozása az Azure-ban

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatók segítségével az Azure-ban új API-k hozzáadásával Azure Resource Managerba is módosíthatja a munkafolyamatokat. Ezekkel az egyéni API-kkal a Resource Manager új üzembe helyezési és felügyeleti képességeket tud használni.

Ez az oktatóanyag egy egyszerű példát mutat be, hogyan adhat hozzá új műveleteket és erőforrásokat az Azure-hoz, és hogyan integrálhatja őket.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure Functions beállítása egyéni Azure-szolgáltatók számára

Az oktatóanyag első része azt ismerteti, hogyan állítható be egy Azure Function-alkalmazás az egyéni szolgáltatókkal való együttműködéshez:

- [Azure Functions beállítása egyéni Azure-szolgáltatók számára](./tutorial-custom-providers-function-setup.md)

Az egyéni szolgáltatók bármilyen nyilvános URL-címmel működhetnek.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>REST-végpont létrehozása egyéni szolgáltatók számára

Az oktatóanyag második része azt ismerteti, hogyan hozhat létre REST-végpontot az egyéni szolgáltatók számára:

- [REST-végpont létrehozása egyéni szolgáltatók számára](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Egyéni szolgáltató létrehozása és használata

Az oktatóanyag harmadik része azt ismerteti, hogyan hozhat létre egyéni szolgáltatót, és hogyan használhatja az egyéni műveleteit és erőforrásait:

- [Egyéni szolgáltató létrehozása és használata](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az egyéni szolgáltatókat, és hogyan hozhat létre egyet. A következő oktatóanyag folytatásához tekintse [meg az oktatóanyagot: Azure Functions beállítása Azure-beli egyéni szolgáltatók](./tutorial-custom-providers-function-setup.md)számára.

Ha referenciákat vagy rövid útmutatót keres, íme néhány hasznos hivatkozás:

- [Rövid útmutató: Egyéni Azure-erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Útmutató: Egyéni műveletek hozzáadása az Azure REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST APIhoz](./custom-providers-resources-endpoint-how-to.md)
