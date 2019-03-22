---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0201776914610ddaca50a670fc500156a65cd734
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2019
ms.locfileid: "58124684"
---
## <a name="premium-ssd"></a>Prémium SSD

Az Azure prémium szintű SSD-k révén a virtuális gépek (VM) a nagy teljesítményű és kis késleltetésű lemeztámogatás bemeneti/kimeneti (I/O)-igényű számítási feladatokhoz. A sebesség előnyeit, és a prémium szintű tárolólemezeket teljesítményének figyelembe, áttelepítheti már meglévő Virtuálisgép-lemezek a prémium szintű SSD-k. Prémium szintű SSD-k alkalmasak a kritikus fontosságú éles üzemű alkalmazás számára.

### <a name="disk-size"></a>Lemezméret

A méret, egy csillaggal jelölve jelenleg előzetes verzióban érhető el.

| Prémium szintű SSD-méretek | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60* | P70* | P80* |
|-------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Lemez mérete GiB-ban | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8,192 | 16,384 | 32,767 |
| IOPS-érték lemezenként | Legfeljebb 120 | Legfeljebb 240 | Legfeljebb 500 | Akár 1100 | Akár 2,300 | Legfeljebb 5000 | Akár 7500 | Akár 7500 | Akár 12,500 | Legfeljebb 15 000 | Legfeljebb 20 000 |
| Adattovábbítás lemezenként | Legfeljebb 25 MiB/mp | Legfeljebb 50 MiB/mp | Legfeljebb 100 MiB/mp | Akár 125 MiB/mp | Legfeljebb 150 MiB/mp | Akár 200 MiB/mp | Legfeljebb 250 MiB/mp | Legfeljebb 250 MiB/mp| Legfeljebb 480 MiB/mp | Legfeljebb 750 MiB/mp | Legfeljebb 750 MiB/mp |

## <a name="standard-ssd"></a>Standard SSD

Az Azure standard SSD-k egy költséghatékony tárolási megoldás az IOPS alacsonyabb szinten egységes teljesítményt igénylő feladatokra optimalizált. Standard SSD azok számára, akik szeretne áthelyezni a felhőbe, különösen akkor, ha problémákat tapasztal a HDD-megoldások a helyszínen futó számítási feladatok varianciáját együtt egy jó belépési szintű megoldást kínál. Standard SSD-k jobb rendelkezésre állás, a konzisztencia, a megbízhatóság és a HDD lemezek képest késést biztosít. Standard SSD webkiszolgálók, a alacsony IOPS-alkalmazáskiszolgálók, a kisebb terhelésű vállalati alkalmazások és a fejlesztési/tesztelési feladatokhoz alkalmasak.

### <a name="disk-size"></a>Lemezméret

A méret, egy csillaggal jelölve jelenleg előzetes verzióban érhető el.

| Standard SSD-méretek | E4 | E6 | E10 | E15 | E20 | E30 | E40 | E50 | E60* | E70* | E80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Lemez mérete GiB-ban | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8,192 | 16,384 | 32,767 |
| IOPS-érték lemezenként | Legfeljebb 120 | Legfeljebb 240 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Akár 1300 | Legfeljebb 2000 | Legfeljebb 2000 |
| Adattovábbítás lemezenként |  Legfeljebb 25 MiB/mp |  Legfeljebb 50 MiB/mp  |  Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp| Legfeljebb 300 MiB/mp |  Legfeljebb 500 MiB/mp | Legfeljebb 500 MiB/mp |

## <a name="standard-hdd"></a>Standard HDD

Az Azure standard szintű HDD biztosíthat a virtuális gépek megbízható, költséghatékony lemeztámogatás késleltetést toleráló-alapú számítási feladatait. Blobok, táblák, üzenetsorok és fájlokat is támogatja. A standard szintű storage merevlemezes (HDD) meghajtók tárolják az adatokat. Ha virtuális gépek dolgozik, fejlesztési és tesztelési célra, a kevésbé kritikus fontosságú számítási feladatokhoz szabványos SSD és HDD lemez is használható. Standard szintű storage az összes Azure-régióban érhető el.

### <a name="disk-size"></a>Lemezméret

A méret, egy csillaggal jelölve jelenleg előzetes verzióban érhető el.

| Standard lemez típusa | S4 | S6 | S10 | S15 | S20 | S30 | S40 | S50 | S60* | S70* | S80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Lemez mérete GiB-ban | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8,192 | 16,384 | 32,767 |
| IOPS-érték lemezenként | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Legfeljebb 500 | Akár 1300 | Legfeljebb 2000 | Legfeljebb 2000 |
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