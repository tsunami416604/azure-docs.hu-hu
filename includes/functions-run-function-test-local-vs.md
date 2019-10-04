---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592899"
---
1. A függvény futtatásához nyomja le az **F5**billentyűt. Előfordulhat, hogy engedélyeznie kell egy tűzfal-kivételt, hogy az eszközök kezelni tudják a HTTP-kérelmeket. A helyi futtatáskor a rendszer soha nem kényszeríti ki az engedélyezési szinteket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az URL-címhez fűzze hozzá a `?name=<YOUR_NAME>` lekérdezési sztringet, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz: 

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. A hibakeresés leállításához nyomja le a **Shift + F5** billentyűkombinációt.