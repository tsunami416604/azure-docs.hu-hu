---
title: "Csoportban értékeléséhez Azure telepítse át a gépek |} Microsoft Docs"
description: "Útmutatás a gépek az Azure áttelepítése szolgáltatással értékelését futtatása előtt."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
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
