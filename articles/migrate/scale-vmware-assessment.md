---
title: Nagy számú VMware virtuális gép felmérése az Azure-ba való migráláshoz az Azure Migrate segítségével
description: A cikk azt ismerteti, hogy miként lehet felmérni a Virtuálisgép-szolgáltatások nagy számú virtuális gépét az Azure-ba való áttelepítéshez az Azure Migrate szolgáltatás használatával.e
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336850"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Nagyszámú VMware virtuális gép felmérése az Azure-ba való migráláshoz


Ez a cikk ismerteti, hogyan értékelheti a helyszíni VMware virtuális gépek nagy számú (1000-35 000) az Azure-ba való áttelepítéshez az Azure-ba való áttelepítéshez az Azure Áttelepítési kiszolgáló értékelése eszközzel.

[Az Azure Migrate](migrate-services-overview.md) olyan eszközök központi eszköze, amelyek segítségével felderítheti, felmérheti és áttelepítheti az alkalmazásokat, az infrastruktúrát és a számítási feladatokat a Microsoft Azure-ba. A központ tartalmazza az Azure Migrate eszközöket és a külső független szoftverszállítói (ISV) ajánlatokat. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Nagyszabású értékelési terv.
> * Konfigurálja az Azure-engedélyeket, és készítse elő a VMware-t az értékelésre.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezésekor.


