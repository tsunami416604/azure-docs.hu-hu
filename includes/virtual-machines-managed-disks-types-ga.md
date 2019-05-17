---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 30df480eb314594cbc4d949302aff11e6d764b6f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65815555"
---
## <a name="premium-ssd"></a>Prémium SSD

Az Azure prémium szintű SSD-k révén a virtuális gépek (VM) a nagy teljesítményű és kis késleltetésű lemeztámogatás bemeneti/kimeneti (I/O)-igényű számítási feladatokhoz. A sebesség előnyeit, és a prémium szintű tárolólemezeket teljesítményének figyelembe, áttelepítheti már meglévő Virtuálisgép-lemezek a prémium szintű SSD-k. Prémium szintű SSD-k alkalmasak a kritikus fontosságú éles üzemű alkalmazás számára. Prémium szintű SSD-k csak akkor használható Virtuálisgép-sorozat, amelyek a premium storage-kompatibilis.

További információ az egyes Virtuálisgép-típusok és -méretek az Azure a Windows, mely méretek a következők prémium szintű storage-kompatibilis, beleértve: [Windows Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes.md). További információ az egyes Virtuálisgép-típusok és az Azure-ban Linux rendszeren, mely méretek a következők prémium szintű storage-kompatibilis, beleértve: [Linux Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Amikor üzembe helyez egy prémium szintű tárolólemez standard szintű tárolás esetén, garantáltan a kapacitás, IOPS és átviteli sebessége a lemezen. Ha például P50 lemez létrehozása, ha Azure látja el 4095 GB-os tárolási kapacitás, 7500 IOPS és 250 MB/s átviteli sebességet, hogy a lemez. Az alkalmazás használhatja a kapacitás és teljesítmény részét vagy egészét. Prémium szintű SSD-lemezeket úgy tervezték, hogy a cél teljesítmény 99,9 %-ában.

### <a name="transactions"></a>Tranzakciók

Prémium szintű SSD-k, az egyes i/o műveletek kisebb vagy egyenlő, mint 256 átviteli KiB számít egy egyetlen i/o-művelet. Az átviteli sebesség nagyobb, mint 256 KiB műveletei számítanak több i/o-mérete 256 KiB.

## <a name="standard-ssd"></a>Standard SSD

Az Azure standard SSD-k egy költséghatékony tárolási megoldás az IOPS alacsonyabb szinten egységes teljesítményt igénylő feladatokra optimalizált. Standard SSD azok számára, akik szeretne áthelyezni a felhőbe, különösen akkor, ha problémákat tapasztal a HDD-megoldások a helyszínen futó számítási feladatok varianciáját együtt egy jó belépési szintű megoldást kínál. Standard HDD képest, standard SSD-k nyújthat jobb rendelkezésre állás, a konzisztencia, a megbízhatóság és a késés. Standard SSD webkiszolgálók, a alacsony IOPS-alkalmazáskiszolgálók, a kisebb terhelésű vállalati alkalmazások és a fejlesztési/tesztelési feladatokhoz alkalmasak. Standard HDD-k, például a standard SSD-k minden Azure virtuális gépeken érhető el.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standard SSD-k egyszámjegyű ezredmásodperces késéseket nyújt a legtöbb i/o-műveletek és az IOPS és átviteli sebesség kereteken belül az előző táblázatban 99 %-ában ismertetett tervezték. Tényleges IOPS és átviteli sebesség függvénye néha a forgalmi minták. Standard SSD-k, mint a HDD-lemezek stabilabb teljesítményt biztosít a kisebb hálózati késést.

### <a name="transactions"></a>Tranzakciók

Standard SSD-k, az egyes i/o műveletek kisebb vagy egyenlő, mint 256 átviteli KiB számít egy egyetlen i/o-művelet. Az átviteli sebesség nagyobb, mint 256 KiB műveletei számítanak több i/o-mérete 256 KiB. Ezek a tranzakciók számlázási hatást.

## <a name="standard-hdd"></a>Standard HDD

Az Azure standard szintű HDD biztosíthat a virtuális gépek megbízható, költséghatékony lemeztámogatás késleltetést toleráló-alapú számítási feladatait. Blobok, táblák, üzenetsorok és fájlokat is támogatja. A standard szintű storage merevlemezes (HDD) meghajtók tárolják az adatokat. Ha virtuális gépek dolgozik, fejlesztési és tesztelési célra, a kevésbé kritikus fontosságú számítási feladatokhoz szabványos SSD és HDD lemez is használható. Standard HDD-k minden Azure-régióban érhető el, és az összes Azure-alapú virtuális gépekhez használható.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Tranzakciók

Standard HDD minden egyes i/o-művelet különálló tranzakcióként, függetlenül az i/o-mérete számít történik. Ezek a tranzakciók számlázási hatást.

## <a name="billing"></a>Számlázás

A felügyelt lemezek használata esetén az alábbi számlázási szempontok érvényesek:

- Lemez típusa
- felügyelt lemez mérete
- Pillanatképek
- Kimenő adatforgalom
- Tranzakciók száma

**Felügyelt lemez mérete**: felügyelt lemezek a kiépítési méret számítjuk fel. Azure maps-(kerekítve) a legközelebbi felajánlott lemezméret kiosztott méretét. A lemezméretek érhető el a részletekért lásd az előző táblázatokban. Minden lemez egy támogatott kiosztott lemez mérete ajánlat vannak leképezve, és ennek megfelelően történik. Például, ha az Ön által üzembe helyezett egy 200 GB Standard SSD, vannak leképezve a lemez mérete vonatkozó ajánlatot E15 (256 GB). Minden üzembe helyezett lemez használata óradíjas a Premium Storage-ajánlat a havi díjak használatával. Például ha üzembe helyezett egy E10 lemezt, és 20 óra múlva törli azt, a számlázás a 20 óra arányosan E10 előfizetésért. Ez a, függetlenül a lemezre írt adatok tényleges mennyiségét.

**A pillanatképek**: A pillanatképek fizetendő használt méret. Például egy felügyelt lemez pillanatképének létrehozása 64 GB kiosztott kapacitással és 10 GB tényleges felhasznált adatok mérete, a pillanatkép számoljuk fel csak a felhasznált adatok mérete 10 GB.