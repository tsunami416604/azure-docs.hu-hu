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
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692576"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Tárfiók létrehozása az Event Processor Host számára
Az Event Processor Host egy intelligens ügynök, amely leegyszerűsíti az események fogadását az Event Hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat. Az ellenőrzőpontok használatához az Event Processor Hostnak tárfiókra van szüksége. Az alábbi példa bemutatja, hogyan hozhat létre tárfiókot és hogyan szerezheti be a hozzáférési kulcsokat:

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.

    ![Erőforrás-menüelem létrehozása, Microsoft Azure portál](./media/event-hubs-create-storage/create-resource.png)

2. Válassza **a Storage** > **Storage fiók lehetőséget.**
   
    ![Válassza ki a Tárfiókot, a Microsoft Azure portált](./media/event-hubs-create-storage/select-storage-account.png)

3. A **Tárfiók létrehozása** oldalon hajtsa végre az alábbi lépéseket: 

   1. Adja meg a **Tárfiók nevét**.
   2. Válasszon egy **Azure-előfizetést,** amely tartalmazza az eseményközpontot.
   3. Válassza ki vagy hozza létre az **erőforráscsoportot,** amely rendelkezik az eseményközponttal.
   4. Válassza ki **azt a helyet,** ahol az erőforrást létre szeretné hozni. 
   5. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
        ![Véleményezés + létrehozás, Tárfiók létrehozása, Microsoft Azure portál](./media/event-hubs-create-storage/review-create.png)

4. A **Felülvizsgálat + létrehozás** oldalon tekintse át az értékeket, majd kattintson a **Létrehozás** lehetőségre. 

    ![Tekintse át a tárfiók beállításait, és hozzon létre, a Microsoft Azure portál](./media/event-hubs-create-storage/create-storage-account.png)
5. Miután megjelenik a **központi telepítések sikeres** üzenetet az értesítésekben, válassza az Ugrás az **erőforrásra** lehetőséget a Tárfiók lap megnyitásához. Azt is megteheti, hogy kibontja **a Központi telepítés részleteit,** majd kiválassza az új erőforrást az erőforráslistából.  

    ![Ugrás az erőforrásra, a tárfiók telepítésére, a Microsoft Azure portalra](./media/event-hubs-create-storage/go-to-resource.png) 
6. Válassza a **Tárolók**lehetőséget.

    ![Válassza ki a Blobs tárolószolgáltatást, a tárfiókokat, a Microsoft Azure portalt](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Válassza a **+ Tároló** lehetőséget a tetején, adja meg a tároló **nevét,** és kattintson az **OK gombra.** 

    ![Új blobtároló, tárfiókok, Microsoft Azure portal létrehozása](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Válassza a **Tárfiók** lap menüjének **Access-billentyűk** parancsát, és másolja a **key1**értékét.

    Mentse az alábbi értékeket a Jegyzettömbbe vagy más ideiglenes helyre.
    - A tárfiók neve
    - A tárfiók hozzáférési kulcsa
    - A tároló neve
