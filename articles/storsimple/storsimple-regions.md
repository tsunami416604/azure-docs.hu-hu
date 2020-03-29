---
title: StorSimple régió elérhetősége
description: Az Azure-régiók, ahol a különböző StorSimple eszközmodellek érhetők el.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: a2f04d6faa7770eef7768437ae0e624b76713f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275116"
---
# <a name="available-regions-for-your-storsimple"></a>A StorSimple elérhető régiói

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

Az Azure-adatközpontok világszerte több földrajzi területen működnek, hogy megfeleljenek az ügyfelek teljesítménybeli igényeinek, követelményeinek és az adatok helyével kapcsolatos preferenciáinak. Az Azure-földrajzi terület a világ egy meghatározott területe, amely legalább egy Azure-régiót tartalmaz. Az Azure-régió egy földrajzi területen belüli terület, amely egy vagy több adatközpontot tartalmaz.

Az Azure-régió kiválasztása nagyon fontos, és a régió kiválasztását olyan tényezők befolyásolják, mint az adatok letelepedési helye és a szuverenitás, a szolgáltatás rendelkezésre állása, a teljesítmény, a költség és a redundancia. Ha további információra van kitenni egy régió kiválasztásáról, válassza a [Melyik Azure-régió a megfelelő számomra?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

A StorSimple megoldás esetében a régió kiválasztását kifejezetten a következő tényezők határozzák meg:

- Azok a régiók, ahol a StorSimple Eszközkezelő szolgáltatás elérhető.
- Azok az országok/régiók, ahol a StorSimple fizikai, felhő- vagy virtuális eszköz elérhető.
- A storSimple-adatokat tároló tárfiókokat az optimális teljesítmény érdekében el kell helyezni.

Ez az oktatóanyag ismerteti a StorSimple Eszközkezelő szolgáltatás, a helyszíni fizikai és a felhőbeli eszközök régióelérhetőségét. Az ebben a cikkben található információk a StorSimple 8000 és 1200 sorozatú eszközökre vonatkoznak.

## <a name="region-availability-for-storsimple-device-manager-service"></a>A StorSimple Eszközkezelő szolgáltatás régióelérhetősége

A StorSimple Eszközkezelő szolgáltatás jelenleg 12 nyilvános régióban és 2 Azure Government-régióban támogatott.

A StorSimple Eszközkezelő szolgáltatás első létrehozásakor meg kell adnia egy régiót vagy helyet. Általában az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet választják. De az eszköz és a szolgáltatás is telepíthető a különböző helyeken.

Az alábbiakban azokat a régiókat listázhatja, ahol a StorSimple Device Manager szolgáltatás elérhető az Azure nyilvános felhőjéhez, és telepíthető.

![storsimple-device-manager-service-régiók](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Az Azure Government-felhő esetében a StorSimple Eszközkezelő szolgáltatás az USA Iowa és az USA-beli Gov Virginia adatközpontokban érhető el.

## <a name="region-availability-for-data-stored-in-storsimple"></a>A StorSimple-ben tárolt adatok régióelérhetősége

A StorSimple-adatok fizikailag tárolódnak az Azure storage-fiókokban, és ezek a fiókok az Összes Azure-régióban elérhetők. Amikor létrehoz egy Azure-tárfiókot, a tárfiók elsődleges helyét választja, és ez határozza meg a régiót, ahol az adatok találhatók.

Amikor először hoz létre egy StorSimple Eszközkezelő-szolgáltatást, és hozzá társít egy tárfiókot, a StorSimple Eszközkezelő-szolgáltatás és az Azure-tárhely két külön helyen is megtekinthető. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia.

Általában válassza ki a legközelebbi régiót a szolgáltatása a tárfiókhoz. Előfordulhat azonban, hogy a legközelebbi Microsoft Azure-régió valójában nem a legalacsonyabb késésű régió. Ez a késés, amely előírja a hálózati szolgáltatás teljesítményét, és így a megoldás teljesítményét. Így ha egy másik régióban lévő tárfiókot választ, fontos tudni, hogy mi a késések a szolgáltatás és a tárfiókhoz társított régió között.

Ha egy StorSimple cloud appliance, majd azt javasoljuk, hogy a szolgáltatás és a kapcsolódó tárfiók ugyanabban a régióban. Egy másik régióban lévő tárfiókok gyenge teljesítményt eredményezhetnek.

## <a name="availability-of-storsimple-device"></a>A StorSimple eszköz elérhetősége

A modelltől függően a StorSimple eszközök különböző földrajzi területeken vagy országokban/régiókban is elérhetők.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple fizikai eszköz (Modellek 8100/8600)

StorSimple 8100 vagy 8600 fizikai eszköz használata esetén az eszköz a következő országokban/régiókban érhető el.

| #  | Ország/régió        | #  | Ország/régió     | #  | Ország/régió      | #  | Ország/régió             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Ausztrália             | 16 | Hongkong (KKT)      | 31 | Új-Zéland         | 46 | Dél-afrikai Köztársaság               |
| 2  | Ausztria               | 17 | Magyarország            | 32 | Nigéria             | 47 | Dél-Korea                |
| 3  | Bahrein               | 18 | Izland            | 33 | Norvégia              | 48 | Spanyolország                      |
| 4  | Belgium               | 19 | India              | 34 | Peru                | 49 | Srí Lanka                  |
| 5  | Brazília                | 20 | Indonézia          | 35 | Fülöp-szigetek         | 50 | Svédország                     |
| 6  | Kanada                | 21 | Írország            | 36 | Lengyelország              | 51 | Svájc                |
| 7  | Chile                 | 22 | Izrael             | 37 | Portugália            | 52 | Tajvan                     |
| 8  | Kolumbia              | 23 | Olaszország              | 38 | Puerto Rico         | 53 | Thaiföld                   |
| 9  | Cseh Köztársaság        | 24 | Japán              | 39 | Katar               | 54 | Törökország                     |
| 10 | Dánia               | 25 | Kenya              | 40 | Románia             | 55 | Ukrajna                    |
| 11 | Egyiptom                 | 26 | Kuvait             | 41 | Oroszország              | 56 | Egyesült Arab Emírségek       |
| 12 | Finnország               | 27 | Makaó (KKT)          | 42 | Szaúd-Arábia        | 57 | Egyesült Királyság             |
| 13 | Franciaország                | 28 | Malajzia           | 43 | Szingapúr           | 58 | Egyesült Államok              |
| 14 | Németország               | 29 | Mexikó             | 44 | Szlovákia            | 59 | Vietnam                    |
| 15 | Görögország                | 30 | Hollandia        | 45 | Szlovénia            | 60 | Horvátország                    |

Ez a lista a következővel változik, ahogy több ország/régió kerül hozzáadásra. A földrajzi területek legfrissebb listáját a Termék kifejezések Tárolási tömb feltételei függelékben [találja.](https://www.microsoft.com/en-us/licensing/product-licensing/products)

A Microsoft fizikai hardvert szállíthat, és a StorSimple hardvertartalék alkatrész-cseréjét biztosíthatja az előző lista földrajzi listáihoz.

> [!IMPORTANT]
> StorSimple fizikai eszközt ne helyezzen olyan régióban, ahol a StorSimple nem támogatott. A Microsoft nem tud cserealkatrészeket szállítani olyan országokba/régiókba, ahol a StorSimple nem támogatott.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple cloud appliance (modellek 8010/8020)

Ha egy StorSimple Cloud Appliance 8010 vagy 8020, majd az eszköz támogatott és elérhető minden olyan régióban, ahol az alapul szolgáló virtuális gép támogatott. A 8010 egy _Standard_A3_ virtuális gép, amely támogatja az összes Azure-régiókban.

A 8020 prémium szintű tárhelyet _használ,_ és Standard_DS3 virtuális gép egy felhőalapú készülék létrehozásához. A 8020-as támogatott az Azure-régiókban, amelyek támogatják a prémium szintű storage-t és _Standard_DS3_ Azure-beli virtuális gépek. [Ebben a listában](https://azure.microsoft.com/regions/services/) ellenőrizheti, hogy a **Virtuális gépek &gt; DS-sorozat** és a **Storage &gt; Disk Storage** termékek egyaránt elérhetők-e az adott régióban.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple virtuális tömb (1200-as modell)

Ha egy 1200-as sorozatú StorSimple virtuális tömb, majd a virtuális lemezkép minden Azure-régióban támogatott.

## <a name="next-steps"></a>További lépések

* További információ a [különböző StorSimple modellek áráról.](https://azure.microsoft.com/pricing/calculator/#storsimple2)
* További információ [a StorSimple tárfiók kezeléséről.](storsimple-8000-manage-storage-accounts.md)
* További információ [aStorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)
