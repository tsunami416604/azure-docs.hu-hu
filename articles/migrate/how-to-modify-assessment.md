---
title: Azure áttelepítése assessment beállítások testreszabása |} Microsoft Docs
description: Ismerteti, hogyan lehet beállítása és futtatása egy értékelési áttelepítése VMware virtuális gépek esetén az Azure-bA az Azure áttelepítési Planner segítségével
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: c826453dcbcaf2facfd58daa05b77decda7ae456
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

[Az Azure áttelepítése](migrate-overview.md) hoz létre a vizsgálatok során az alapértelmezett tulajdonságokat. Miután létrehozta a értékelését, módosíthatja az alapértelmezett tulajdonságokat az utasításokat követve ebben a cikkben.


## <a name="edit-assessment-properties"></a>Értékelés tulajdonságainak szerkesztése

1. A migrálási projekt **Értékelések** lapján válassza ki az értékelést, és kattintson a **Tulajdonságok szerkesztése** elemre.
2. Módosítsa a tulajdonságokat az alábbi táblázat szerint:

    **Beállítás** | **Részletek** | **Alapértelmezett**
    --- | --- | ---
    **Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/><br/> Azure áttelepítése jelenleg 30, beleértve a Kelet-Ausztrália, Ausztrália délkeleti, Dél-Brazília, Kanada központi, Kanada keleti, közép-Indiában, USA középső RÉGIÓJA, Kína keleti, Kína északi, Kelet-Ázsia, USA keleti régiója, Németország központi, Németország szerepel, USA keleti régiója 2., japán régiók Keleti, Nyugat-japán, koreai központi koreai Dél, északi USA középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsiában, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, USA – (kormányzati) Arizona, USA – (kormányzati) Texas, USA – (kormányzati) Virginia, központi USA nyugati régiója, Nyugat-Európa, Nyugat-Indiában, USA nyugati régiója és nyugati US2. |  USA 2. nyugati alapértelmezett helye.
    **Tárhely-redundancia** | A tárhely-redundancia azon típusa, amelyet az Azure-beli virtuális gépek a migrálás után használni fognak. | Az alapértelmezett érték a [Helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md). Azure áttelepítése csak támogatja felügyelt lemezek alapú értékelések és felügyelt lemezek csak az LRS támogatják, ezért a tulajdonságot jelenleg csak LRS lehetősége van.
    **Méretezési feltétel** | Az Azure Migrate által használt feltétel a virtuális gépek Azure-nak megfelelő méretezéséhez. Végezhet *teljesítményalapú* méretezést, vagy méretezheti a virtuális gépeket *helyszíniként* is, a teljesítményelőzmények figyelembe vétele nélkül. | Az alapértelmezett beállítás a teljesítményalapú méretezés.
    **Teljesítményelőzmények** | A virtuális gépek teljesítményének értékelésekor figyelembe veendő időtartam. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*. | Az alapértelmezett érték egy nap.
    **Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*.  | Az alapértelmezett érték a 95. percentilis.
    **Virtuálisgép-sorozat** | Megadhatja, hogy a Virtuálisgép-sorozat, amelyeket szeretne, fontolja meg a megfelelő méretének kiválasztását. Például ha egy éles környezetben, amelyet nem szeretne áttelepíteni A-sorozatú virtuális gépek Azure-ban, A-sorozatú kizárhatja a listából, vagy adatsorozat és a megfelelő méretének kiválasztását csak a kijelölt sorozatban történik. | Alapértelmezés szerint az összes Virtuálisgép-sorozat ki lesz választva.
    **Tarifacsomag** | Megadhatja a cél Azure-beli virtuális gépek [tarifacsomagját (alapszintű/standard)](../virtual-machines/windows/sizes-general.md). Például ha azt tervezi, hogy éles környezetet migrál, érdemes a Standard csomagot választani, amely kis késleltetésű virtuális gépeket biztosít, de többe kerülhet. Másrészről ha egy fejlesztői/tesztkörnyezetet használ, érdemes lehet az Alapszintű csomag mellett dönteni, amely nagyobb késleltetésű virtuális gépeket biztosít, alacsonyabb költségek mellett. | Alapértelmezés szerint a rendszer a [Standard](../virtual-machines/windows/sizes-general.md) csomagot használja.
    **Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. | Alapértelmezett beállítás 1.3x.
    **Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. | Az alapértelmezett érték a [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Pénznem** | A számlázás pénzneme. | Az alapértelmezett érték az amerikai dollár.
    **Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények. | Az alapértelmezett beállítás 0%.
    **Azure Hybrid Benefit** | Adja meg, hogy rendelkezik-e Frissítési Garanciával, és jogosult-e az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) igénybevételére. Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. | Az alapértelmezett érték az Igen.

3. Kattintson a **mentése** az értékelés frissítése.


## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
