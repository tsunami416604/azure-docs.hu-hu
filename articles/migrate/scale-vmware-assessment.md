---
title: Mérje fel az Azure-bA az Azure Migrate az áttelepítéshez a VMware virtuális gépek nagy számú |} A Microsoft Docs
description: Ismerteti, hogyan lehet felmérheti a VMware virtuális gépek nagy számú Azure-bA az Azure Migrate szolgáltatás használatával az áttelepítéshez.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811335"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Felmérheti a VMware virtuális gépek nagy számú Azure-bA az áttelepítéshez


Ez a cikk bemutatja, hogyan annak ellenőrzéséhez, nagy számú (1000 – 35 000) a helyszíni VMware virtuális gépek áttelepítése az Azure-ba, az Azure Migrate Server Assessment eszközzel

[Az Azure Migrate](migrate-services-overview.md) hub eszközöket tartalmazza, amelyek segítséget nyújtanak a felderítése, értékelheti és migrálhatja az alkalmazásokat, az infrastruktúra és a számítási feladatok a Microsoft Azure biztosít. A hub tartalmaz, az Azure Migrate eszközökkel és külső független szoftverszállítók (ISV) szállító ajánlatok. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Tervezze meg a felmérés ipari méretekben.
> * Az Azure-engedélyek konfigurálása, és a VMware előkészítése az értékeléshez.
> * Az Azure Migrate-projekt létrehozása, és hozzon létre egy értékelést.
> * Tekintse át az értékelés, migrálás megtervezésekor.


