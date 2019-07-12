---
title: Egyéni műveletek és az erőforrások létrehozása az Azure-ban
description: Ez az oktatóanyag az egyéni műveletek és az erőforrások létrehozása az Azure Resource Manager és az hogyan integrálhatók őket egyéni munkafolyamatokat az Azure Resource Manager-sablonok, az Azure CLI, Azure Policy és tevékenységnapló kerül.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800042"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Egyéni műveletek és az erőforrások létrehozása az Azure-ban

Egyéni szolgáltatók lehetővé teszik az Azure-ban a munkafolyamatok testreszabása. Egy egyéni szolgáltató az Azure között létrejött szerződés és a egy `endpoint`. Lehetővé teszi új egyéni API-k hozzáadását az az Azure Resource Manager új üzembe helyezési és felügyeleti funkciók engedélyezéséhez. Ebben az oktatóanyagban egy egyszerű példa az új műveletek és az erőforrások hozzáadása az Azure-ba, és hogyan integrálhatja azokat fog áthaladni.

Ebben az oktatóanyagban meg van osztva az alábbi lépéseket:

- Az Azure Functions beállítása az Azure egyéni szolgáltatók
- Jelentésszerkesztő egy RESTful-végpont egyéni szolgáltatók számára
- Létrehozás és a egyéni Provider

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Az Azure Functions beállítása az Azure egyéni szolgáltatók

Az oktatóanyag ezen részében egy Azure-függvényt az egyéni szolgáltatók telepítés részleteinek kerül. Egyéni szolgáltatók képes együttműködni minden olyan nyilvános URL-CÍMÉT.

- [Az Azure Functions beállítása az Azure egyéni szolgáltatók](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Jelentésszerkesztő egy RESTful-végpont egyéni szolgáltatók számára

Az oktatóanyag ezen részében részletes információk a létrehozásról egy RESTful-végpont egyéni szolgáltatók lépnek.

- [Jelentésszerkesztő egy RESTful-végpont egyéni szolgáltatók számára](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>Létrehozás és a egyéni Provider

Az oktatóanyag ezen részében hozzon létre egy egyéni szolgáltatót, és az egyéni műveletek és az erőforrások használata részletesen kerül.

- [Hozzon létre, és felhasználja az Azure egyéni szolgáltató](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>További lépések

Ebből a cikkből megismerhette egyéni szolgáltatókat, és hogyan hozzon létre egyet. Az a következő lépés az oktatóanyag folytatása:

- [Oktatóanyag: Az Azure Functions beállítása az Azure egyéni szolgáltatók](./tutorial-custom-providers-function-setup.md)

Ha a keresett hivatkozásokat vagy egy rövid, az alábbiakban néhány hasznos hivatkozás:

- [Rövid útmutató: Az Azure egyéni erőforrás-szolgáltató létrehozása és telepítése az egyéni erőforrások](./create-custom-provider.md)
- [Útmutató: Az egyéni műveletek hozzáadása az Azure REST API-val](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST API-val](./custom-providers-resources-endpoint-how-to.md)
