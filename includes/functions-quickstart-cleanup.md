---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: e5213a20036385ff25373252e6670d8bd86a4b07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056557"
---
Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy a következő rövid útmutatók, oktatóanyagok, vagy bármely, a szolgáltatások létrehozott ebben a rövid útmutató, ne törölje az erőforrásokat.

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. *Erőforráscsoportokba*vannak csoportosítva, és a csoport törlésével törölheti a csoport minden elemét. 

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

1. Az Azure Portalon nyissa meg az **Erőforráscsoport** oldalt. 

   Ahhoz, hogy eljusson erre az oldalra a függvényalkalmazás oldaláról, az **Áttekintés** lapon kattintson az **Erőforráscsoport** alatti hivatkozásra.

   ![Válassza ki az erőforráscsoportot, amelyet törölni kíván a függvényalkalmazás oldaláról.](./media/functions-quickstart-cleanup/functions-app-delete-resource-group.png)

   Ahhoz, hogy eljusson erre az oldalra az irányítópultról, az **Erőforráscsoportok** területen válassza ki a jelen rövid útmutatóban használt erőforráscsoportot.

2. Az **Erőforráscsoport** lapon tekintse át a belefoglalt erőforrások listáját, és ellenőrizze, hogy ezek azok-e, amelyeket törölni szeretne.
 
3. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.
