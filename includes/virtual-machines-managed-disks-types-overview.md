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
ms.openlocfilehash: 1556a7d482a6644831589937b1847b1d63e72693
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890938"
---
# <a name="what-disk-types-are-available-in-azure"></a>Milyen lemeztípusok érhetők el az Azure-ban?

Azure managed disksbe jelenleg négy lemeztípusok, amelyek közül három általánosan elérhető (GA), a másik szolgáltatás jelenleg előzetes verzióban érhetők el. Következő négy lemeztípusok minden saját cél megfelelő ügyfél-forgatókönyvekkel rendelkezik.

## <a name="disk-comparison"></a>Lemezek összehasonlítása

Az alábbi táblázat összehasonlítja a ultranagy solid-állam-meghajtók (SSD) (előzetes verzió), prémium szintű SSD, standard SSD és szabványos merevlemez-meghajtókon (HDD) a felügyelt lemezek segítségével eldöntheti, melyiket érdemes használni.

|   | Ultranagy SSD (előzetes verzió)   | Prémium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Lemez típusa   |SSD   |SSD   |SSD   |HDD   |
|Forgatókönyv   |I/o-igényes számítási feladatokhoz például az SAP HANA, felső szintű adatbázisok (például SQL, Oracle) és egyéb tranzakció – (nagy erőforrásigényű) számítási feladatokat.   |Éles, teljesítményérzékeny számítási feladatok   |Webkiszolgálók, kisebb terhelésű vállalati alkalmazások és a fejlesztés + tesztelés   |Biztonsági mentés, nem kritikus fontosságú, alkalmi hozzáférést   |
|Lemezméret   |65 536 gibibájtnak (GiB) (előzetes verzió)   |4095 giB (GA), a – 32 767 GiB (előzetes verzió)    |(Elérhetővé tétel GA) 4095 GiB, – 32 767 GiB (előzetes verzió)   |4095 giB (GA), a – 32 767 GiB (előzetes verzió)   |
|Maximális átviteli sebesség   |2000 MiB/s (előzetes verzió)   |250 (elérhetővé tétel GA) MiB/s, 750 MiB/s (előzetes verzió)   |60 MiB/s (GA), 500 MiB/s (előzetes verzió)   |60 Mib/s (GA), 500 MiB/s (előzetes verzió)   |
|Maximális IOPS-érték   |160,000 (előzetes verzió)   |Elérhetővé tétel (GA), 7500 20 000 (előzetes verzió)   |500 (GA), a 2000 (előzetes verzió)   |500 (GA), a 2000 (előzetes verzió)   |

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

## <a name="premium-ssd"></a>Prémium SSD

Az Azure prémium szintű SSD-k révén a virtuális gépek (VM) a nagy teljesítményű és kis késleltetésű lemeztámogatás bemeneti/kimeneti (I/O)-igényű számítási feladatokhoz. A sebesség előnyeit, és a prémium szintű tárolólemezeket teljesítményének figyelembe, áttelepítheti már meglévő Virtuálisgép-lemezek a prémium szintű SSD-k. Prémium szintű SSD-k alkalmasak a kritikus fontosságú éles üzemű alkalmazás számára.

### <a name="disk-size"></a>Lemezméret

A méret, egy csillaggal jelölve jelenleg előzetes verzióban érhető el.

| Prémium szintű SSD-méretek  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60*              | P70*              | P80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Lemez mérete GiB-ban           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8,192     | 16,384     | 32,767     |
| IOPS-érték lemezenként       | Legfeljebb 120 | Legfeljebb 240              | Legfeljebb 500              | Akár 1100 | Akár 2,300              | Legfeljebb 5000              | Akár 7500             | Akár 7500              | Akár 12,500              | Legfeljebb 15 000              | Legfeljebb 20 000              |
| Adattovábbítás lemezenként | Legfeljebb 25 MiB/mp | Legfeljebb 50 MiB/mp | Legfeljebb 100 MiB/mp | Akár 125 MiB/mp | Legfeljebb 150 MiB/mp | Akár 200 MiB/mp | Legfeljebb 250 MiB/mp | Legfeljebb 250 MiB/mp| Legfeljebb 480 MiB/mp | Legfeljebb 750 MiB/mp | Legfeljebb 750 MiB/mp |

## <a name="standard-ssd"></a>Standard SSD

