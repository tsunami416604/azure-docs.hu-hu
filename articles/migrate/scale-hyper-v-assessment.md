---
title: Nagy számú Hyper-V virtuális gép felmassa az Azure-ba való áttelepítéshez az Azure Migrate | Microsoft dokumentumok
description: Bemutatja, hogyan értékelheti a nagy számú Hyper-V virtuális gépek az Azure-ba az Azure Áttelepítés szolgáltatás használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70279438"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Nagy számú Hyper-V virtuális gép felmassza az Azure-ba való migráláshoz

Ez a cikk ismerteti, hogyan értékelheti a nagy számú helyszíni Hyper-V virtuális gépek az Azure-ba való áttelepítés, az Azure Áttelepítési kiszolgáló értékelése eszközzel.

[Az Azure Migrate](migrate-services-overview.md) olyan eszközök központi eszköze, amelyek segítségével felderítheti, felmérheti és áttelepítheti az alkalmazásokat, az infrastruktúrát és a számítási feladatokat a Microsoft Azure-ba. A központ tartalmazza az Azure Migrate eszközöket és a külső független szoftverszállítói (ISV) ajánlatokat. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Nagyszabású értékelési terv.
> * Konfigurálja az Azure-engedélyeket, és készítse elő a Hyper-V-t az értékeléshez.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezésekor.


> [!NOTE]
> Ha ki szeretné próbálni a proof-of-concept, hogy értékelje egy pár virtuális gépek értékelése előtt nagy méretekben, kövesse [az oktatóanyag-sorozat](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Értékelési terv

A Hyper-V virtuális gépek nagy számának felmérése során néhány dolgot át kell gondolnia:

- **Tervezze meg az Azure Migrate-projekteket:** Az Azure Migrate-projektek üzembe helyezésének kivárható. Ha például az adatközpontok különböző földrajzi területeken találhatók, vagy a felderítéssel, értékeléssel vagy áttelepítéssel kapcsolatos metaadatokat egy másik földrajzi helyen kell tárolnia, előfordulhat, hogy több projektre van szüksége.
- **Berendezések tervezése:** Az Azure Migrate egy helyszíni Azure Migrate-készüléket használ, amelyet Hyper-V virtuális gépként telepítenek üzembe, hogy folyamatosan felderítse a virtuális gépeket az értékelés hez és az áttelepítéshez. A készülék figyeli a környezeti változásokat, például virtuális gépek, lemezek vagy hálózati adapterek hozzáadását. Metaadatokat és teljesítményadatokat is küld az Azure-ba. Ki kell találnia, hogy hány berendezést kell telepítenie.


## <a name="planning-limits"></a>Tervezési korlátok
 
A táblában összegzett korlátokat használja a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Áttelepítési projektek** | Akár 35 000 virtuális gépet is felmérhet egy projektben.
**Azure Migrate-berendezés** | Egy készülék legfeljebb 5000 virtuális gépet fedezhet fel.<br/> Egy készülék legfeljebb 300 Hyper-V állomáshoz tud csatlakozni.<br/> Egy készülék csak egyetlen Azure Migrate projekthez társítható.<br/> Tetszőleges számú készülék társítható egyetlen Azure Migrate projekthez. <br/><br/> 
**Csoport** | Egy csoportban legfeljebb 35 000 virtuális gépet adhat hozzá.
**Azure Áttelepítés felmérése** | Egyetlen értékelés ben legfeljebb 35 000 virtuális gépet értékelhet.



## <a name="other-planning-considerations"></a>Egyéb tervezési szempontok

- A felderítés elindításához a készülékből ki kell választania az egyes Hyper-V gazdagépeket. 
- Ha egy több-bérlős környezetben, jelenleg nem lehet felderíteni csak virtuális gépek, amelyek egy adott bérlőhöz tartozik. 

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Készítse elő az Azure-t és a Hyper-V-t a kiszolgáló felméréséhez. 

1. Ellenőrizze a [Hyper-V támogatási követelményeit és korlátait.](migrate-support-matrix-hyper-v.md)
2. Engedélyek beállítása az Azure-fiókjához az Azure Migrate szolgáltatással való interakcióhoz
3. Hyper-V-állomások és virtuális gépek előkészítése

Az [oktatóanyag](tutorial-prepare-hyper-v.md) utasításainak megfelelően konfigurálhatja ezeket a beállításokat.

## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményeknek megfelelően tegye a következőket:

1. Hozzon létre egy Azure Migrate-projekteket.
2. Adja hozzá az Azure Áttelepítési kiszolgáló értékelése eszközt a projektekhez.

[További információ](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és felülvizsgálata

1. Értékelések létrehozása a Hyper-V virtuális gépekhez.
1. Tekintse át az értékeléseket az áttelepítés tervezésére való felkészülés során.

[További információ](tutorial-assess-hyper-v.md) az értékelések létrehozásáról és áttekintéséről.
    

## <a name="next-steps"></a>További lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * Az Azure Migrate-értékelések méretezése a Hyper-V virtuális gépekhez
> * Előkészített Azure és Hyper-V az értékeléshez
> * Létrehozott egy Azure Migrate projektet, és lefuttatott felméréseket
> * A migrációra való felkészülés során felülvizsgált értékelések.

Most, [ismerje meg, hogyan](concepts-assessment-calculation.md) értékelések kiszámítása, és hogyan kell módosítani [értékelések](how-to-modify-assessment.md)
