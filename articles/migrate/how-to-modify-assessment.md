---
title: Azure Migrate Server Assessment értékelésének testreszabása | Microsoft Docs
description: A Azure Migrate Server Assessment használatával létrehozott értékelések testreszabásának ismertetése
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "68234273"
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

Ez a cikk a Azure Migrate Server Assessment által létrehozott értékelések testreszabását ismerteti.

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.

Az Azure-ba való Migrálás előkészítése során a Azure Migrate Server Assessment Tool eszközzel hozhat létre értékeléseket a helyszíni VMware virtuális gépekhez és a Hyper-V virtuális gépekhez.

## <a name="about-assessments"></a>Az értékelések ismertetése

Az értékeléseknek két típusa lehet a Azure Migrate Server Assessment használatával.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)**: a helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: a helyszíni virtuális gép méretétől függően<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott tárolási típus alapján.


## <a name="how-is-an-assessment-done"></a>Hogyan történik az értékelés?

Azure Migrate kiszolgáló értékelésében elvégzett értékelés három szakaszból áll. Az értékelés megfelelőségi elemzéssel kezdődik, majd a méretezés és végül a havi költségbecslés. A gépek csak akkor haladnak át egy későbbi fázisban, ha az előzőre kerül. Ha például egy gép meghibásodik az Azure alkalmassági ellenőrzésének, az az Azure-nak nem megfelelőként van megjelölve, és a méretezés és a költségszámítás nem lesz végrehajtva. [További információ.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/> A kiszolgáló értékelése jelenleg a következő célcsoportokat támogatja: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Közép-India, Közép-USA, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Kelet-RÉGIÓJA, Közép-Németország, Északkelet-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, északi középső régió, észak-Európa, az USA déli középső régiója Egyesült Királyság déli régiója , US Gov Texas, US Gov Virginia, az USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és Nyugat-RÉGIÓJA.
**Tárolás típusa** | Ezzel a tulajdonsággal adhatja meg, hogy milyen típusú lemezeket szeretne áthelyezni az Azure-ban.<br/><br/> A helyszíni méretezéshez a célként megadott tárolási típust prémium szintű felügyelt lemezként, standard SSD felügyelt lemezként vagy standard HDD által felügyelt lemezként is megadhatja. A teljesítmény-alapú méretezéshez megadhatja a céllemez típusát automatikus, prémium szintű felügyelt lemezként, standard HDD felügyelt lemezként vagy standard SSD által felügyelt lemezként.<br/><br/> Ha a tárolási típust automatikusként adja meg, a lemezre vonatkozó javaslat a lemezek teljesítményi adatai (IOPS és átviteli sebesség) alapján történik. Ha a tárterületet prémium/standard szintűként adja meg, akkor az értékelés a kiválasztott tárolási típuson belül egy lemez SKU-t javasol. Ha az Egypéldányos VM 99,9%-os SLA-t kívánja elérni, érdemes lehet a tárolási típust prémium szintű felügyelt lemezként megadni. Ez biztosítja, hogy az értékelés összes lemeze prémium szintű felügyelt lemezként legyen ajánlott. Azure
**Fenntartott példányok (RI)** | Ennek a tulajdonságnak a segítségével megadhatja, hogy az Azure-ban [foglalt példányok](https://azure.microsoft.com/pricing/reserved-vm-instances/) rendelkeznek-e, majd az értékelésben szereplő KÖLTSÉGBECSLÉS az ri-kedvezményekbe kerül. A fenntartott példányok jelenleg csak az utólagos elszámolású ajánlatokhoz használhatók Azure Migrateban.
**Méretezési feltétel** | Az Azure-hoz megfelelő méretű virtuális gépekhez használandó feltétel. A teljesítmény *-alapú* méretezés vagy a virtuális gépek mérete a *helyszínen*is végezhető, a teljesítmény előzményeinek figyelembevétele nélkül.
**Teljesítményelőzmények** | A gépek teljesítményi adatai értékelésének időtartama. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel *teljesítmény-alapú*.
**Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés *teljesítmény-alapú*.
**Virtuálisgép-sorozatok** |     Megadhatja, hogy melyik virtuálisgép-sorozatot szeretné figyelembe venni a megfelelő méretezéshez. Ha például olyan éles környezettel rendelkezik, amelyet nem szeretne áttelepíteni az Azure-beli sorozatú virtuális gépekre, kizárhatja a-sorozatokat a listából vagy adatsorozatból, és a jobb oldali méretezés csak a kiválasztott adatsorozatban végezhető el.
**Kényelmi faktor** | Azure Migrate kiszolgáló értékelése az értékelés során egy puffert (komfort faktor) tekint. Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A számlázás pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények.<br/> Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha a virtuális gépek nem fognak nonstop futni az Azure-ban, megadhatja az időtartamot (a havonta megjelenő napok számát és a napi óraszámot), és ennek megfelelően elvégezheti a költségbecslést.<br/> Az alapértelmezett érték havi 31 nap, és naponta 24 óra.
**Azure Hybrid Benefit** | Megadhatja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. Az alapértelmezett érték az Igen.


## <a name="edit-assessment-properties"></a>Értékelési Tulajdonságok szerkesztése

Értékelés létrehozása után az értékelés tulajdonságainak szerkesztéséhez tegye a következőket:

1. A Azure Migrate projektben kattintson a **kiszolgálók**elemre.
2. **Azure Migrate: kiszolgáló értékelése**területen kattintson az értékelések száma elemre.
3. Az **értékelés**területen kattintson a megfelelő értékelés > **Szerkesztés tulajdonságok**elemre.
5. Szabja testre az értékelési tulajdonságokat a fenti táblázatnak megfelelően.
6. Az értékelés frissítéséhez kattintson a **Save (Mentés** ) gombra.


Értékelés létrehozásakor is szerkesztheti az értékelési tulajdonságokat.


## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
