---
title: Nagy számú VMware virtuális gép értékelése az Azure-ba való Migrálás Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan lehet kiértékelni az Azure-ba való áttelepítéshez szükséges nagy számú VMware virtuális gépet a Azure Migrate szolgáltatás használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 0d279781cdc27dbf2140c0100d84de5128c6a3d5
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279430"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Nagy számú VMware virtuális gép értékelése az Azure-ba való áttelepítéshez


Ez a cikk azt ismerteti, hogyan értékelhető ki a helyszíni VMware virtuális gépek nagy száma (1000-35000) az Azure-ba való áttelepítéshez az Azure Migrate Server Assessment Tool használatával

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Tervezze meg az értékelést a skálán.
> * Azure-engedélyek konfigurálása és a VMware előkészítése az értékeléshez.
> * Hozzon létre egy Azure Migrate projektet, és hozzon létre egy értékelést.
> * Tekintse át az értékelést az áttelepítés megtervezése során.


> [!NOTE]
> Ha szeretné kipróbálni egy próba-koncepciót néhány virtuális gép értékeléséhez, mielőtt a méretezést kivizsgáljuk, kövesse az [oktatóanyag-sorozatot](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Az értékelés megtervezése

A nagyszámú VMware virtuális gép értékelésének tervezésekor néhány dolgot érdemes meggondolni:

- **Azure Migrate projektek tervezése**: Megtudhatja, hogyan helyezhet üzembe Azure Migrate projekteket. Ha például az adatközpontok különböző földrajzi területeken vannak, vagy ha a felderítést, az értékelést vagy az áttelepítéssel kapcsolatos metaadatokat más földrajzi helyeken kell tárolnia, akkor lehet, hogy több projektre van szüksége. 
- **Berendezések megtervezése**: A Azure Migrate a virtuális gépek folyamatos felderítése érdekében a VMware virtuális gépként üzembe helyezett helyszíni Azure Migrate berendezést használ. A készülék figyeli a környezet változásait, például virtuális gépek, lemezek vagy hálózati adapterek hozzáadását. Emellett metaadatokat és teljesítményadatokat is küld az Azure-nak. Meg kell állapítani, hogy hány készüléket kell telepítenie.
- **A felderítési fiókok tervezése**: Az Azure Migrate készülék egy olyan fiókot használ, amely a vCenter Serverhoz fér hozzá, hogy felderítse a virtuális gépeket az értékeléshez és áttelepítéshez. Ha több mint 10 000 virtuális gépet keres, állítson be több fiókot.


## <a name="planning-limits"></a>Tervezési korlátok
 
Használja az ebben a táblázatban foglalt korlátokat a tervezéshez.

**Tervezés** | **Korlátok**
--- | --- 
**Azure Migrate projektek** | Egy projektben akár 35 000 virtuális gépet is megvizsgálhat.
**Azure Migrate berendezés** | Egy készülék legfeljebb 10 000 virtuális gépet képes felderíteni egy vCenter Server.<br/> Egy készülék csak egyetlen vCenter Server tud csatlakozni.<br/> Egy berendezés csak egyetlen Azure Migrate projekthez társítható.<br/>  Tetszőleges számú készülék társítható egyetlen Azure Migrate-projekthez is. <br/><br/> 
**Csoport** | Egyetlen csoportban legfeljebb 35 000 virtuális gépet adhat hozzá.
**Azure Migrate Értékelés** | Egyetlen értékeléssel akár 35 000 virtuális gépet is megvizsgálhat.

Ezekkel a korlátozásokkal kapcsolatban néhány példa az üzemelő példányokra:


**vCenter server** | **Kiszolgálókon futó virtuális gépek** | **Az ajánlás** | **Művelet**
---|---|---
Egy | < 10 000 | Egy Azure Migrate projekt.<br/> Egy készülék.<br/> Egy vCenter-fiók a felderítéshez. | Állítsa be a készüléket, és kapcsolódjon vCenter Server fiókkal.
Egy | > 10 000 | Egy Azure Migrate projekt.<br/> Több készülék.<br/> Több vCenter-fiók. | Állítsa be a készüléket minden 10 000 virtuális gépre.<br/><br/> Állítsa be a vCenter-fiókokat, és ossza fel a leltárt, hogy korlátozza a fiókok hozzáférését a 10 000-nél kevesebb virtuális gépre.<br/> Csatlakoztasson minden készüléket a vCenter-kiszolgálóhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített gépek között.
Több | < 10 000 |  Egy Azure Migrate projekt.<br/> Több készülék.<br/> Egy vCenter-fiók a felderítéshez. | Állítsa be a készülékeket, és kapcsolódjon a vCenter Serverhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített gépek között.
Több | > 10 000 | Egy Azure Migrate projekt.<br/> Több készülék.<br/> Több vCenter-fiók. | Ha vCenter Server felderítést < 10 000 virtuális gépeket, állítson be egy berendezést az egyes vCenter Serverokhoz.<br/><br/> Ha vCenter Server felderítést > 10 000 virtuális gépeket, állítson be egy berendezést minden 10 000 virtuális gép számára.<br/> Állítsa be a vCenter-fiókokat, és ossza fel a leltárt, hogy korlátozza a fiókok hozzáférését a 10 000-nél kevesebb virtuális gépre.<br/> Csatlakoztasson minden készüléket a vCenter-kiszolgálóhoz egy fiókkal.<br/> Elemezheti a függőségeket a különböző készülékekkel felderített gépek között.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>A felderítés tervezése több-bérlős környezetben

Ha több-bérlős környezetet szeretne tervezni, a felderítést a vCenter Serverra szűkítheti.

- A berendezés felderítési hatókörét beállíthatja egy vCenter Server adatközpontra, fürtökre vagy a fürtök, a gazdagépek vagy az egyes virtuális gépek mappájára vagy mappára.
- Ha a környezet a bérlők között van megosztva, és az egyes bérlőket külön szeretné felderíteni, akkor a hatókörhöz való hozzáférést a berendezés által a felderítéshez használt vCenter-fiókhoz is elérheti. 
    - Előfordulhat, hogy a virtuális gépek mappáinak hatókörét szeretné használni, ha a bérlők megosztják a gazdagépeket. Azure Migrate nem tudja felderíteni a virtuális gépeket, ha a vCenter-fiókhoz hozzáférés van megadva a vCenter VM-mappa szintjén. Ha a virtuális gép mappáiban szeretné kiterjeszteni a felderítést, a vCenter-fióknak csak olvasási hozzáférése van hozzárendelve a virtuális gép szintjén. További információk a hatókör-felderítésről [itt](tutorial-assess-vmware.md#scoping-discovery).

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

Az Azure és a VMware előkészítése a kiszolgálók értékeléséhez. 

1. Ellenőrizze [a VMware támogatási követelményeit és korlátozásait](migrate-support-matrix-vmware.md).
2. Az Azure-fiók engedélyeinek beállítása a Azure Migrate való interakcióhoz.
3. A VMware előkészítése az értékeléshez.

A beállítások konfigurálásához kövesse az [oktatóanyag](tutorial-prepare-vmware.md) utasításait.


## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményekkel összhangban tegye a következőket:

1. Hozzon létre egy Azure Migrate projekteket.
2. Adja hozzá a Azure Migrate Server Assessment eszközt a projektekhez.

[További információ](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Értékelés létrehozása és áttekintése

1. Hozzon létre értékeléseket a VMware virtuális gépekhez.
1. Tekintse át az áttelepítési tervezés előkészítésének értékelését.


A beállítások konfigurálásához kövesse az [oktatóanyag](tutorial-assess-vmware.md) utasításait.
    

## <a name="next-steps"></a>További lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * A VMware virtuális gépek Azure Migrate értékelésének tervezett méretezése
> * Az Azure és a VMware előkészítése az értékeléshez
> * Létrehozott egy Azure Migrate projektet és futtatott értékeléseket
> * Az áttelepítésre való felkészülés során felülvizsgálták az értékeléseket.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) számítja ki az értékeléseket, és hogyan [módosíthatja az értékeléseket](how-to-modify-assessment.md).
