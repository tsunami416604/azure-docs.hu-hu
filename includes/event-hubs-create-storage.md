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
ms.openlocfilehash: e499a0f7bec47e672c599c729a15cc3e3d04a28a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471626"
---
## <a name="create-a-storage-account-for-event-processor-host"></a>Tárfiók létrehozása az Event Processor Host számára
Az Event Processor Host egy intelligens ügynök, amely leegyszerűsíti az események fogadását az Event Hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat. Az ellenőrzőpontok használatához az Event Processor Hostnak tárfiókra van szüksége. Az alábbi példa bemutatja, hogyan hozhat létre tárfiókot és hogyan szerezheti be a hozzáférési kulcsokat:

1. Az Azure portálra, és kattintson a **erőforrás létrehozása** , a képernyő bal felső.

2. Válassza a **Storage** elemet, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget.
   
    ![Tárolási fiók kiválasztása](./media/event-hubs-create-storage/create-storage1.png)

3. Az a **storage-fiók létrehozása** lapon, tegye a következőket: 

    1. Adja meg a tárfiók nevét. 
    2. Válassza ki az Azure-előfizetéssel, amely tartalmazza az eseményközpontba.
    3. Válassza ki az erőforráscsoportot, amely az event hubs rendelkezik.
    4. Válasszon egy helyet, ahol az erőforrás létrehozásához. 
    5. Kattintson a **felülvizsgálat + létrehozása**.
   
    ![Storage-fiók létrehozása – lapon](./media/event-hubs-create-storage/create-storage2.png)

4. Az a **tekintse át + létrehozása** lapon tekintse át az értékeket, és válassza ki **létrehozás**. 

    ![Tekintse át a storage-fiók beállításait, és hozzon létre](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Miután látta, a **üzembe helyezések sikerültek** üzenetben válassza **erőforráshoz van** az oldal tetején. A Storage-fiók lap az erőforrások listájából válassza ki a tárfiók is elindíthatja.  

    ![Válassza ki a tárfiókot az üzembe helyezés](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. Az a **Essentials** ablakban válassza **Blobok**. 

    ![Válassza ki a Blobok szolgáltatást](./media/event-hubs-create-storage/select-blobs-service.png)
1. Válassza ki **+ tároló** a lap tetején adja meg egy **neve** a tárolót, és válassza a **OK**. 

    ![Blobtároló létrehozása](./media/event-hubs-create-storage/create-blob-container.png)
1. Válassza ki **hozzáférési kulcsok** a bal oldali menüben, és másolja az értéket a **key1**. 

    Mentse a következő értékeket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
    - A tárfiók neve
    - A tárfiók hozzáférési kulcsát
    - A tároló nevét.