> [!NOTE]
> Ha ki szeretné próbálni a proof-of-concept, hogy értékelje egy pár virtuális gépek értékelése előtt nagy méretekben, kövesse [az oktatóanyag-sorozat](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Értékelési terv

A VMware virtuális gépek nagy számának felmérése során néhány dolgot át kell gondolnia:

- **Tervezze meg az Azure Migrate-projekteket:** Az Azure Migrate-projektek üzembe helyezésének kivárható. Ha például az adatközpontok különböző földrajzi területeken találhatók, vagy a felderítéssel, értékeléssel vagy áttelepítéssel kapcsolatos metaadatokat egy másik földrajzi helyen kell tárolnia, előfordulhat, hogy több projektre van szüksége. 
- **Berendezések tervezése:** Az Azure Migrate egy helyszíni Azure Migrate-berendezést használ, amelyet VMware virtuális gépként telepítenek üzembe a virtuális gépek folyamatos felderítéséhez. A készülék figyeli a környezeti változásokat, például virtuális gépek, lemezek vagy hálózati adapterek hozzáadását. Metaadatokat és teljesítményadatokat is küld az Azure-ba. Meg kell, hogy kitaláljuk, hogy hány készüléket kell telepíteni.
- **A felderítési fiókok tervezése:** Az Azure Migrate appliance egy virtuális központ-kiszolgálóhoz hozzáféréssel rendelkező fiókot használ a virtuális gépek felmérése és áttelepítése során. Ha több mint 10 000 virtuális gépet fedez fel, állítson be több fiókot.


## <a name="planning-limits"></a>Tervezési korlátok
 
A táblában összegzett korlátokat használja a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Áttelepítési projektek** | Akár 35 000 virtuális gépet is felmérhet egy projektben.
**Azure Migrate-berendezés** | Egy készülék legfeljebb 10 000 virtuális gépet képes felderíteni egy vCenter-kiszolgálón.<br/> Egy készülék csak egyetlen vCenter-kiszolgálóhoz tud csatlakozni.<br/> Egy készülék csak egyetlen Azure Migrate projekthez társítható.<br/>  Tetszőleges számú készülék társítható egyetlen Azure Migrate projekthez. <br/><br/> 
**Csoport** | Egy csoportban legfeljebb 35 000 virtuális gépet adhat hozzá.
**Azure Áttelepítés felmérése** | Egyetlen értékelés ben legfeljebb 35 000 virtuális gépet értékelhet.

Ezeket a korlátokat szem előtt tartva, íme néhány példa a központi telepítések:


**vCenter-kiszolgáló** | **Virtuális gépek a kiszolgálón** | **Ajánlás** | **Művelet**
---|---|---
Eggyel | 10 000 < | Egy Azure Migrate projekt.<br/> Egy készülék.<br/> Egy vCenter-fiók felderítéséhez. | Állítsa be a készüléket, csatlakozzon a vCenter Server egy fiókkal.
Eggyel | 10 000 > | Egy Azure Migrate projekt.<br/> Több készülék.<br/> Több vCenter-fiók. | Állítsa be a készüléket minden 10 000 virtuális géphez.<br/><br/> VCenter-fiókok beállítása, és a készlet felosztása a fiók hoz való hozzáférés 10 000-nél kevesebb, mint 10 000 virtuális gépek.<br/> Csatlakoztassa az egyes készülékeket a vCenter-kiszolgálóhoz egy fiókkal.<br/> Elemezheti a különböző készülékekkel felderített gépek függőségeit.
Többszörös | 10 000 < |  Egy Azure Migrate projekt.<br/> Több készülék.<br/> Egy vCenter-fiók felderítéséhez. | Állítsa be a készülékeket, csatlakozzon a vCenter Server egy fiókkal.<br/> Elemezheti a különböző készülékekkel felderített gépek függőségeit.
Többszörös | 10 000 > | Egy Azure Migrate projekt.<br/> Több készülék.<br/> Több vCenter-fiók. | Ha a vCenter Server felderítése 10 000 virtuális gépet <, állítson be egy-egy készüléket minden egyes vCenter-kiszolgálóhoz.<br/><br/> Ha a vCenter Server felderítése 10 000 virtuális gépet >, állítson be egy készüléket minden 10 000 virtuális géphez.<br/> VCenter-fiókok beállítása, és a készlet felosztása a fiók hoz való hozzáférés 10 000-nél kevesebb, mint 10 000 virtuális gépek.<br/> Csatlakoztassa az egyes készülékeket a vCenter-kiszolgálóhoz egy fiókkal.<br/> Elemezheti a különböző készülékekkel felderített gépek függőségeit.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Felderítés tervezése több-bérlős környezetben

Ha több-bérlős környezetet tervez, a felderítéshatókörrel a vCenter-kiszolgálón.

- Beállíthatja a készülék felderítési hatókört egy vCenter Server adatközpontok, fürtök vagy mappák fürtök, állomások vagy mappa a gazdagépek, vagy az egyes virtuális gépek.
- Ha a környezet meg van osztva a bérlők között, és szeretné felderíteni az egyes bérlők külön-külön, a hozzáférést a vCenter-fiókhoz, amely a készülék felderítésére használja. 
    - Előfordulhat, hogy virtuális gép mappák szerint szeretné hatókört használni, ha a bérlők megosztják az állomásokat. Az Azure Migrate nem tudja felderíteni a virtuális gépeket, ha a vCenter-fiók a vCenter virtuálisgép mappájának szintjén biztosított hozzáféréssel rendelkezik. Ha a virtuális gép mappák általi felderítés hatókörét szeretné hatókörbe helyezni, ezt úgy teheti meg, hogy biztosítja, hogy a vCenter-fiók csak olvasható hozzáféréssel rendelkezik a virtuális gép szintjén. [További információ](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Készítse elő az Azure-t és a VMware-t a kiszolgáló felméréséhez. 

1. Ellenőrizze a [VMware támogatási követelményeit és korlátait.](migrate-support-matrix-vmware.md)
2. Állítsa be az Azure-fiók engedélyeit az Azure Migrate szolgáltatással való együttműködéshez.
3. Készítse elő a VMware-t az értékelésre.

Az [oktatóanyag](tutorial-prepare-vmware.md) utasításainak megfelelően konfigurálhatja ezeket a beállításokat.


## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményeknek megfelelően tegye a következőket:

1. Hozzon létre egy Azure Migrate-projekteket.
2. Adja hozzá az Azure Áttelepítési kiszolgáló értékelése eszközt a projektekhez.

[További információ](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és felülvizsgálata

1. VMware virtuális gépek revonatkozó értékelések et hozhat létre.
1. Tekintse át az értékeléseket az áttelepítés tervezésére való felkészülés során.


Az [oktatóanyag](tutorial-assess-vmware.md) utasításainak megfelelően konfigurálhatja ezeket a beállításokat.
    

## <a name="next-steps"></a>További lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * Az Azure Migrate-értékelések méretezése vmware virtuális gépekhez
> * Előkészített Azure és VMware értékelésre
> * Létrehozott egy Azure Migrate projektet, és lefuttatott felméréseket
> * A migrációra való felkészülés során felülvizsgált értékelések.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) számítják ki az értékeléseket, és hogyan módosíthatják az [értékeléseket.](how-to-modify-assessment.md)
