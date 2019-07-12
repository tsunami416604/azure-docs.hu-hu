---
title: Mérje fel az Azure-bA az Azure Migrate az áttelepítéshez a Hyper-V virtuális gépek nagy számú |} A Microsoft Docs
description: Ismerteti, hogyan lehet Azure-bA az Azure Migrate szolgáltatás használatával az áttelepítéshez a Hyper-V virtuális gépek nagy számú felmérése.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811543"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Az Azure-bA az áttelepítéshez a Hyper-V virtuális gépek nagy számú felmérése

Ez a cikk ismerteti, hogyan értékelheti a nagy számú (> 1000) a helyszíni Hyper-V rendszerű virtuális gépek áttelepítése az Azure-ba, az Azure Migrate Server Assessment eszközzel.

[Az Azure Migrate](migrate-services-overview.md) hub eszközöket tartalmazza, amelyek segítséget nyújtanak a felderítése, értékelheti és migrálhatja az alkalmazásokat, az infrastruktúra és a számítási feladatok a Microsoft Azure biztosít. A hub tartalmaz, az Azure Migrate eszközökkel és külső független szoftverszállítók (ISV) szállító ajánlatok. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Tervezze meg a felmérés ipari méretekben.
> * Az Azure-engedélyek konfigurálása, és a Hyper-V előkészítése az értékeléshez.
> * Az Azure Migrate-projekt létrehozása, és hozzon létre egy értékelést.
> * Tekintse át az értékelés, migrálás megtervezésekor.


> [!NOTE]
> Ha ki szeretné próbálni a proof-of-concept mérje fel a virtuális gépek néhány ipari méretekben értékelése előtt hajtsa végre a [oktatóanyag-sorozat](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Értékelés tervezése

Tervezésekor értékelése a Hyper-V virtuális gépek nagy számú, van néhány dolgot kell foglalkoznia:

- **Az Azure Migrate a projektek tervezésében**: Döntse el, hogyan helyezhet üzembe az Azure Migrate-projekt. Például ha az adatközpontok különböző földrajzi területeken, vagy egy másik földrajzi felderítési, értékelés vagy migrálási kapcsolatos metaadatokat tárolni kívánt, szüksége lehet a több projektet is.
- **Készülékek megtervezése**: Az Azure Migrate a helyszíni Azure Migrate telepíthetőek, egy Hyper-V virtuális gépként üzembe helyezett virtuális gépek folyamatosan felderítéséhez elemzésekhez és migráláshoz használ. A berendezés figyeli a környezet változásakor, például virtuális gépek, a lemezek vagy a hálózati adapterek hozzáadása. Az Azure-bA velük kapcsolatos metaadatok és a teljesítmény adatokat is küld. Döntse el, hogy hány berendezések üzembe helyezéséhez szükség.


## <a name="planning-limits"></a>Korlátok tervezése
 
Használja a korlátok a tervezési táblázat foglalja össze.

**Tervezés** | **Korlátok**
--- | --- 
**Az Azure Migrate-projekt** | Mérje fel, legfeljebb 10 000 virtuális gépek a projektben.
**Az Azure Migrate-készülék** | Egy készülék legfeljebb 5000-es virtuális gépek képes felderíteni.<br/> Egy készülék legfeljebb 300 Hyper-V-gazdagépek csatlakozhat.<br/> Egy készülék csak társítható egy egyetlen Azure Migrate-projekt.<br/><br/> 
**Az Azure Migrate-értékelés** | Egyetlen értékeléssel akár 10 000 virtuális gépet értékelhet.



## <a name="other-planning-considerations"></a>Egyéb tervezési szempontok

- Felderítés indítása a készülék, válassza ki az egyes Hyper-V-gazdagép rendelkezik. 
- Ha egy több-bérlős környezetben futtatja, akkor nem jelenleg csak virtuális gépek felderítése, amely egy adott bérlő tartozik. 

## <a name="prepare-for-assessment"></a>Értékelés előkészítése

Készítse elő az Azure és Hyper-V kiszolgáló értékelés. 

1. Győződjön meg arról [Hyper-V támogatási követelmények és korlátozások](migrate-support-matrix-hyper-v.md).
2. Az Azure-fiók használatával kommunikálhat az Azure Migrate engedélyek beállítása
3. A Hyper-V-gazdagépek és virtuális gépek előkészítése

Kövesse a [ebben az oktatóanyagban](tutorial-prepare-hyper-v.md) ezek a beállítások konfigurálásához.

## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményeinek megfelelően tegye a következőket:

1. Hozzon létre egy Azure Migrate-projekt.
2. Adja hozzá az Azure Migrate Server Assessment eszköz a projekteket.

[További információ](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Hozzon létre, és tekintse át az értékeléseket

1. Hozzon létre Hyper-V virtuális gépek értékeléseket.
1. Tekintse át az értékeléseket, az áttelepítés tervezéséhez előkészítésekor.

[További](tutorial-assess-hyper-v.md) létrehozásáról, és tekintse át az értékeléseket.
    

## <a name="next-steps"></a>További lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * A Hyper-V virtuális gépek Azure Migrate értékelések méretezése tervezett
> * Az Azure és az értékelés Hyper-V előkészítése
> * Azure Migrate-projekt létrehozása, és futtatta az értékelések
> * Tekintse át a migrálásra való felkészüléskor értékeléseket.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) értékelés kiszámítása, és hogyan [értékelések módosítása](how-to-modify-assessment.md)
