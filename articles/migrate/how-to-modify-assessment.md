---
title: Azure áttelepítése assessment beállítások testreszabása |} Microsoft Docs
description: Ismerteti, hogyan lehet beállítása és futtatása egy értékelési áttelepítése VMware virtuális gépek esetén az Azure-bA az Azure áttelepítési Planner segítségével
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 9ddd6c32388b2e05fd97138414958b67c009f9ee
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284913"
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

[Az Azure áttelepítése](migrate-overview.md) hoz létre a vizsgálatok során az alapértelmezett tulajdonságokat. Miután létrehozta a értékelését, módosíthatja az alapértelmezett tulajdonságokat az utasításokat követve ebben a cikkben.


## <a name="edit-assessment-properties"></a>Értékelés tulajdonságainak szerkesztése

1. A migrálási projekt **Értékelések** lapján válassza ki az értékelést, és kattintson a **Tulajdonságok szerkesztése** elemre.
2. Módosítsa a tulajdonságokat az alábbi táblázat szerint:

    **Beállítás** | **Részletek** | **Alapértelmezett**
    --- | --- | ---
    **Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/><br/> Az Azure Migrate jelenleg 30 régiót támogat, beleértve a következőket: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-India, USA középső régiója, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Közép-Németország, Északkelet-Németország, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Korea déli régiója, USA északi középső régiója, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, USA-beli államigazgatás – Arizona, USA-beli államigazgatás – Texas, USA-beli államigazgatás – Virginia, USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és USA 2. nyugati régiója. |  USA 2. nyugati alapértelmezett helye.
    **Tarifacsomag** | Megadhatja a cél Azure-beli virtuális gépek [tarifacsomagját (alapszintű/standard)](../virtual-machines/windows/sizes-general.md). Például ha azt tervezi, hogy éles környezetet migrál, érdemes a Standard csomagot választani, amely kis késleltetésű virtuális gépeket biztosít, de többe kerülhet. Másrészről ha egy fejlesztői/tesztkörnyezetet használ, érdemes lehet az Alapszintű csomag mellett dönteni, amely nagyobb késleltetésű virtuális gépeket biztosít, alacsonyabb költségek mellett. | Alapértelmezés szerint a rendszer a [Standard](../virtual-machines/windows/sizes-general.md) csomagot használja.
    **Tárolás típusa** | Megadhatja, hogy milyen típusú lemezek szeretne lefoglalni az Azure-ban. Ez a tulajdonság akkor alkalmazható, ha a méretezési kritériumot esetén, mert a helyszíni méretezése. Megadhatja a céltípus lemez vagy felügyelt Premium lemezek vagy Standard által kezelt lemezeken. Az teljesítmény-alapú méretezés, a lemez javaslat automatikusan végezhető el a virtuális gépek teljesítményadatok alapján. Ügyeljen arra, hogy Azure áttelepítése csak támogatja a felügyelt lemezek a áttelepítésének ellenőrzéséhez. | Az alapértelmezett érték: felügyelt Premium lemezek (a méretezési kritériumot, *regisztrációja, mivel a helyszíni méretezési*).
    **Fenntartott példányok** |  Megadhatja azt is, ha akkor [példányok fenntartott](https://azure.microsoft.com/pricing/reserved-vm-instances/) az Azure és Azure áttelepítése megbecsüli, a költség ennek megfelelően. Fenntartott példányok szuverén régiók (Azure Government, német és Kína) nem alkalmazhatók, és azok alkalmazhatók csak az Azure át használatalapú fizetésre ajánlat. | Ez a tulajdonság alapértelmezett értéke 3 évig fenntartott példányok.
    **Méretezési feltétel** | Az Azure Migrate által használt feltétel a virtuális gépek Azure-nak megfelelő méretezéséhez. Végezhet *teljesítményalapú* méretezést, vagy méretezheti a virtuális gépeket *helyszíniként* is, a teljesítményelőzmények figyelembe vétele nélkül. | Az alapértelmezett beállítás a teljesítményalapú méretezés.
    **Teljesítményelőzmények** | A virtuális gépek teljesítményének értékelésekor figyelembe veendő időtartam. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*. | Az alapértelmezett érték egy nap.
    **Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*.  | Az alapértelmezett érték a 95. percentilis.
    **Virtuálisgép-sorozatok** | Megadhatja, hogy melyik virtuálisgép-sorozatot szeretné figyelembe venni a megfelelő méretezéshez. Például egy olyan éles környezetben, amelyet nem szeretne A-sorozatú virtuális gépekre migrálni az Azure-ban, kizárhatja a listából vagy sorozatból az A-sorozatot, így a megfelelő méretezés csak a kiválasztott sorozatban megy végbe. | Alapértelmezés szerint az összes Virtuálisgép-sorozat ki lesz választva.
    **Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. | Alapértelmezett beállítás 1.3x.
    **Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. | Az alapértelmezett érték a [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Pénznem** | A számlázás pénzneme. | Az alapértelmezett érték az amerikai dollár.
    **Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények. | Az alapértelmezett beállítás 0%.
    **Virtuális gép hasznos üzemidő** | Ha a virtuális gépek nem fognak az Azure-ban 24 x 7 futnia kell, megadhat a időtartama (hónap / nap) és száma órát amely azok akkor fut, és a költségek becsléseket ennek megfelelően történik. | Az alapértelmezett értéke 31 nap havi és napi 24 órában.
    **Azure Hybrid Benefit** | Adja meg, hogy rendelkezik-e Frissítési Garanciával, és jogosult-e az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) igénybevételére. Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. | Az alapértelmezett érték az Igen.

3. Kattintson a **mentése** az értékelés frissítése.


## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
