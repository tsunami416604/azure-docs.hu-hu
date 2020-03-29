---
title: Az Azure Migrate Server Assessment értékelésének testreszabása | Microsoft dokumentumok
description: Az Azure Migrate Server Assessment segítségével létrehozott értékelések testreszabása
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234273"
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

Ez a cikk ismerteti, hogyan szabhatja testre az Azure Migrate Server Assessment által létrehozott értékelések.

[Az Azure Migrate](migrate-services-overview.md) központi központot biztosít a helyszíni alkalmazások és számítási feladatok, valamint a magán-/nyilvános felhőbeli virtuális gépek Azure-ba való felderítésének, értékelésének és áttelepítésének nyomon követéséhez. A központ Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint külső független szoftverszállítói (ISV) ajánlatokat.

Az Azure Áttelepítési kiszolgáló értékelése eszközzel értékeléseket hozhat létre a helyszíni VMware virtuális gépek és a Hyper-V VM-ek számára az Azure-ba való migrálás előkészítéseként.

## <a name="about-assessments"></a>Az értékelésekről

Kétféle értékelések futtatható az Azure Áttelepítési kiszolgáló értékelése használatával.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Összegyűjtött teljesítményadatokon alapuló értékelések | **Ajánlott virtuális gép mérete:** A PROCESSZOR és a memória kihasználtsági adatai alapján.<br/><br/> **Ajánlott lemeztípus (standard vagy prémium díjas felügyelt lemez)**: Az IOPS és a helyszíni lemezek átviteli hatása alapján.
**A helyszíni** | A helyszíni méretezésen alapuló értékelések. | **Ajánlott virtuális gép mérete:** A helyszíni virtuális gép mérete alapján<br/><br> **Ajánlott lemeztípus:** Az értékeléshez kiválasztott tárolási típusbeállítás alapján.


## <a name="how-is-an-assessment-done"></a>Hogyan történik az értékelés?

Az Azure Áttelepítési kiszolgáló értékelése három szakaszból áll. Az értékelés egy alkalmassági elemzéssel kezdődik, amelyet a méretezés követ, és végül egy havi költségbecslés. A gép csak akkor halad tovább egy későbbi szakaszba, ha átmegy az előzőn. Ha például egy gép nem felel meg az Azure alkalmassági ellenőrzésében, az Azure számára alkalmatlanként van megjelölve, és a méretezés és a költségszámítás nem történik meg. [További információ.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/> A Server Assessment jelenleg a következő célrégiókat támogatja: Ausztrália Keleti, Ausztrália délkeleti, Brazília déli, Kanada középső, Kanada Keleti, Közép-India, Usa középső része, Kelet-Kína, Kína Északkeleti, Kína Észak-Ázsia, USA keleti régiója, USA keleti régiója, Németország közép-, németországi északkeleti része, Kelet-Japán, Nyugat-Japán, Korea Közép-Korea, Dél-Korea, Usa északi középső része, Észak-Európa, Dél-Közép-USA, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli része, az Egyesült Királyság nyugati része, az EGYESÜLT Államok kormánya, az USA állam kormánya, az USA nyugati régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és AZ USA nyugati régiója2.
**Tárolás típusa** | Ezzel a tulajdonsággal megadhatja, hogy milyen típusú lemezekre szeretne áthelyezni az Azure-ban.<br/><br/> A helyszíni méretezéshez megadhatja a céltároló típusát prémium szintű felügyelt lemezek, standard SSD-felügyelt lemezek vagy standard HDD-felügyelt lemezek. A teljesítményalapú méretezéshez megadhatja a céllemez típusát automatikus, prémium szintű felügyelt lemezek, szabványos HDD-kezelt lemezek vagy standard SSD-felügyelt lemezek.<br/><br/> Ha a tároló típusát automatikusként adja meg, a lemezajánlás a lemezek (IOPS és átviteli teljesítmény) teljesítményadatai alapján történik. Ha prémium szintűként adja meg a tárolási típust, az értékelés egy lemeztermék-változatot javasol a kiválasztott tárolási típuson belül. Ha azt szeretné elérni, hogy egy példány vm SLA 99,9%, érdemes megadni a tárolási típus prémium szintű felügyelt lemezek. Ez biztosítja, hogy az értékelésben szereplő összes lemez prémium szintű felügyelt lemezként ajánlott. Azure
**Fenntartott példányok (RI)** | Ez a tulajdonság segít meghatározni, ha az Azure-ban [fenntartott példányok,](https://azure.microsoft.com/pricing/reserved-vm-instances/) költségbecslések az értékelés ben, majd kész figyelembe vevő ri-kedvezmények. Fenntartott példányok jelenleg csak az Azure Migrate szolgáltatás on-you-go ajánlat támogatott.
**Méretezési feltétel** | Az Azure-hoz megfelelő méretű virtuális gépekhez használandó feltétel. *Teljesítményalapú* méretezést vagy a virtuális gépek helyszíni méreteként is elláthatja, a teljesítményelőzmények figyelembe venélkül. *as on-premises*
**Teljesítményelőzmények** | A gépek teljesítményadatainak kiértékelésére figyelembe vejthető időtartam. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel *teljesítményalapú.*
**Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés *teljesítményalapú.*
**Virtuálisgép-sorozatok** |     Megadhatja, hogy melyik virtuálisgép-sorozatot szeretné figyelembe venni a megfelelő méretezéshez. Ha például olyan éles környezettel rendelkezik, amelyet nem tervez áttelepíteni az A-sorozatú virtuális gépekre az Azure-ban, kizárhatja az A-sorozatot a listából vagy sorozatból, és a jobb méretezés csak a kiválasztott sorozatban történik.
**Kényelmi faktor** | Az Azure Áttelepítési kiszolgáló értékelése puffert (komforttényezőt) vesz figyelembe az értékelés során. Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A számlázás pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények.<br/> Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha a virtuális gépek nem fognak futni 24órás az Azure-ban, megadhatja az időtartamot (a napok száma havonta és órák száma naponta), amelyre futnának, és a költségbecslések ennek megfelelően történik.<br/> Az alapértelmezett érték havi 31 nap és napi 24 óra.
**Azure Hybrid Benefit** | Adja meg, hogy rendelkezik-e szoftvergaranciával, és jogosult-e az [Azure Hybrid Benefit használatára.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. Az alapértelmezett érték az Igen.


## <a name="edit-assessment-properties"></a>Értékelési tulajdonságok szerkesztése

Ha az értékelés tulajdonságait az értékelés létrehozása után szeretné szerkeszteni, tegye a következőket:

1. Az Azure Áttelepítés projektben kattintson a **Kiszolgálók gombra.**
2. Az **Azure Migrate: Server Assessment (Az Azure Migrate: Server Assessment)** alkalmazásban kattintson az értékelések számának.
3. Az **Értékelés párbeszédpanelen**kattintson a tulajdonságok szerkesztése > vonatkozó **értékelésre**.
5. Az értékelési tulajdonságoktestreszabása a fenti táblázatnak megfelelően.
6. Az értékelés frissítéséhez kattintson a **Mentés** gombra.


Az értékelési tulajdonságokat is szerkesztheti, amikor értékelést hoz létre.


## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
