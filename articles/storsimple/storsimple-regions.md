---
title: "StorSimple régiónkénti elérhetőség |} Microsoft Docs"
description: "Ismerteti az Azure-régiók, amelyben a különböző StorSimple eszköz modellek érhetők el."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: d47109d541a3df93d9234e27e53d1538f6bc4c6e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="available-regions-for-your-storsimple"></a>A StorSimple elérhető régiók

## <a name="overview"></a>Áttekintés

Az Azure adatközpontjaiban működik, a teljesítmény, a követelmények és a beállítások vonatkozó adatok helye az ügyfél kielégítése érdekében a világ különböző földrajzi. Egy Azure geográfiai egy meghatározott területen a világ, amely tartalmaz legalább egy Azure-régióban. Egy Azure-régió, egy földrajzi hely, egy vagy több adatközpontok tartalmazó terület.

Egy Azure-régió kiválasztásával nagyon fontos, és a választott régió tényezőktől, például az adatok rezidens és a közös joghatóság alá, a szolgáltatás rendelkezésre állása, a teljesítmény, a költség és a redundancia befolyásol. Válasszon egy régiót módjáról további információkért látogasson el [ami Azure-régióban megfelelő a számomra?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

A StorSimple megoldásban a választott régió kifejezetten határozza meg a következő tényezőket:

- Az régió, ahol a StorSimple Device Manager szolgáltatás nem érhető el.
- A országok, ahol a StorSimple fizikai, a felhő és a virtuális eszköz áll rendelkezésre.
- A régió, ahol a tárfiókok a StorSimple adatokat tároló kell lennie az optimális teljesítmény.

Ez az oktatóanyag leírja a régiónkénti elérhetőség, a StorSimple eszköz kezelő szolgáltatás, a helyszíni fizikai és a felhőbeli eszközöket. Ebben a cikkben szereplő információk csak a StorSimple 8000 és 1200 sorozat eszközeire alkalmazható.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Régiónkénti elérhetőség StorSimple Device Manager szolgáltatáshoz

StorSimple Device Manager szolgáltatás jelenleg 12 nyilvános régiók és 2 Azure Government régiókban támogatott.

Amikor először létre kell hoznia a StorSimple Device Manager szolgáltatás olyan régióban vagy a hely definiálni. Általában a legközelebbi a földrajzi régióban, az eszköz telepítési helyét egy helyre van kiválasztva. De az eszköz és a szolgáltatás is telepíthető a különböző helyeken.

Ez egy lista, régió, ahol StorSimple Device Manager szolgáltatás Azure nyilvános felhőjében érhető el és telepíthetők.

![storsimple-eszköz-manager-szolgáltatás-régiók](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Azure Government felhő, a StorSimple Device Manager szolgáltatás érhető el Nekünk – (kormányzati) Iowa és Velünk – (kormányzati) Virginia adatközpontokban.

## <a name="region-availability-for-data-stored-in-storsimple"></a>A StorSimple tárolt adatok esetében régiónkénti elérhetőség

StorSimple-adatokat az Azure storage-fiókok fizikailag tárolja, és ezek a fiókok érhetők el minden Azure-régió. Egy Azure storage-fiók létrehozásakor a tárfiók elsődleges hely van kiválasztva, és, amely meghatározza, hogy a régió, az adatokat tároló.

Amikor először hozzon létre egy StorSimple Device Manager szolgáltatást, és a storage-fiók társítása, a StorSimple eszköz Manager szolgáltatás és az Azure storage lehet két külön helyen. Ilyen esetben a StorSimple-eszközkezelő fiókot és az Azure-tárfiókot külön kell létrehoznia.

Általánosságban elmondható válassza ki a tárfiók a szolgáltatás legközelebbi régiót. Azonban a legközelebbi Microsoft Azure azon régióját előfordulhat, hogy nem lehetnek a régióban, a legkisebb mértékű késleltetést. A késés, amely határozzák meg, hogy a hálózati szolgáltatás teljesítményének, így a megoldás teljesítményére. Ezért kiválasztása a tárfiók más régióban, ha fontos tudni, hogy mi a vannak a és a tároló fiókjához társított régió között.

Ha egy StorSimple felhő készülék használ, majd azt javasoljuk, hogy a szolgáltatás és a kapcsolódó tárfiók ugyanabban a régióban van. Tárfiók más régióban teljesítménycsökkenést eredményezhet.

## <a name="availability-of-storsimple-device"></a>StorSimple-eszköz elérhetősége

Attól függően, a modell, a StorSimple eszközök lehetnek elérhető különböző földrajzi területeken vagy országokból.

### <a name="storsimple-physical-device-models-81008600"></a>A StorSimple fizikai eszköz (modellek 8100/8600)

A StorSimple 8100 vagy 8600 fizikai eszköz használatával, ha az eszköz nem áll rendelkezésre a következő országokban.

| #  | Ország        | #  | Ország     | #  | Ország      | #  | Ország              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Ausztrália      | 16 | Hongkong   | 31 | Új-Zéland  | 46 | Dél-Afrika         |
| 2  | Ausztria        | 17 | Magyarország     | 32 | Nigéria      | 47 | Dél-Korea          |
| 3  | Bahrein        | 18 | Izland     | 33 | Norvégia       | 48 | Spanyolország                |
| 4  | Belgium        | 19 | India       | 34 | Peru         | 49 | Srí Lanka            |
| 5  | Brazília         | 20 | Indonézia   | 35 | Fülöp-szigetek  | 50 | Svédország               |
| 6  | Kanada         | 21 | Írország     | 36 | Lengyelország       | 51 | Svájc          |
| 7  | Chile          | 22 | Izrael      | 37 | Portugália     | 52 | Tajvan               |
| 8  | Kolumbia       | 23 | Olaszország       | 38 | Puerto Rico  | 53 | Thaiföld             |
| 9  | Csehország | 24 | Japán       | 39 | Katar        | 54 | Törökország               |
| 10 | Dánia        | 25 | Kenya       | 40 | Románia      | 55 | Ukrajna              |
| 11 | Egyiptom          | 26 | Kuvait      | 41 | Oroszország       | 56 | Egyesült Arab Emírségek |
| 12 | Finnország        | 27 | Makao       | 42 | Szaúd-Arábia | 57 | Egyesült Királyság       |
| 13 | Franciaország         | 28 | Malajzia    | 43 | Szingapúr    | 58 | Egyesült Államok        |
| 14 | Németország        | 29 | Mexikó      | 44 | Szlovákia     | 59 | Vietnam              |
| 15 | Görögország         | 30 | Hollandia | 45 | Szlovénia     | 60 | Horvátország              |

Ebben a listában több országban kerülnek változik. A földrajzi legfrissebb listáját, látogasson el a tárolási tömb feltételek függelék: az a [termék feltételek](https://www.microsoft.com/en-us/Licensing/product-licensing).

Microsoft fizikai hardvereszközöket, és adja meg az előző listában földrajzi a StorSimple hardver tartalék részek cseréje.

> [!IMPORTANT]
> A StorSimple fizikai eszköz nem helyez egy régiót, ahol StorSimple nem támogatott. A Microsoft nem tudnak majd küldje el minden országban, ahol nem támogatott a StorSimple helyettesítő részből.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple felhő készülék (modellek 8010-es/8020-as modell)

Ha egy StorSimple felhő készülék 8010-es vagy a 8020-as modellt használ, az eszköz akkor támogatott, és elérhető minden régióban, ahol az alapul szolgáló virtuális gép esetén támogatott. A 8010 használatát egy _Standard_A3_ virtuális gép, amely az összes Azure-régiók támogatott.

A 8020-as modellt használ a prémium szintű storage és _Standard_DS3_ virtuális gép egy felhőben készülék létrehozásához. A 8020-as modell esetén támogatott Azure-régiók, amely támogatja a prémium szintű Storage és _Standard_DS3_ Azure virtuális gépeken. [Ebben a listában](https://azure.microsoft.com/regions/services/) ellenőrizheti, hogy a **Virtuális gépek &gt; DS-sorozat** és a **Storage &gt; Disk Storage** termékek egyaránt elérhetők-e az adott régióban.

### <a name="storsimple-virtual-array-model-1200"></a>A StorSimple virtuális tömb (1200-as modell)

Ha 1200 több StorSimple virtuális tömb használ, majd a virtuális lemezképet használata támogatott minden Azure-régió.

## <a name="next-steps"></a>Következő lépések

* További információ a [különböző StorSimple modellek árképzési](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* További információ [tárfiók felügyelete a StorSimple](storsimple-8000-manage-storage-accounts.md).
* További tudnivalók a [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).
