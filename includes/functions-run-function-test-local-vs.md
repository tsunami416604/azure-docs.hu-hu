---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056581"
---
1. A funkció futtatásához nyomja le az F5 billentyűt a Visual Studio alkalmazásban. Előfordulhat, hogy engedélyeznie kell egy tűzfalkivételt, hogy az eszközök kezelni tudják a HTTP-kérelmeket. Az engedélyezési szintek soha nem lépnek érvénybe, ha helyileg futtat egy függvényt.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Fűzze hozzá `?name=<YOUR_NAME>` a lekérdezési karakterláncot ehhez az URL-címhez, és futtassa a kérelmet. Az alábbi képen a böngészőben a függvény által visszaadott helyi GET-kérésre adott válasz látható: 

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. A hibakeresés leállításához nyomja le a Shift+F5 billentyűkombinációt a Visual Studióban.