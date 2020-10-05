---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056581"
---
1. A függvény futtatásához nyomja le az F5 billentyűt a Visual Studióban. Előfordulhat, hogy engedélyeznie kell egy tűzfal-kivételt, hogy az eszközök kezelni tudják a HTTP-kérelmeket. A függvény helyi futtatásakor a rendszer soha nem kényszeríti ki az engedélyezési szinteket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Fűzze hozzá a lekérdezési karakterláncot `?name=<YOUR_NAME>` Ehhez az URL-címhez, és futtassa a kérést. Az alábbi képen látható a böngészőben a függvény által visszaadott helyi GET kérelemre adott válasz: 

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. A hibakeresés leállításához nyomja le a SHIFT + F5 billentyűkombinációt a Visual Studióban.