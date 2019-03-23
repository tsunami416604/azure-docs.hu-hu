---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6eae536bd19a2c0e5707d8e0b379774b6eb2707a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395599"
---
# <a name="what-disk-types-are-available-in-azure"></a>Milyen lemeztípusok érhetők el az Azure-ban?

Azure managed disksbe jelenleg négy lemeztípusok, amelyek közül három általánosan elérhető (GA), a másik szolgáltatás jelenleg előzetes verzióban érhetők el. Következő négy lemeztípusok minden saját cél megfelelő ügyfél-forgatókönyvekkel rendelkezik.

## <a name="disk-comparison"></a>Lemezek összehasonlítása

Az alábbi táblázat összehasonlítja a ultranagy solid-állam-meghajtók (SSD) (előzetes verzió), prémium szintű SSD, standard SSD és szabványos merevlemez-meghajtókon (HDD) a felügyelt lemezek segítségével eldöntheti, melyiket érdemes használni.

|   | Ultranagy SSD (előzetes verzió)   | Prémium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Lemez típusa   |SSD   |SSD   |SSD   |HDD   |
|Forgatókönyv   |I/o-igényes számítási feladatokhoz például az SAP HANA, felső szintű adatbázisok (például SQL, Oracle) és egyéb tranzakció – (nagy erőforrásigényű) számítási feladatokat.   |Éles, teljesítményérzékeny számítási feladatok   |Webkiszolgálók, kisebb terhelésű vállalati alkalmazások és a fejlesztés + tesztelés   |Biztonsági mentés, nem kritikus fontosságú, alkalmi hozzáférést   |
|Lemezméret   |65 536 gibibájtnak (GiB) (előzetes verzió)   |– 32 767 giB    |– 32 767 giB   |– 32 767 giB   |
|Maximális átviteli sebesség   |2000 MiB/s (előzetes verzió)   |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maximális IOPS-érték   |160,000 (előzetes verzió)   |20,000   |6,000   |2,000   |

## <a name="ultra-ssd-preview"></a>Ultranagy SSD (előzetes verzió)

Azure ultranagy SSD (előzetes verzió) az Azure IaaS virtuális gépek nagy teljesítményű, magas iops-érték és egységes közel valós idejű lemezes tárolás biztosításához. Néhány ultranagy SSD további előnyei a következők dinamikusan megváltoztathatja a teljesítmény, a lemez és a számítási feladatok, a virtuális gépek újraindítása nélkül. Ultranagy SSD adatok-igényes számítási feladatokhoz, például az SAP HANA, a felső szintű adatbázisok és a tranzakciós adatokat használó munkaterhelések vannak kialakítva. Ultranagy SSD csak akkor használható, az adatlemezeket. Operációsrendszer-lemezek, prémium szintű SSD-k használatát javasoljuk.

### <a name="performance"></a>Teljesítmény

Az ultranagy lemez üzembe helyezésekor, a kapacitás és a teljesítmény, a lemez egymástól függetlenül beállíthatja. Ultranagy SSD különféle rögzített méretű, és 4 GB, legfeljebb 64 Tib-ra, a következő, és rugalmas teljesítmény konfigurációs modell, amely lehetővé teszi, hogy külön konfigurálni az IOPS és átviteli sebesség funkciót.

Az Ultranagy SSD néhány főbb funkciók a következők:

- Lemez kapacitása: Ultranagy SSD kapacitása címtartományok 4 GiB akár 64 Tib-ra.
- A lemez iops-érték: Ultranagy SSD támogatja a 300 IOPS/GIB-ra, egy 160 KB IOPS lemezenként maximum IOPS-korlátok. Elérése érdekében az Ön által üzembe helyezett iops-érték, győződjön meg arról, hogy a kiválasztott lemez iops-érték kisebb, mint a virtuális gépek iops-érték. A minimális IOPS lemez 100 IOPS.
- Lemez átviteli sebesség: Ultranagy SSD, az átviteli sebesség egyetlen határérték 256 KiB/s minden egyes üzembe helyezett IOPS, legfeljebb 2000 MB/s lemezenként (ahol MBps = 10 ^ 6 bájt / másodperc). A minimális átviteli sebesség 1 MiB.

### <a name="disk-size"></a>Lemezméret

|Lemezméret (GiB)  |IOPS Caps  |Átviteli sebesség korlát (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1024-65,536 (ebben a tartományban, 1 Tib-ra egységnyi növekményekben növelése mérete)     |160,000         |2,000         |

### <a name="preview-scope-and-limitations"></a>Előzetes verzió hatókör és korlátozások

Előzetes verzióban az ultranagy SSD:

- Támogatottak az USA keleti RÉGIÓJA 2, egyetlen rendelkezésre állási zónában  
- Csak akkor használható a rendelkezésre állási zónák (rendelkezésre állási csoportok és egyetlen virtuális gépek üzembe helyezése zónák lesz kívül nem tudnak egy ultra lemezt)
- Csak a gen/DS v3-as virtuális gépek támogatottak
- Az adatlemezeket és a csak 4k támogatási fizikai szektormérettel csak elérhetők  
- Csak üres lemezként hozható létre  
- Jelenleg kizárólag telepíthető Azure Resource Manager-sablonok, CLI és a python SDK használatával.
- Még nem támogatja pillanatképeit, virtuális gép lemezképek, a rendelkezésre állási csoportok, a virtual machine scale sets és az Azure disk encryption.
- Még nem támogatja az Azure Backup vagy az Azure Site Recovery-integráció.
- A [legtöbb előzetes verziók](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), ez a funkció nem használható éles számítási feladatok esetében csak az általánosan (elérhetővé tétel GA).
