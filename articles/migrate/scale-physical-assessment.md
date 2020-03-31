---
title: Nagyszámú fizikai kiszolgáló felmérése az Azure-ba való áttelepítéshez az Azure Migrate | Microsoft dokumentumok
description: Bemutatja, hogyan értékelheti a fizikai kiszolgálók nagy számú fizikai kiszolgálók az Azure-ba az Azure Áttelepítés szolgáltatás használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294369"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Nagyszámú fizikai kiszolgáló felmérése az Azure-ba való áttelepítéshez

Ez a cikk ismerteti, hogyan értékelheti a helyszíni fizikai kiszolgálók nagy számú azure-ba való áttelepítése az Azure-ba áttelepítése az Azure Áttelepítési kiszolgáló értékelése eszközzel.

[Az Azure Migrate](migrate-services-overview.md) olyan eszközök központi eszköze, amelyek segítségével felderítheti, felmérheti és áttelepítheti az alkalmazásokat, az infrastruktúrát és a számítási feladatokat a Microsoft Azure-ba. A központ tartalmazza az Azure Migrate eszközöket és a külső független szoftverszállítói (ISV) ajánlatokat. 


Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Nagyszabású értékelési terv.
> * Konfigurálja az Azure-engedélyeket, és készítse elő a fizikai kiszolgálókat az értékelésre.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezésekor.


> [!NOTE]
> Ha azt szeretnénk, hogy próbálja ki a proof-of-concept, hogy értékelje egy pár szerver értékelése előtt a skála, kövesse [a bemutató sorozat](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Értékelési terv

A nagyszámú fizikai kiszolgáló értékelésének tervezésekor néhány dolgot át kell gondolni:

- **Tervezze meg az Azure Migrate-projekteket:** Az Azure Migrate-projektek üzembe helyezésének kivárható. Ha például az adatközpontok különböző földrajzi területeken találhatók, vagy a felderítéssel, értékeléssel vagy áttelepítéssel kapcsolatos metaadatokat egy másik földrajzi helyen kell tárolnia, előfordulhat, hogy több projektre van szüksége.
- **Berendezések tervezése:** Az Azure Migrate egy helyszíni Azure Migrate készüléket használ, amelyet egy Windows-gépen telepítenek, hogy folyamatosan felderítse a kiszolgálókat az értékelés és az áttelepítés számára. A készülék figyeli a környezeti változásokat, például virtuális gépek, lemezek vagy hálózati adapterek hozzáadását. Metaadatokat és teljesítményadatokat is küld az Azure-ba. Ki kell találnia, hogy hány berendezést kell telepítenie.


## <a name="planning-limits"></a>Tervezési korlátok
 
A táblában összegzett korlátokat használja a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Áttelepítési projektek** | A projektben akár 35 000 kiszolgálót is felmérhet.
**Azure Migrate-berendezés** | Egy készülék legfeljebb 250 kiszolgálót fedezhet fel.<br/> Egy készülék csak egyetlen Azure Migrate projekthez társítható.<br/> Tetszőleges számú készülék társítható egyetlen Azure Migrate projekthez. <br/><br/> 
**Csoport** | Egy csoportban legfeljebb 35 000 kiszolgálót adhat hozzá.
**Azure Áttelepítés felmérése** | Egyetlen értékelés alatt legfeljebb 35 000 kiszolgálót értékelhet.


## <a name="other-planning-considerations"></a>Egyéb tervezési szempontok

- A felderítés elindításához a készülékről ki kell választania az egyes fizikai kiszolgálókat. 

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Készítse elő az Azure-t és a fizikai kiszolgálókat a kiszolgáló konklisen. 

1. Ellenőrizze a [kiszolgáló fizikai támogatási követelményeit és korlátait.](migrate-support-matrix-physical.md)
2. Állítsa be az Azure-fiók engedélyeit az Azure Migrate szolgáltatással való együttműködéshez.
3. Készítsd elő a fizikai szervereket.

Az [oktatóanyag](tutorial-prepare-physical.md) utasításainak megfelelően konfigurálhatja ezeket a beállításokat.

## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményeknek megfelelően tegye a következőket:

1. Azure Migrate-projekt létrehozása.
2. Adja hozzá az Azure Áttelepítési kiszolgáló értékelése eszközt a projektekhez.

[További információ](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és felülvizsgálata

1. Értékelések létrehozása fizikai kiszolgálókhoz.
1. Tekintse át az értékeléseket az áttelepítés tervezésére való felkészülés során.

[További információ](tutorial-assess-physical.md) az értékelések létrehozásáról és áttekintéséről.
    

## <a name="next-steps"></a>További lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * Az Azure Migrate-értékelések fizikai kiszolgálókra való méretezése.
> * Előkészített Azure és fizikai kiszolgálók az értékeléshez.
> * Létrehozott egy Azure Migrate projektet, és lefuttatott felméréseket.
> * A migrációra való felkészülés során felülvizsgált értékelések.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) számítják ki az értékeléseket, és hogyan módosíthatják az [értékeléseket.](how-to-modify-assessment.md)
