---
title: Egyéni műveletek és erőforrások létrehozása
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egyéni műveleteket és erőforrásokat az Azure Resource Managerben. Azt is bemutatja, hogy az egyéni munkafolyamatok hogyan működnek együtt az Azure Resource Manager-sablonokkal, az Azure CLI-vel, az Azure Policy-del és az Azure-tevékenységnaplóval.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649896"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Egyéni műveletek és erőforrások létrehozása az Azure-ban

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatókkal módosíthatja az Azure munkafolyamatait, ha új API-kat ad hozzá az Azure Resource Managerhez. Ezekkel az egyéni API-kkal az Erőforrás-kezelő új üzembe helyezési és felügyeleti képességeket használhat.

Ez az oktatóanyag egy egyszerű példa, hogyan adhat hozzá új műveleteket és erőforrásokat az Azure-hoz, és hogyan integrálhatja őket.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure-függvények beállítása egyéni Azure-szolgáltatókhoz

Az oktatóanyag első része azt ismerteti, hogyan állíthat be egy Azure függvényalkalmazást az egyéni szolgáltatókkal való együttműködésre:

- [Azure-függvények beállítása egyéni Azure-szolgáltatókhoz](./tutorial-custom-providers-function-setup.md)

Az egyéni szolgáltatók bármilyen nyilvános URL-címmel dolgozhatnak.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>ReSTful végpont készítése egyéni szolgáltatók számára

Az oktatóanyag második része azt ismerteti, hogyan lehet restful végpontot írni egyéni szolgáltatók számára:

- [RESTful végpont készítése egyéni szolgáltatók számára](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Egyéni szolgáltató létrehozása és használata

Az oktatóanyag harmadik része azt ismerteti, hogy miként hozhat létre egyéni szolgáltatót, és hogyan használhatja az egyéni műveleteket és erőforrásokat:

- [Egyéni szolgáltató létrehozása és használata](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett az egyéni szolgáltatókkal és az egyéni szolgáltatók kal. A következő oktatóanyag folytatásához olvassa el [az Oktatóanyag: Azure-függvények beállítása az Azure egyéni szolgáltatókhoz című témakört.](./tutorial-custom-providers-function-setup.md)

Ha referenciákat vagy rövid útmutatót keres, az alábbiakban néhány hasznos linket olvashat:

- [Rövid útmutató: Hozzon létre egy egyéni Azure-erőforrás-szolgáltatót, és telepítsen egyéni erőforrásokat](./create-custom-provider.md)
- [Útmutató: Egyéni műveletek hozzáadása az Azure REST API-hoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST API-hoz](./custom-providers-resources-endpoint-how-to.md)
