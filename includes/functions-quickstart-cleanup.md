---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a1bedf97f5ae7c48e5bcb47880a25ee1219b917b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131635"
---
Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épít. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az ebben a rövid útmutatóban létrehozott szolgáltatások valamelyikével dolgozik tovább, akkor ne törölje az erőforrásokat.

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. Az erőforrások *erőforráscsoportokba* vannak csoportosítva, és az adott csoport törlésével a benne lévő összes erőforrás törölhető. 

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

1. Az Azure Portalon nyissa meg az **Erőforráscsoport** oldalt. 

   Ahhoz, hogy eljusson erre az oldalra a függvényalkalmazás oldaláról, az **Áttekintés** lapon kattintson az **Erőforráscsoport** alatti hivatkozásra.

   ![Válassza ki az erőforráscsoportot, amelyet törölni kíván a függvényalkalmazás oldaláról.](./media/functions-quickstart-cleanup/functions-app-delete-resource-group.png)

   Ahhoz, hogy eljusson erre az oldalra az irányítópultról, az **Erőforráscsoportok** területen válassza ki a jelen rövid útmutatóban használt erőforráscsoportot.

2. Az **Erőforráscsoport** oldalon tekintse át a csoportban lévő erőforrások listáját, és győződjön meg arról, hogy a törölni kívánt elemek szerepelnek benne.
 
3. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.
