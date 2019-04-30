---
title: Az Azure Migrate-értékelési beállítások testreszabása |} A Microsoft Docs
description: Ismerteti, hogyan állíthatja be, és át a VMware virtuális gépek az értékelés futtatása az Azure-ban az Azure Migrálási Planner
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129823"
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

[Az Azure Migrate](migrate-overview.md) értékelések hoz létre az alapértelmezett tulajdonságokat. Miután értékelését hozza létre, módosíthatja az alapértelmezett tulajdonságokat ebben a cikkben szereplő utasítások segítségével.


## <a name="edit-assessment-properties"></a>Értékelés tulajdonságainak szerkesztése

1. A migrálási projekt **Értékelések** lapján válassza ki az értékelést, és kattintson a **Tulajdonságok szerkesztése** elemre.
2. Testre szabhatja az értékelésben alapján a következő adatokat:

    **Beállítás** | **Részletek** | **Alapértelmezett**
    --- | --- | ---
    **Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/><br/> Az Azure Migrate jelenleg 30 régiót támogat, beleértve a következőket: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-India, USA középső régiója, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Közép-Németország, Északkelet-Németország, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Korea déli régiója, USA északi középső régiója, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, USA-beli államigazgatás – Arizona, USA-beli államigazgatás – Texas, USA-beli államigazgatás – Virginia, USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és USA 2. nyugati régiója. |  2. nyugati RÉGIÓJA az alapértelmezett helyet.
    **Tárolás típusa** | Ez a tulajdonság segítségével határozza meg az Azure-ban az áthelyezni kívánt lemezek típusát. Az as-helyszíni méretezést prémium szintű managed disks-vagy standard szintű felügyelt lemezekhez vagy megadhatja a céllemez típusa. A teljesítményalapú méretezés, automatikus, a prémium szintű felügyelt lemezek vagy standard szintű felügyelt lemezekhez vagy megadhatja a céllemez típusa. Automatikus a tárolási típust ad meg, ha a lemez javaslatot a teljesítményadatokat a lemezek (IOPS és átviteli sebesség) alapján történik. Például, ha használatával kíván elérni egy [egypéldányos virtuális gép SLA 99,9 %-os](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), érdemes lehet prémium szintű managed disks sparkként határozza meg a tárolás típusát. Ez biztosítja, hogy azok a lemezek az értékelés, prémium szintű felügyelt lemezek használata ajánlott. Vegye figyelembe, hogy az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja. | Az alapértelmezett érték: prémium szintű managed disks (a méretezési feltétel, *helyszíni méretezési*).
    **Fenntartott példányok** |  Megadhatja azt is, hogy vannak-e [fenntartott példányai](https://azure.microsoft.com/pricing/reserved-vm-instances/) az Azure-ban, és az Azure Migrate az alapján megbecsüli a költségeket. A fenntartott példányok jelenleg csak a használatalapú fizetéses ajánlatra az Azure Migrate támogatott. | Ez a tulajdonság alapértelmezett értéke 3 éves fenntartott példány.
    **Méretezési feltétel** | Az Azure Migrate által használt feltétel a virtuális gépek Azure-nak megfelelő méretezéséhez. Végezhet *teljesítményalapú* méretezést, vagy méretezheti a virtuális gépeket *helyszíniként* is, a teljesítményelőzmények figyelembe vétele nélkül. | Az alapértelmezett beállítás a teljesítményalapú méretezés.
    **Teljesítményelőzmények** | A virtuális gépek teljesítményének értékelésekor figyelembe veendő időtartam. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*. | Az alapértelmezett érték egy nap.
    **Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*.  | Az alapértelmezett érték a 95. percentilis.
    **Virtuálisgép-sorozatok** | Megadhatja, hogy melyik virtuálisgép-sorozatot szeretné figyelembe venni a megfelelő méretezéshez. Például ha egy éles környezetben, amelyet nem szeretne áttelepíteni A-sorozatú virtuális gépekhez az Azure-ban, A-sorozatú kizárhatja a listából, vagy sorozat és a megfelelő méretezéséhez történik, csak a kijelölt sorozat a. | Alapértelmezés szerint az összes Virtuálisgép-sorozatok ki van jelölve.
    **Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. | Alapértelmezett beállítás 1.3x.
    **Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. | Az alapértelmezett érték a [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Pénznem** | A számlázás pénzneme. | Az alapértelmezett érték az amerikai dollár.
    **Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények. | Az alapértelmezett beállítás 0%.
    **Virtuális gép üzemideje** | Ha nem tervezi a virtuális gépek 24 x 7 fut az Azure-ban, amely azok kellene futnia adhat meg az időtartamot (havonta napok száma) és napi óraszám, és a költségbecslések elkészítését ennek megfelelően kell végrehajtania. | Az alapértelmezett értéke 31 nap / hó és napi 24 órában.
    **Azure Hybrid Benefit** | Adja meg, hogy rendelkezik-e Frissítési Garanciával, és jogosult-e az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) igénybevételére. Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. | Az alapértelmezett érték az Igen.

3. Kattintson a **mentése** az értékelés frissítése.

## <a name="faqs-on-assessment-properties"></a>Az értékelési – gyakori kérdések

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Mi a különbség a között, helyszíni méretezése és a teljesítményalapú méretezéshez?

A méretezési feltétel teljesítményalapú szerint – a helyszínen a megadása esetén méretezése, Azure Migrate nem veszi figyelembe a teljesítményadatokat a virtuális gépek és a helyszíni konfiguráció alapján a virtuális gépek mérete. Ha a méretezési feltétel teljesítményalapú, a méretezés a kihasználtsági adatok alapján történik. Tegyük fel például, hogy egy helyszíni virtuális gép 4 maggal és 8 GB memóriával rendelkezik, a processzorkihasználtsága 50%-os, a memóriakihasználtsága pedig szintén 50%-os. Ha a méretezési feltétel helyszíni méretezés, a rendszer 4 maggal és 8 GB memóriával rendelkező Azure-beli virtuálisgép-termékváltozatot ajánl, ha azonban a méretezési feltétel teljesítményalapú, a rendszer 2 maggal és 4 GB memóriával rendelkező virtuálisgép-termékváltozatot ajánlana, mert a kihasználtság százalékos arányát veszi figyelembe a méret ajánlásakor.

Ehhez hasonlóan a lemezek, a lemezt méretezési attól függ, két értékelés tulajdonságai – a méretezési feltétel és a tárolás típusát. Ha a méretezési feltétel teljesítményalapú, a tárolótípus pedig automatikus, a rendszer a lemez IOPS- és átvitelisebesség-értékeit veszi figyelembe a cél lemeztípus (Standard vagy Prémium) azonosításához. Ha a méretezési feltétel teljesítményalapú, a tárolótípus pedig prémium, a rendszer prémium lemezt ajánl. A prémium lemez termékváltozat kiválasztása az Azure-ban a helyszíni lemez mérete alapján történik. A rendszer ugyanezt a logikát használja a lemezméretezéshez, ha a méretezési feltétel helyszíni méretezés, a tárolótípus pedig standard vagy prémium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Milyen hatással van a teljesítmény előzmények és PERCENTILIS kihasználtságát a javaslatok a méretekkel kapcsolatban?

Ez a tulajdonságok csak a teljesítményalapú méretezés esetén számítanak. Az Azure Migrate gyűjti a helyszíni gépek teljesítményelőzményeit, és ezeket alapján tesz javaslatot az Azure-beli virtuális gép méretére és lemeztípusára.

- A gyűjtőberendezés folyamatosan készít profilokat a helyszíni környezetről a kihasználtsággal kapcsolatos valós idejű adatok 20 másodpercenként történő gyűjtéséhez.
- A berendezés összesíti a 20 másodpercenként vett mintákat, és egyetlen adatpontot hoz létre minden 15 percről. A berendezés úgy hoz létre egyetlen adatpontot, hogy kiválasztja minden 20 másodperces minta csúcsértékét, amelyet elküld az Azure-nak.
- Amikor értékelést hoz létre az Azure-ban a teljesítmény-időtartam és a teljesítményelőzmények percentilisértéke alapján, az Azure Migrate a tényleges kihasználtsági értéket számítja ki, és azt használja a méretezéshez.

Ha például a teljesítmény-időtartam értékét 1 napban határozza meg, a percentilisértéket pedig a 95. percentilisben, az Azure Migrate a gyűjtő által az elmúlt egy napra vonatkozóan küldött 15 perces időtartamokhoz kapcsolódó mintapontokat használja. Emelkedő sorrendben rendezi őket, és a 95. percentilisnek megfelelő értékét választja ki a tényleges kihasználtságként. A 95. percentilisnek megfelelő érték biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek a 99. percentilis választása esetén részét képezhetik a számításnak. Ha az időtartam használati csúcsát szeretné kiválasztani, és nem szeretne lemaradni egyetlen kiugró adatról sem, a 99. percentilist válassza.

## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
