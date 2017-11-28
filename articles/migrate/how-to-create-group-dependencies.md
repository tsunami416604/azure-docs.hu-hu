---
title: "Egy értékelési csoport csoport függőségi leképezéssel Azure telepítse át a pontosítsa |} Microsoft Docs"
description: "Ismerteti, hogyan pontosítsa csoport függőségi leképezés használata az Azure áttelepítése szolgáltatásban értékelését."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: b4d6861f147fbb6e65a9d529f17f78b54075eb90
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Pontosítsa a csoportot a függőségi leképezése

Ez a cikk ismerteti, hogyan csoport függőségi leképezést beállítása [Azure áttelepítése](migrate-overview.md) assessment. Általában ezt a módszert helyzet finomíthatja egy meglévő csoport beállításainak ellenőrzése függőségek, a értékelését futtatása előtt. Csoportok függőségi leképezése futtatni kívánt több mint 10 gépet nem szabad tartalmaznia.  

## <a name="modify-a-group"></a>Csoport módosítása

1. Az Azure-ban áttelepítése projekt, a **kezelése**, kattintson a **csoportok**, és válassza ki azt a csoportot.
2. A csoport lapján, kattintson a **függőségeinek megtekintése**kattintva nyissa meg a csoport kapcsolatfüggőségi térképének megjelenítéséhez. 

     ![Csoport megtekintése](./media/how-to-create-group-dependencies/create-group.png)

3. Részletesebb függőségek megtekintéséhez kattintson az időtartományt módosítható. Alapértelmezés szerint a tartománya egy óra. Módosítsa az időtartományt, vagy adja meg a kezdő és záró dátumát, és időtartama.
4. Ctrl + kattintás segítségével válassza ki a gépeket a térképen. Adja hozzá, vagy távolítsa el a térkép gépet.
    - Csak a felderített gépeket adhat hozzá.
    - Hozzáadása és a gép eltávolítása egy csoportból érvényteleníti az értékelések részhez.
    - Egy új assessment másik lehetőségként létrehozhatja, ha a csoport módosítása.
5. Kattintson a **OK** kell elmentenie a csoportot.

    ![Hozzáadása és eltávolítása](./media/how-to-create-group-dependencies/add-remove.png)

Ha egy adott gépen futó, a csoport függőségi térkép függőségeit ellenőrizni kívánja [gép függőségi megfeleltetés beállítása](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Következő lépések

[További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
