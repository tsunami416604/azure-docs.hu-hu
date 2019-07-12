---
title: Az Azure Migrate Server Assessment értékelések testreszabása |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure Migrate Server Assessment értékelés testreszabása
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807929"
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

Ez a cikk bemutatja, hogyan hozott létre az Azure Migrate Server Assessment értékelések testreszabása.

[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és a számítási feladatok és a privát és nyilvános felhőbeli VM-eken, az Azure-ba való migrálásának nyomon követéséhez. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.

Az Azure Migrate Server Assessment eszközzel hozhat létre értékeléseket a helyszíni VMware virtuális gépek és a Hyper-V virtuális gépek, az Azure-ba való migrálásra való felkészüléskor. 

## <a name="about-assessments"></a>Értékelések kapcsolatban

Értékelések futtatása az Azure Migrate Server Assessment használatával két típusa van.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Értékelések alapján összegyűjtött teljesítményadatok | **Virtuálisgép-méretet ajánlott**: A Processzor és a memória kihasználtsági adatok alapján.<br/><br/> **Lemez típusa (standard vagy prémium szintű felügyelt lemez) ajánlott**: Az IOPS és átviteli sebessége, a helyi lemezek alapján.
**Helyszíni** | Értékelések alapján helyszíni méretezést. | **Virtuálisgép-méretet ajánlott**: A helyszíni virtuális gép mérete alapján<br/><br> **Ajánlott a lemez típusát**: A tárolási típus beállítását választja, az értékelés alapján.


## <a name="how-is-an-assessment-done"></a>Hogyan történik az értékelés?

Az Azure Migrate értékelés három fázisa van. Értékelés egy alkalmasságát elemzés, a méretezés, kiegészítve előtaggal kezdődik, és végül egy-egy havi költségbecsléshez. A gép csak áthelyezi egy későbbi szakaszban Ha átadja az előzőre. Például egy gép az Azure-megfelelőségi ellenőrzés meghiúsul, ha meg van jelölve, nem alkalmasak az Azure, és a méretezési és költségszámítási nem történik meg.

## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/> Az Azure Migrate jelenleg ezek célrégiók támogatja: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Kanada közép-India, kelet-Kanada, közép-India, USA középső RÉGIÓJA, kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti RÉGIÓJA, 2 USA keleti régiója, közép-India, Németország északkelet-Németország, japán keleti régiójában, Nyugat-japán, Korea középső régiója, Korea déli régiója, Észak USA középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, USA beli államigazgatás – Arizona, USA-beli államigazgatás – Texas, USA-beli államigazgatás – Virginia, USA nyugati középső RÉGIÓJA, Nyugat-Európa, Nyugat-India, USA nyugati RÉGIÓJA és USA 2. nyugati régiója.<br/> Az alapértelmezetten beállított célrégió az USA 2. nyugati régiója.
**Tárolás típusa** | Standard HHD lemezek/Standard SSD lemez/Premium.<br/> A tárolási típust tartalmaz egy értékelés automatikus ad meg, ha a lemez javaslatot a teljesítményadatokat a lemezek (IOPS és átviteli sebesség) alapul.<br/> Ha prémium vagy Standard a tárolási típust ad meg, az értékelés javasolni a felhasználóknak egy lemezt Termékváltozat belül a tárolási típust kiválasztva.<br/> Ha azt szeretné, 99,9 %-os virtuális gép SLA-t egyetlen példány eléréséhez, beállíthatja a tárolási típust, prémium szintű managed disks. Az értékelésben az összes lemez majd javasolni, prémium szintű managed disks. <br/> Az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.<br/> 
**Reserved Instances (RI)** | Adja meg ezt a tulajdonságot, ha Ön fenntartott példányok az Azure-ban. Költség becsléseket az értékelés során figyelembe kell vennie a fenntartott példányok kedvezmények. A fenntartott példányok jelenleg csak akkor támogatott, a használatalapú fizetéssel az Azure Migrate kínál.
**Méretezési feltétel** | Jobb méretű virtuális gépek segítségével. Méretezési lehet a teljesítmény-alapú, vagy **helyszíni**, teljesítményelőzmények figyelembe vétele nélkül.
**Teljesítményelőzmények** | A virtuális gépek teljesítményének értékelésekor figyelembe veendő időtartam. Ez a tulajdonság csak akkor alkalmazható, teljesítményalapú méretezés esetén.
**Százalékos kihasználtság** | A teljesítmény-minta a megfelelő virtuális gépekhez használt PERCENTILIS értéke. Ez a tulajdonság csak akkor alkalmazható, teljesítményalapú méretezés esetén.
**Virtuálisgép-sorozatok** | Méretbecslésekhez használt virtuálisgép-sorozatok. Például egy olyan éles környezetben, amelyet nem szeretne A-sorozatú virtuális gépekre migrálni az Azure-ban, kizárhatja a listából vagy sorozatból az A-sorozatot. Ilyenkor a méretezés csak a kiválasztott sorozatokat veszi figyelembe.
**Kényelmi faktor** | Az Azure Migrate Server Assessment értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2\.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A számlázás pénzneme. 
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények.<br/> Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha nem tervezi a virtuális gépek 24 x 7 fut az Azure-ban, amely azok kellene futnia adhat meg az időtartamot (havonta napok száma) és napi óraszám, és a költségbecslések elkészítését ennek megfelelően kell végrehajtania.<br/> Az alapértelmezett értéke 31 nap / hó és napi 24 órában.
**Azure Hybrid Benefit** | Itt adhatja meg e rendelkezik frissítési garanciával, és jogosult [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. | Az alapértelmezett érték az Igen.


## <a name="edit-assessment-properties"></a>Értékelés tulajdonságainak szerkesztése

Értékelés tulajdonságainak szerkesztése után értékelését hozza létre, tegye a következőket:

1. Az Azure Migrate-projektben kattintson **kiszolgálók**.
2. A **Azure Migrate: Server Assessment**, kattintson az értékelések száma.
3. A **értékelés**, kattintson a megfelelő értékelés > **tulajdonságainak szerkesztése**.
5. Testre szabhatja az értékelésben a fenti táblázat szerint.
6. Kattintson a **mentése** az értékelés frissítése.


Az értékelés létrehozásakor értékelésben is szerkesztheti.


## <a name="next-steps"></a>További lépések

[További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