Az Azure standard SSD-k egy költséghatékony tárolási megoldás az IOPS alacsonyabb szinten egységes teljesítményt igénylő feladatokra optimalizált. Standard SSD azok számára, akik szeretne áthelyezni a felhőbe, különösen akkor, ha problémákat tapasztal a HDD-megoldások a helyszínen futó számítási feladatok varianciáját együtt egy jó belépési szintű megoldást kínál. Standard SSD-k jobb rendelkezésre állás, a konzisztencia, a megbízhatóság és a HDD lemezek képest késést biztosít. Standard SSD webkiszolgálók, a alacsony IOPS-alkalmazáskiszolgálók, a kisebb terhelésű vállalati alkalmazások és a fejlesztési/tesztelési feladatokhoz alkalmasak.

### <a name="disk-size"></a>Lemezméret

A méret, egy csillaggal jelölve jelenleg előzetes verzióban érhető el.

| Standard SSD-méretek  | E10               | E15               | E20             | E30 | E40              | E50              | E60*              | E70*              | E80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Lemez mérete GiB-ban           | 128             | 256             | 512            | 1,024  | 2,048            | 4,095     | 8,192     | 16,384     | 32,767    |
| IOPS-érték lemezenként       | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500 | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500             | Legfeljebb 500              | Akár 1300              | Legfeljebb 2000              | Legfeljebb 2000              |
| Adattovábbítás lemezenként |  Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp| Legfeljebb 300 MiB/mp |  Legfeljebb 500 MiB/mp | Legfeljebb 500 MiB/mp |

## <a name="standard-hdd"></a>Standard HDD

Az Azure standard szintű HDD biztosíthat a virtuális gépek megbízható, költséghatékony lemeztámogatás késleltetést toleráló-alapú számítási feladatait. Blobok, táblák, üzenetsorok és fájlokat is támogatja. A standard szintű storage merevlemezes (HDD) meghajtók tárolják az adatokat. Ha virtuális gépek dolgozik, fejlesztési és tesztelési célra, a kevésbé kritikus fontosságú számítási feladatokhoz szabványos SSD és HDD lemez is használható. Standard szintű storage az összes Azure-régióban érhető el.

### <a name="disk-size"></a>Lemezméret

A méret, egy csillaggal jelölve jelenleg előzetes verzióban érhető el.

| Standard lemez típusa  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*              | S70*              | S80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Lemez mérete GiB-ban          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8,192     | 16,384     | 32,767     |
| IOPS-érték lemezenként       | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500 | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500             | Legfeljebb 500              | Akár 1300              | Legfeljebb 2000              | Legfeljebb 2000              |
| Adattovábbítás lemezenként | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp| Legfeljebb 300 MiB/mp | Legfeljebb 500 MiB/mp | Legfeljebb 500 MiB/mp |

## <a name="billing"></a>Számlázás

A felügyelt lemezek használata esetén az alábbi számlázási szempontok érvényesek:

- Lemez típusa
- felügyelt lemez mérete
- Pillanatképek
- Kimenő adatforgalom
- Tranzakciók száma

**Felügyelt lemez mérete**: felügyelt lemezek a kiépítési méret számítjuk fel. Azure maps-(kerekítve) a legközelebbi felajánlott lemezméret kiosztott méretét. A lemezméretek érhető el a részletekért lásd az előző táblázatokban. Minden lemez egy támogatott kiosztott lemez mérete ajánlat vannak leképezve, és ennek megfelelően történik. Például, ha az Ön által üzembe helyezett egy 200 GB Standard SSD, vannak leképezve a lemez mérete vonatkozó ajánlatot E15 (256 GB). Minden üzembe helyezett lemez használata óradíjas a Premium Storage-ajánlat a havi díjak használatával. Például ha üzembe helyezett egy E10 lemezt, és 20 óra múlva törli azt, a számlázás a 20 óra arányosan E10 előfizetésért. Ez a, függetlenül a lemezre írt adatok tényleges mennyiségét.

**A pillanatképek**: A pillanatképek fizetendő használt méret. Például egy felügyelt lemez pillanatképének létrehozása 64 GB kiosztott kapacitással és 10 GB tényleges felhasznált adatok mérete, a pillanatkép számoljuk fel csak a felhasznált adatok mérete 10 GB.
