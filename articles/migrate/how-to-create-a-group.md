---
title: Az Azure Migrate-tel gépeket |} A Microsoft Docs
description: Ismerteti, hogyan lehet gépeket az Azure Migrate szolgáltatással értékelés futtatása előtt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252422"
---
# <a name="group-machines-for-assessment"></a>A gépeket a

Ez a cikk bemutatja, hogyan hozhat létre az értékelés szerint gépcsoport [Azure Migrate](migrate-overview.md). Az Azure Migrate, ellenőrizze, hogy azok még az Azure-ba való migrálásra alkalmas, és a gép futtatása az Azure-ban a méretezési és becsléseket biztosít a csoportban található gépek értékeli. Ha tudja, hogy a gépek, amelyeket együttesen kell áttelepíteni, manuálisan is létrehozhat a csoport az Azure Migrate használatával a következő metódust. Ha nem biztos nagyon kapcsolatban, amely csoportosítja a kell a gépek, használhatja a függőségek képi megjelenítésének funkcióival az Azure Migrate hozhat létre csoportokat. [Részletek](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> A függőségek képi megjelenítésének funkcióival nem érhető el az Azure Government szolgáltatásban.

## <a name="create-a-group"></a>Csoport létrehozása

1. Az a **áttekintése** az Azure Migrate-projektben, kezelés, területen kattintson a **csoportok** > **+ csoport**, és adjon meg egy csoportnevet.
2. Adjon hozzá egy vagy több gépet a csoporthoz, és kattintson a **létrehozás**.
3. Választhatja a csoport egy új értékelés futtatása igény szerint.

    ![Csoport létrehozása](./media/how-to-create-a-group/create-group.png)

A csoport létrehozása után módosítja a csoport választva a **csoportok** lapot, és ezután hozzáadásával vagy eltávolításával gépek.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használható [gépfüggőségi leképezések](how-to-create-group-machine-dependencies.md) nagy megbízhatóságú csoportokat létrehozni.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