> [!NOTE]
> Ha ki szeretné próbálni a proof-of-concept mérje fel a virtuális gépek néhány ipari méretekben értékelése előtt hajtsa végre a [oktatóanyag-sorozat](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Értékelés tervezése

VMware virtuális gépek nagy számú értékelése tervezésekor van néhány dolgot kell foglalkoznia:

- **Az Azure Migrate a projektek tervezésében**: Döntse el, hogyan helyezhet üzembe az Azure Migrate-projekt. Például ha az adatközpontok különböző földrajzi területeken, vagy egy másik földrajzi felderítési, értékelés vagy migrálási kapcsolatos metaadatokat tárolni kívánt, szüksége lehet a több projektet is. 
- **Készülékek megtervezése**: Az Azure Migrate a helyszíni Azure Migrate telepíthetőek, egy VMware virtuális gépként üzembe helyezett folyamatosan a virtuális gépek felderítéséhez használ. A berendezés figyeli a környezet változásakor, például virtuális gépek, a lemezek vagy a hálózati adapterek hozzáadása. Az Azure-bA velük kapcsolatos metaadatok és a teljesítmény adatokat is küld. Döntse el, hogy hány készülék kell telepítenie kell.
- **A felderítéshez fiókok megtervezése**: Az Azure Migrate készülék-fiókot használ a vCenter-kiszolgálóhoz való hozzáférést annak érdekében, hogy a virtuális gépek felderítése elemzésekhez és migráláshoz. Ha több mint 10 000 virtuális gépek feltárására több fiókok beállítása.


## <a name="planning-limits"></a>Korlátok tervezése
 
Használja a korlátok a tervezési táblázat foglalja össze.

**Tervezés** | **Korlátok**
--- | --- 
**Az Azure Migrate-projekt** | Mérje fel, akár 35,000 virtuális gépeket a projektben.
**Az Azure Migrate-készülék** | Egy készülék csak egyetlen vCenter-kiszolgáló lehet csatlakozni.<br/><br/> Egy készülék csak társítható egy egyetlen Azure Migrate-projekt.<br/> Egy készülék felfedezheti a vCenter Server-alapú virtuális gépek legfeljebb 10 000.
**Az Azure Migrate-értékelés** | Egyetlen értékeléssel akár 35,000 virtuális gépet értékelhet.

Ezek a korlátok szem előtt, az alábbiakban néhány példa a központi telepítésekre:


**vCenter server** | **Kiszolgáló-alapú virtuális gépek** | **Az ajánlás** | **Művelet**
---|---|---
Az egyik | < 10,000 | Egy Azure Migrate-projekt.<br/> Egy készülék.<br/> Egy vCenter-fióknak a felderítéshez | Állítsa be a készülék, csatlakozik a vCenter-kiszolgáló egy olyan fiókkal.
Az egyik | > 10,000 | Egy Azure Migrate-projekt.<br/> Több készülékek.<br/> Több vCenter-fiókot. | Minden 10 000 virtuális berendezés beállítását.<br/><br/> Állítsa be a vCenter-fiókokat, és a készlet korlátozása kisebb, mint 10 000 virtuális fiók hozzáférési felosztani.<br/> Minden egyes készülék csatlakozni a vCenter-kiszolgáló egy olyan fiókkal.<br/> Elemezheti a függőségek között a különböző készülékeket felderített gépek.
Többszörös | < 10,000 |  Egy Azure Migrate-projekt.<br/> Több készülékek.<br/> Egy vCenter-fióknak a felderítéshez | Állítsa be a berendezések, csatlakozik a vCenter-kiszolgáló egy olyan fiókkal.<br/> Elemezheti a függőségek között a különböző készülékeket felderített gépek.
Többszörös | > 10,000 | Egy Azure Migrate-projekt.<br/> Több készülékek.<br/> Több vCenter-fiókot. | Ha a vCenter-kiszolgáló felderítése < 10 000 virtuális gépeket, állítsa be a készülék mindegyik vcenter-kiszolgálóhoz.<br/><br/> Ha a vCenter-kiszolgáló felderítése > 10 000-beli virtuális gépek beállítása telepíthetőek minden 10 000 virtuális gépekhez.<br/> Állítsa be a vCenter-fiókokat, és a készlet korlátozása kisebb, mint 10 000 virtuális fiók hozzáférési felosztani.<br/> Minden egyes készülék csatlakozni a vCenter-kiszolgáló egy olyan fiókkal.<br/> Elemezheti a függőségek között a különböző készülékeket felderített gépek.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>A több-bérlős környezetben felderítés tervezése

Ha egy több-bérlős környezet, gazdagépcsoportjaira a vCenter-kiszolgáló felderítése.

- A berendezés felderítési hatókör és a egy vCenter Server datacenter, a fürt vagy a mappa fürtöket, -gazdagép vagy gazdagépek, illetve az egyes virtuális gépekhez mappa megadható.
- Ha a környezet megosztott bérlőn, és nem kívánja minden bérlő külön-külön felderítése, a vCenter-fiók, amely a készülék használja a felderítéshez való hozzáférést korlátozhatja körét. 
    - Ha a bérlők osztoznak a gazdagép, hozzon létre hitelesítő adatok csak olvasási hozzáféréssel a virtuális gépek, amelyek az adott bérlőhöz tartozik. 
    - Az Azure Migrate készülék felderítéshez használni ezeket a hitelesítő adatokat.
    - Az Azure Migrate-értékelés nem virtuális gépek felderítéséhez, ha a vCenter-fióknak a vCenter VM mappa szintjén a hozzáférést. Gazdagépek és fürtök mappák támogatottak. 

## <a name="prepare-for-assessment"></a>Értékelés előkészítése

Készítse elő az Azure és a VMware server assessment. 

1. Győződjön meg arról [VMware támogatja a követelmények és korlátozások](migrate-support-matrix-vmware.md).
2. Az Azure-fiók használatával kommunikálhat az Azure Migrate engedélyek beállítása.
3. VMware előkészítése az értékeléshez.


Kövesse a [ebben az oktatóanyagban](tutorial-prepare-vmware.md) ezek a beállítások konfigurálásához.


## <a name="create-a-project"></a>Projekt létrehozása

A tervezési követelményeinek megfelelően tegye a következőket:

1. Hozzon létre egy Azure Migrate-projekt.
2. Adja hozzá az Azure Migrate Server Assessment eszköz a projekteket.

[További információ](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Hozzon létre, és tekintse át az értékeléseket

1. Hozzon létre értékelések a VMware virtuális gépekhez.
1. Tekintse át az értékeléseket, az áttelepítés tervezéséhez előkészítésekor.


Kövesse a [ebben az oktatóanyagban](tutorial-assess-vmware.md) ezek a beállítások konfigurálásához.
    

## <a name="next-steps"></a>További lépések

Ebben a cikkben:
 
> [!div class="checklist"] 
> * VMware virtuális gépek Azure Migrate értékelések méretezése tervezett
> * Előkészített Azure és VMware-értékelés
> * Azure Migrate-projekt létrehozása, és futtatta az értékelések
> * Tekintse át a migrálásra való felkészüléskor értékeléseket.

Most [megtudhatja, hogyan](concepts-assessment-calculation.md) értékelés kiszámítása, és hogyan [értékelések módosítása](how-to-modify-assessment.md).
