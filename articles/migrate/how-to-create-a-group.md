---
title: Csoportban értékeléséhez Azure telepítse át a gépek |} Microsoft Docs
description: Útmutatás a gépek az Azure áttelepítése szolgáltatással értékelését futtatása előtt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: ccab88c0195a7ca459c8579b7870d121dfd0fe1d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231406"
---
# <a name="group-machines-for-assessment"></a>Csoport gépek értékeléshez

Ez a cikk ismerteti az értékelés szerint gépcsoport létrehozása [Azure áttelepítése](migrate-overview.md). Azure áttelepítése értékeli a csoport, ellenőrizze, hogy azok alkalmas áttelepítése az Azure-ba, és biztosít az Azure-beli a gép méretezési és költségű becsléseket gépek.


## <a name="create-a-group"></a>Hozzon létre egy csoportot

1. A a **áttekintése** Azure áttelepítése projekt kezelése, kattintson a **csoportok** > **+ csoport**, és adjon meg egy felügyeleticsoport-nevet.
2. Egy vagy több gépek felvétele a csoportba, és kattintson a **létrehozása**. 
3. Bejelölésével opcionálisan megadhatja a csoport egy új assessment futtatásához. 

    ![Hozzon létre egy csoportot](./media/how-to-create-a-group/create-group.png)

A csoport létrehozása után módosíthatja a csoport választva módosíthatja a **csoportok** lap, és hozzáadása vagy eltávolítása a gépek.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan használható [gép függőségi leképezési](how-to-create-group-machine-dependencies.md) magas megbízhatósági csoportok létrehozásához.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
