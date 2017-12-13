---
title: "Csoportban értékeléséhez Azure telepítse át a gépek |} Microsoft Docs"
description: "Útmutatás a gépek az Azure áttelepítése szolgáltatással értékelését futtatása előtt."
author: rayne-wiselman
ms.service: azure-migrate
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 429a9150d1fbf50c0e3fa2046eb64affc8db8e5d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="group-machines-for-assessment"></a>Csoport gépek értékeléshez

Ez a cikk ismerteti az értékelés szerint gépcsoport létrehozása [Azure áttelepítése](migrate-overview.md). Azure áttelepítése értékeli a csoport, ellenőrizze, hogy azok alkalmas áttelepítése az Azure-ba, és biztosít az Azure-beli a gép méretezési és költségű becsléseket gépek.


## <a name="create-a-group"></a>Hozzon létre egy csoportot

1. Az a **irányítópult** Azure áttelepítése projekt kattintson **csoportok** > **+ csoport**, és adjon meg egy felügyeleticsoport-nevet.
2. Egy vagy több gépek felvétele a csoportba, és kattintson a **létrehozása**. 
3. Bejelölésével opcionálisan megadhatja a csoport egy új assessment futtatásához. 

    ![Hozzon létre egy csoportot](./media/how-to-create-a-group/create-group.png)

A csoport létrehozása után módosíthatja a csoport választva módosíthatja a **csoportok** lap, és hozzáadása vagy eltávolítása a gépek.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan használható [gép függőségi leképezési](how-to-create-group-machine-dependencies.md) részletesebb csoportok létrehozásához.
- [További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
