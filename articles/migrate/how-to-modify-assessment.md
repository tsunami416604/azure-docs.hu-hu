---
title: Az Azure Migrate-értékelési beállítások testreszabása |} A Microsoft Docs
description: Ismerteti, hogyan állíthatja be, és át a VMware virtuális gépek az értékelés futtatása az Azure-ban az Azure Migrálási Planner
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2018
ms.author: raynew
ms.openlocfilehash: 2423c4fde177ab50552af580a60c7a15550e5586
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840435"
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

[Az Azure Migrate](migrate-overview.md) értékelések hoz létre az alapértelmezett tulajdonságokat. Miután értékelését hozza létre, módosíthatja az alapértelmezett tulajdonságokat ebben a cikkben szereplő utasítások segítségével.


## <a name="edit-assessment-properties"></a>Értékelés tulajdonságainak szerkesztése

1. A migrálási projekt **Értékelések** lapján válassza ki az értékelést, és kattintson a **Tulajdonságok szerkesztése** elemre.
2. Testre szabhatja az értékelésben, az alábbi adatok alapján:

    **Beállítás** | **Részletek** | **Alapértelmezett**
    --- | --- | ---
    **Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/><br/> Az Azure Migrate jelenleg 30 régiót támogat, beleértve a következőket: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-India, USA középső régiója, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Közép-Németország, Északkelet-Németország, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Korea déli régiója, USA északi középső régiója, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, USA-beli államigazgatás – Arizona, USA-beli államigazgatás – Texas, USA-beli államigazgatás – Virginia, USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és USA 2. nyugati régiója. |  2. nyugati RÉGIÓJA az alapértelmezett helyet.
    **Tárolás típusa** | Ez a tulajdonság segítségével adhatja meg az Azure-ban lefoglalni kívánt lemezeket. Az as-helyszíni méretezést prémium szintű managed disks-vagy standard szintű felügyelt lemezekhez vagy megadhatja a céllemez típusa. A teljesítményalapú méretezés, automatikus, a prémium szintű felügyelt lemezek vagy standard szintű felügyelt lemezekhez vagy megadhatja a céllemez típusa. Automatikus a tárolási típust ad meg, ha a lemez javaslatot a teljesítményadatokat a lemezek (IOPS és átviteli sebesség) alapján történik. Ha például szeretne elérni egy [egypéldányos virtuális gép SLA 99,9 %-os](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), előfordulhat, hogy szeretne megadni a tárolási típust, amely biztosítja, hogy azok a lemezek az értékelés rendszer azt javasolja, prémium szintű felügyelt lemezek prémium szintű felügyelt lemezek. Vegye figyelembe, hogy az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja. | Az alapértelmezett érték: prémium szintű felügyelt lemezek (a méretezési feltétel, *helyszíni méretezési*).
    **Fenntartott példányok** |  Megadhatja azt is, hogy vannak-e [fenntartott példányai](https://azure.microsoft.com/pricing/reserved-vm-instances/) az Azure-ban, és az Azure Migrate az alapján megbecsüli a költségeket. A fenntartott példányok vásárlására nem használhatók szuverén régiók (az Azure Government, Germany és China), és azok alkalmazhatók csak az Azure Migrate használatalapú fizetéses ajánlatra. | Ez a tulajdonság alapértelmezett értéke 3 éves fenntartott példány.
    **Méretezési feltétel** | Az Azure Migrate által használt feltétel a virtuális gépek Azure-nak megfelelő méretezéséhez. Végezhet *teljesítményalapú* méretezést, vagy méretezheti a virtuális gépeket *helyszíniként* is, a teljesítményelőzmények figyelembe vétele nélkül. | Az alapértelmezett beállítás a teljesítményalapú méretezés.
    **Teljesítményelőzmények** | A virtuális gépek teljesítményének értékelésekor figyelembe veendő időtartam. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*. | Az alapértelmezett érték egy nap.
    **Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*.  | Az alapértelmezett érték a 95. percentilis.
    **Virtuálisgép-sorozatok** | Megadhatja, hogy melyik virtuálisgép-sorozatot szeretné figyelembe venni a megfelelő méretezéshez. Például egy olyan éles környezetben, amelyet nem szeretne A-sorozatú virtuális gépekre migrálni az Azure-ban, kizárhatja a listából vagy sorozatból az A-sorozatot, így a megfelelő méretezés csak a kiválasztott sorozatban megy végbe. | Alapértelmezés szerint az összes Virtuálisgép-sorozatok ki van jelölve.
    **Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. | Alapértelmezett beállítás 1.3x.
    **Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. | Az alapértelmezett érték a [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Pénznem** | A számlázás pénzneme. | Az alapértelmezett érték az amerikai dollár.
    **Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények. | Az alapértelmezett beállítás 0%.
    **Virtuális gép üzemideje** | Ha nem tervezi a virtuális gépek 24 x 7 fut az Azure-ban, amely azok kellene futnia adhat meg az időtartamot (havonta napok száma) és napi óraszám, és a költségbecslések elkészítését ennek megfelelően történik. | Az alapértelmezett értéke 31 nap / hó és napi 24 órában.
    **Azure Hybrid Benefit** | Adja meg, hogy rendelkezik-e Frissítési Garanciával, és jogosult-e az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) igénybevételére. Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. | Az alapértelmezett érték az Igen.

3. Kattintson a **mentése** az értékelés frissítése.


## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
