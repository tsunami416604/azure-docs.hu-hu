---
title: A StorSimple-régiók rendelkezésre állása |} A Microsoft Docs
description: Ismerteti az Azure-régióban, amelyben a különböző StorSimple-eszközök esetében érhető el.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 7e35aa8120c455b8cc0d0881a9ceb5120c57deff
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969668"
---
# <a name="available-regions-for-your-storsimple"></a>A StorSimple az elérhető régiók

## <a name="overview"></a>Áttekintés

Az Azure-adatközpontokból a teljesítmény, a követelmények és az adatok helyére vonatkozó beállításait az ügyfél figyelembevételével világszerte több földrajzi területeken működik. Egy Azure földrajzi területet adtunk a világ, amely tartalmaz legalább egy Azure-régióban. Egy Azure-régióban egy olyan terület, a földrajzi helyen tartalmazó egy vagy több adatközpont tartozhat.

Egy Azure-régió kiválasztásával nagyon fontos, és befolyásolja a kiválasztott régióban tényezők, például az adatok fizikai tárolási helye és szuverenitás, szolgáltatás rendelkezésre állása, teljesítménye, költség és redundanciát. Válasszon régiót módjáról további információkért lépjen [ami Azure-régióban megfelelő a számomra?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

A StorSimple megoldás a kiválasztott régióban kifejezetten határozza meg a következő tényezőktől függ:

- Régióban, ahol a StorSimple-Eszközkezelő szolgáltatás érhető el.
- Az országok, ahol a StorSimple fizikai eszközök, a felhőben és a virtuális eszköz érhető el.
- A régiók, ahol kell lennie az optimális teljesítmény érdekében a StorSimple-adatok tárolására szolgáló tárfiókot.

Ez az oktatóanyag azt ismerteti, hogy a régiók rendelkezésre állása a StorSimple-Eszközkezelő szolgáltatás, a helyszíni fizikai és a felhőbeli eszközöket. Az ebben a cikkben szereplő információ egyaránt vonatkozik az a StorSimple 8000-es és az 1200-as sorozatú eszközöket.

## <a name="region-availability-for-storsimple-device-manager-service"></a>StorSimple-Eszközkezelő szolgáltatás régiók rendelkezésre állása

StorSimple-Eszközkezelő szolgáltatás jelenleg támogatott 12 nyilvános régiókban és 2 Azure Government-régiókban.

Definiálja a régió vagy helyét, és a StorSimple-Eszközkezelő szolgáltatás első létrehozásakor. Általánosságban véve a földrajzi régióban, ahol az eszköz telepíti legközelebbi hely van kiválasztva. De az eszköz és a szolgáltatást is üzembe helyezhetők más-más helyen.

Ez egy lista, régiók, ahol a StorSimple-Eszközkezelő szolgáltatás érhető el az Azure nyilvános felhő, és is üzembe helyezhetők.

![storsimple-eszköz-manager-szolgáltatás-területek](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Az Azure Government-felhőben a StorSimple-Eszközkezelő szolgáltatás US Gov – Iowa és US Gov Virginia adatközpontokban érhető el.

## <a name="region-availability-for-data-stored-in-storsimple"></a>StorSimple-ben tárolt adatok régiók rendelkezésre állása

A StorSimple adatokat az Azure storage-fiókok fizikailag tárolja, és ezeket a fiókokat az összes Azure-régióban érhető el. Amikor létrehoz egy Azure storage-fiókot, az elsődleges helyen, a tárfiók van kiválasztva, és, amely meghatározza, hogy a régió, ahol az adatok találhatók.

Amikor először hozzon létre egy StorSimple-Eszközkezelő szolgáltatás és storage-fiókot társítson, a StorSimple-Eszközkezelő szolgáltatás és az Azure storage lehet két külön helyen. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia.

Általánosságban elmondható válassza ki a legközelebbi régió, a tárfiók a szolgáltatáshoz. Azonban a legközelebbi Microsoft Azure-régióban valójában nem a régiót, a legkisebb késéssel. Legyen a késés, amely előírja a hálózati szolgáltatás teljesítménye, és ezért a megoldás teljesítményére. Így ha egy storage-fiók egy másik régióban is választja, fontos tudni, hogy az késleltetések Mik a és a tárfiók társított régió között.

Ha egy StorSimple Cloud Appliance használ, majd azt javasoljuk, hogy a szolgáltatás és a kapcsolódó tárfiók van-e ugyanabban a régióban. Egy másik régióban a Storage-fiókok gyenge teljesítményt eredményezhet.

## <a name="availability-of-storsimple-device"></a>StorSimple-eszköz elérhetősége

Függően a modellt, a StorSimple-eszközök is érhetők el az különböző földrajzi vagy országok.

### <a name="storsimple-physical-device-models-81008600"></a>A StorSimple fizikai eszköz (8100-as vagy 8600 modelleket)

A StorSimple 8100-as vagy 8600-as fizikai eszköz használata esetén az eszköz érhető el az alábbi országokban.

| #  | Ország        | #  | Ország     | #  | Ország      | #  | Ország              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Ausztrália      | 16 | Hongkong KKT   | 31 | Új-Zéland  | 46 | Dél-Afrika         |
| 2  | Ausztria        | 17 | Magyarország     | 32 | Nigéria      | 47 | Dél-Korea          |
| 3  | Bahrein        | 18 | Izland     | 33 | Norvégia       | 48 | Spanyolország                |
| 4  | Belgium        | 19 | India       | 34 | Peru         | 49 | Srí Lanka            |
| 5  | Brazília         | 20 | Indonézia   | 35 | Fülöp-szigetek  | 50 | Svédország               |
| 6  | Kanada         | 21 | Írország     | 36 | Lengyelország       | 51 | Svájc          |
| 7  | Chile          | 22 | Izrael      | 37 | Portugália     | 52 | Tajvan               |
| 8  | Kolumbia       | 23 | Olaszország       | 38 | Puerto Rico  | 53 | Thaiföld             |
| 9  | Cseh Köztársaság | 24 | Japán       | 39 | Katar        | 54 | Törökország               |
| 10 | Dánia        | 25 | Kenya       | 40 | Románia      | 55 | Ukrajna              |
| 11 | Egyiptom          | 26 | Kuvait      | 41 | Oroszország       | 56 | Egyesült Arab Emírségek |
| 12 | Finnország        | 27 | Makao       | 42 | Szaúd-Arábia | 57 | Egyesült Királyság       |
| 13 | Franciaország         | 28 | Malajzia    | 43 | Szingapúr    | 58 | Egyesült Államok        |
| 14 | Németország        | 29 | Mexikó      | 44 | Szlovákia     | 59 | Vietnam              |
| 15 | Görögország         | 30 | Hollandia | 45 | Szlovénia     | 60 | Horvátország              |

Ebben a listában több országban kerülnek változik. A régiócsoportok legfrissebb listáját, nyissa meg a tárolási tömb feltételek függelék: az a [használati feltételeiben](https://www.microsoft.com/en-us/licensing/product-licensing/products).

A Microsoft is fizikai hardvereszközöket, és adja meg a tartalék hardveralkatrészek cseréje a storsimple, a fenti listán szereplő földrajzi területeken.

> [!IMPORTANT]
> Ne helyezze a StorSimple fizikai eszköz egy régióban, ahol a StorSimple nem támogatott. A Microsoft nem tud minden olyan országban, ahol nem támogatott a StorSimple cserealkatrészekre szállításra.

### <a name="storsimple-cloud-appliance-models-80108020"></a>A StorSimple Cloud Appliance (8010/8020-as modellek)

Egy StorSimple Cloud Appliance 8010 vagy 8020-as használ, majd az eszköz esetén támogatott, és elérhető minden olyan régióban, ahol a mögöttes virtuális gép használata támogatott. A 8010-es használ egy _Standard_A3_ virtuális Gépet, amely az összes Azure-régióban támogatott.

A 8020-as Premium szintű tárolást használ, és _Standard_DS3_ a virtuális gép létrehozása a felhőalapú berendezés. A 8020-as támogatott az Azure-régióban, amely támogatja a Premium Storage és _Standard_DS3_ Azure virtuális gépeken. [Ebben a listában](https://azure.microsoft.com/regions/services/) ellenőrizheti, hogy a **Virtuális gépek &gt; DS-sorozat** és a **Storage &gt; Disk Storage** termékek egyaránt elérhetők-e az adott régióban.

### <a name="storsimple-virtual-array-model-1200"></a>A StorSimple Virtual Array (1200-as modell)

Ha egy 1200-as sorozat a StorSimple Virtual Array használatával, majd a Virtuálislemez-kép az összes Azure-régióban lesz támogatott.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet a [különböző StorSimple-modellek díjszabás](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Tudjon meg többet [kezelése a StorSimple tárfiók](storsimple-8000-manage-storage-accounts.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).
