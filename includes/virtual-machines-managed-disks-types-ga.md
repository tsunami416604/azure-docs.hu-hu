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
ms.openlocfilehash: 88a4110d68dc8aa921d647f90de654d2ebb4e17d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395738"
---
## <a name="premium-ssd"></a>Prémium SSD

Az Azure prémium szintű SSD-k révén a virtuális gépek (VM) a nagy teljesítményű és kis késleltetésű lemeztámogatás bemeneti/kimeneti (I/O)-igényű számítási feladatokhoz. A sebesség előnyeit, és a prémium szintű tárolólemezeket teljesítményének figyelembe, áttelepítheti már meglévő Virtuálisgép-lemezek a prémium szintű SSD-k. Prémium szintű SSD-k alkalmasak a kritikus fontosságú éles üzemű alkalmazás számára.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="standard-ssd"></a>Standard SSD

Az Azure standard SSD-k egy költséghatékony tárolási megoldás az IOPS alacsonyabb szinten egységes teljesítményt igénylő feladatokra optimalizált. Standard SSD azok számára, akik szeretne áthelyezni a felhőbe, különösen akkor, ha problémákat tapasztal a HDD-megoldások a helyszínen futó számítási feladatok varianciáját együtt egy jó belépési szintű megoldást kínál. Standard SSD-k jobb rendelkezésre állás, a konzisztencia, a megbízhatóság és a HDD lemezek képest késést biztosít. Standard SSD webkiszolgálók, a alacsony IOPS-alkalmazáskiszolgálók, a kisebb terhelésű vállalati alkalmazások és a fejlesztési/tesztelési feladatokhoz alkalmasak.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

## <a name="standard-hdd"></a>Standard HDD

Az Azure standard szintű HDD biztosíthat a virtuális gépek megbízható, költséghatékony lemeztámogatás késleltetést toleráló-alapú számítási feladatait. Blobok, táblák, üzenetsorok és fájlokat is támogatja. A standard szintű storage merevlemezes (HDD) meghajtók tárolják az adatokat. Ha virtuális gépek dolgozik, fejlesztési és tesztelési célra, a kevésbé kritikus fontosságú számítási feladatokhoz szabványos SSD és HDD lemez is használható. Standard szintű storage az összes Azure-régióban érhető el.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>Számlázás

A felügyelt lemezek használata esetén az alábbi számlázási szempontok érvényesek:

- Lemez típusa
- felügyelt lemez mérete
- Pillanatképek
- Kimenő adatforgalom
- Tranzakciók száma

**Felügyelt lemez mérete**: felügyelt lemezek a kiépítési méret számítjuk fel. Azure maps-(kerekítve) a legközelebbi felajánlott lemezméret kiosztott méretét. A lemezméretek érhető el a részletekért lásd az előző táblázatokban. Minden lemez egy támogatott kiosztott lemez mérete ajánlat vannak leképezve, és ennek megfelelően történik. Például, ha az Ön által üzembe helyezett egy 200 GB Standard SSD, vannak leképezve a lemez mérete vonatkozó ajánlatot E15 (256 GB). Minden üzembe helyezett lemez használata óradíjas a Premium Storage-ajánlat a havi díjak használatával. Például ha üzembe helyezett egy E10 lemezt, és 20 óra múlva törli azt, a számlázás a 20 óra arányosan E10 előfizetésért. Ez a, függetlenül a lemezre írt adatok tényleges mennyiségét.

**A pillanatképek**: A pillanatképek fizetendő használt méret. Például egy felügyelt lemez pillanatképének létrehozása 64 GB kiosztott kapacitással és 10 GB tényleges felhasznált adatok mérete, a pillanatkép számoljuk fel csak a felhasznált adatok mérete 10 GB.