---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: e5213a20036385ff25373252e6670d8bd86a4b07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80056557"
---
Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatók, oktatóanyagok vagy az ebben a rövid útmutatóban létrehozott bármelyik szolgáltatással dolgozik, ne törölje az erőforrásokat.

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. *Erőforráscsoportokba*vannak csoportosítva, és a csoport törlésével törölheti a csoport összes elemét. 

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

1. Az Azure Portalon nyissa meg az **Erőforráscsoport** oldalt. 

   Ahhoz, hogy eljusson erre az oldalra a függvényalkalmazás oldaláról, az **Áttekintés** lapon kattintson az **Erőforráscsoport** alatti hivatkozásra.

   ![Válassza ki az erőforráscsoportot, amelyet törölni kíván a függvényalkalmazás oldaláról.](./media/functions-quickstart-cleanup/functions-app-delete-resource-group.png)

   Ahhoz, hogy eljusson erre az oldalra az irányítópultról, az **Erőforráscsoportok** területen válassza ki a jelen rövid útmutatóban használt erőforráscsoportot.

2. Az **erőforráscsoport** lapon tekintse át a befoglalt erőforrások listáját, és ellenőrizze, hogy a törölni kívántak-e.
 
3. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.
