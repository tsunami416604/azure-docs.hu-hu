---
title: "Azure áttelepítése assessment beállítások testreszabása |} Microsoft Docs"
description: "Ismerteti, hogyan lehet beállítása és futtatása egy értékelési áttelepítése VMware virtuális gépek esetén az Azure-bA az Azure áttelepítési Planner segítségével"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: 8babdbc30e062c7b289e90a674cec3222943e48d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

[Az Azure áttelepítése](migrate-overview.md) hoz létre a vizsgálatok során az alapértelmezett tulajdonságokat. Miután létrehozta a értékelését, módosíthatja az alapértelmezett tulajdonságokat az utasításokat követve ebben a cikkben.


## <a name="edit-assessment-properties"></a>Értékelés tulajdonságainak szerkesztése

1. Az a **értékelések** az áttelepítési projekt, válassza ki a felmérése, majd kattintson a lap **tulajdonságainak szerkesztése**.
2. Az alábbi táblázat szerint tulajdonságainak módosítása:

    **Beállítás** | **Részletek** | **Alapértelmezett**
    --- | --- | ---
    **Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni. |  USA 2. nyugati alapértelmezett helye.
    **Tárhely-redundancia** | Az adattároló redundanciája, amely az Azure virtuális gépek által használt, az áttelepítés után típusa. | [Helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) az alapértelmezett érték. Azure áttelepítése csak támogatja felügyelt lemezek alapú értékelések és felügyelt lemezek csak az LRS támogatják, ezért a tulajdonságot jelenleg csak LRS lehetősége van. 
    **Méretezési kritériumot** | A feltétel az Azure által Azure megfelelő méretének virtuális gépek áttelepítéséhez használható. Mindent vagy tegye *teljesítmény-alapú* méretezése vagy a virtuális gépek méretezés *, a helyszíni*, figyelembe véve a teljesítményelőzményei nélkül. | Teljesítmény-alapú méretezési beállítás az alapértelmezett.
    **Teljesítményelőzmények** | Fontolja meg a virtuális gépek teljesítményének értékeléséhez idejét. A tulajdonság csak akkor alkalmazható, ha méretezési kritériumot *teljesítmény-alapú méretezési*. | Alapértelmezett érték egy nap.
    **PERCENTILIS kihasználtsága** | A PERCENTILIS értékének a teljesítmény minta állítsa be a megfelelő méretének kiválasztását figyelembe kell venni. A tulajdonság csak akkor alkalmazható, ha méretezési kritériumot *teljesítmény-alapú méretezési*.  | Alapértelmezés szerint 95. percentilis visszaadása.
    **Tarifacsomag** | Megadhatja a [tarifacsomag (alapszintű vagy Standard)](../virtual-machines/windows/sizes-general.md) a cél Azure virtuális gépeken. Például ha azt tervezi, éles környezetben áttelepítéséhez, szeretné fontolja meg a Standard csomagot, amely olyan virtuális gépeket biztosít alacsony késéssel, de előfordulhat, hogy további költség. Másrészről Ha egy fejlesztői tesztkörnyezetben, érdemes lehet figyelembe kell venni az alapszintű rétegben, amely rendelkezik a virtuális gépek nagyobb késleltetéssel járhat, és az alacsonyabb költségek. | Alapértelmezés szerint a [szabványos](../virtual-machines/windows/sizes-general.md) réteg szolgál.
    **Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. | Alapértelmezett érték 1.3 x.
    **Ajánlat** | [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/) , hogy csatlakozott. | [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) az alapértelmezett beállítás.
    Pénznem | Számlázás pénzneme. | Alapértelmezés szerint USD.
    **Kedvezményes (%)** | Bármely előfizetés-specifikus kedvezményes kapni az Azure-ajánlat felett. | Az alapértelmezett érték 0 %.
    **Azure Hybrid Benefit** | Adja meg, ha frissítési garancia, és jogosult [Azure hibrid juttatás](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha értéke Igen, a Windows Azure árak minősülnek Windows virtuális gépek esetén. | Az alapértelmezett érték az Igen.

3. Kattintson a **mentése** az értékelés frissítése.


## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
