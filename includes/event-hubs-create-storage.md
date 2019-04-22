---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b84b0a8e09bf739ce62dee167ff751b491765c66
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684667"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Tárfiók létrehozása az Event Processor Host számára
Az Event Processor Host egy intelligens ügynök, amely leegyszerűsíti az események fogadását az Event Hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat. Az ellenőrzőpontok használatához az Event Processor Hostnak tárfiókra van szüksége. Az alábbi példa bemutatja, hogyan hozhat létre tárfiókot és hogyan szerezheti be a hozzáférési kulcsokat:

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra a képernyő bal felső részén.

2. Válassza a **Storage** elemet, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget.
   
    ![Tárfiók kiválasztása](./media/event-hubs-create-storage/create-storage1.png)

3. A **Tárfiók létrehozása** oldalon hajtsa végre az alábbi lépéseket: 

   1. Adja meg a tárfiók nevét. 
   2. Válassza ki az eseményközpontot tartalmazó Azure-előfizetést.
   3. Válassza ki az eseményközpontot tartalmazó erőforráscsoportot.
   4. Válasszon ki egy helyet az erőforrás létrehozásához. 
   5. Ezután kattintson a **Felülvizsgálat + létrehozás** elemre.
   
      ![Tárfiók létrehozása – oldal](./media/event-hubs-create-storage/create-storage2.png)

4. A **Felülvizsgálat + létrehozás** oldalon tekintse át az értékeket, majd kattintson a **Létrehozás** lehetőségre. 

    ![A tárfiók beállításainak áttekintése és létrehozás](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Miután látta, a **üzembe helyezések sikerültek** üzenetben válassza **erőforrás megnyitása** az oldal tetején. A Storage-fiók lap az erőforrások listájából válassza ki a tárfiók is elindíthatja.  

    ![A tárfiók kiválasztása az üzemelő példányból](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. Az **Alapvető erőforrások** ablakban kattintson a **Blobok** elemre. 

    ![Válassza ki a Blobok szolgáltatást](./media/event-hubs-create-storage/select-blobs-service.png)
1. Válassza ki **+ tároló** a lap tetején adja meg egy **neve** a tárolót, és válassza a **OK**. 

    ![Blobtároló létrehozása](./media/event-hubs-create-storage/create-blob-container.png)
1. Válassza ki **hozzáférési kulcsok** a bal oldali menüben, és másolja az értéket a **key1**. 

    Mentse a következő értékeket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
    - A tárfiók neve
    - A tárfiók hozzáférési kulcsát
    - A tároló nevét.
