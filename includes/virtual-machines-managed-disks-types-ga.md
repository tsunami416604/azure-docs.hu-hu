---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e145cf481dd165144b188e6205e4b78cc61359fd
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202548"
---
## <a name="premium-ssd"></a>prémium SSD

Az Azure Premium SSD-k nagy teljesítményű és kis késleltetésű lemezes támogatást biztosítanak a virtuális gépek (VM) számára a bemeneti/kimeneti (i/o) igényű számítási feladatokhoz. A Premium Storage-lemezek sebességének és teljesítményének kihasználásához áttelepítheti a meglévő VM-lemezeket prémium SSD-re. A prémium SSD-k megfelelőek a kritikus fontosságú üzemi alkalmazások számára. A prémium SSD-k csak a Premium Storage-kompatibilis virtuálisgép-sorozatokkal használhatók.

Ha többet szeretne megtudni az Azure-beli virtuálisgép-típusokról és-méretekről a Windowshoz, beleértve a Premium Storage-kompatibilis méreteket, tekintse meg a [Windowsos virtuális gépek méretét](../articles/virtual-machines/windows/sizes.md). Ha többet szeretne megtudni az Azure-beli VM-típusokról és-méretekről, például a Premium Storage-kompatibilis méretekről, tekintse meg a Linux rendszerű [virtuális gépek méretét](../articles/virtual-machines/linux/sizes.md)ismertető témakört. Ezek közül bármelyik cikkből megtudhatja, hogy az egyes virtuálisgép-méretekkel kapcsolatos cikkek alapján megállapíthatja, hogy a prémium szintű Storage kompatibilis-e.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Ha Premium Storage-lemezt épít ki, a standard szintű tárterülettől eltérően, akkor garantált a lemez kapacitása, IOPS és átviteli sebessége. Ha például létrehoz egy P50 lemezt, az Azure 4 095 GB tárolókapacitást, 7 500 IOPS és 250 MB/s adatátviteli sebességet biztosít a lemez számára. Az alkalmazás a kapacitás és a teljesítmény egészét vagy egy részét használhatja. A prémium SSD lemezek úgy lettek kialakítva, hogy alacsony egyszámjegyű ezredmásodperces késéseket és célként megadott IOPS és átviteli sebességet biztosítson az idő 99,9%-ában.

## <a name="bursting-preview"></a>Burst (előzetes verzió)

A P30-nál kisebb méretű prémium SSD a lemezes kitörést (előzetes verzió), és 3 500-ig, a sávszélességük pedig akár 170 Mbps-ra is feltörte a IOPS. A bursás automatizált, és kreditrendszer alapján működik. A kreditek automatikusan halmozódnak, amikor a lemezes forgalom a kiosztott teljesítményű célpont alá esik, és a rendszer automatikusan felhasználja a krediteket, amikor a forgalom a célon kívül esik, a maximális burst korlátig. A maximális burst korlát határozza meg a lemez IOPS & sávszélességét akkor is, ha a rendszer felhasználja a feltört krediteket. A lemezek felskálázása nagyobb toleranciát biztosít az i/o-minták előre nem látható változásaihoz. A legjobb megoldás az operációs rendszer lemezes rendszerindításához és a tüskés forgalommal rendelkező alkalmazásokhoz.    

A lemezek kitörésének támogatását a rendszer alapértelmezés szerint engedélyezi az [előzetes verziójú régiókban](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) lévő, a megfelelő méretű lemezekre vonatkozó új központi telepítések esetén, és nincs szükség felhasználói beavatkozásra. A megfelelő méretű lemezek meglévő lemezei esetében engedélyezheti a kitörést a két lehetőség közül: válassza le és csatlakoztassa újra a lemezt, vagy állítsa le, majd indítsa újra a csatlakoztatott virtuális gépet. Az összes érintett lemez mérete a teljes burst kredit gyűjtővel kezdődik, ha a lemez egy olyan virtuális géphez van csatlakoztatva, amely legfeljebb 30 percnél hosszabb időt támogat a maximálisan megengedettnél. Ha többet szeretne megtudni az Azure-lemezek kitörésével kapcsolatban, tekintse meg a [prémium SSD burst](../articles/virtual-machines/linux/disk-bursting.md)című témakört. 

### <a name="transactions"></a>Tranzakciók

A prémium SSD-k esetében minden I/O-művelet a 256 KiB-as vagy annál nagyobb átviteli sebesség esetében egyetlen I/O-műveletnek számít. A 256 KiB-nál nagyobb teljesítményű i/O-műveletek a 256 KiB-os méretnél több I/O-műveletnek számítanak.

## <a name="standard-ssd"></a>standard SSD

Az Azure standard SSD-k költséghatékony tárolási lehetőséget biztosítanak olyan számítási feladatokhoz, amelyeknek konzisztens teljesítményre van szükségük az alacsonyabb IOPS szinten. A standard SSD jó beléptetési szintű élményt nyújt azok számára, akik a felhőbe szeretnének költözni, különösen akkor, ha a helyszíni HDD-megoldásokon futó munkaterhelések ingadozásával kapcsolatos problémákat tapasztal. A standard merevlemezekhez képest a standard SSD-k jobb rendelkezésre állást, következetességet, megbízhatóságot és késést biztosítanak. A standard SSD-k a webkiszolgálók, az alacsony IOPS, a könnyű használatú vállalati alkalmazások és a fejlesztési/tesztelési feladatok számára alkalmasak. A standard merevlemezekhez hasonlóan a standard SSD-k is elérhetők az összes Azure-beli virtuális gépen.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

A standard SSD-k úgy lettek kialakítva, hogy egyszámjegyű ezredmásodperces késéseket, valamint a IOPS és az átviteli sebességet az előző táblázatban 99%-ban leírt korlátokig. A tényleges IOPS és átviteli sebesség esetenként eltérő lehet a forgalmi mintáktól függően. A standard SSD-k konzisztens teljesítményt biztosítanak, mint a HDD-lemezek, amelyek alacsonyabb késéssel rendelkeznek.

### <a name="transactions"></a>Tranzakciók

A standard SSD-k esetében minden I/O-művelet a 256 KiB-nál kisebb vagy azzal egyenlő, egyetlen I/O-műveletnek számít. A 256 KiB-nál nagyobb teljesítményű i/O-műveletek a 256 KiB-os méretnél több I/O-műveletnek számítanak. Ezek a tranzakciók számlázási hatással vannak.

## <a name="standard-hdd"></a>standard HDD

Az Azure standard HDD-k megbízható, alacsony díjszabású lemezes támogatást biztosítanak a késést okozó munkaterheléseket futtató virtuális gépek számára. A standard szintű Storage szolgáltatásban az adattárakat merevlemez-meghajtókon (HDD-k) tárolja a rendszer. A standard HDD lemezek késése, IOPS és átviteli sebessége az SSD-alapú lemezekhez képest szélesebb körben is változhat. Standard HDD lemezeken írási késések érhetők el a 10ms alatt, a legtöbb IO-művelet esetében pedig a 20ms alatt olvashatók a késések, azonban a tényleges teljesítmény az i/o-mérettől és a számítási feladatoktől függően változhat. A virtuális gépek használatakor szabványos HDD-lemezeket használhat a fejlesztési és tesztelési forgatókönyvekhez, valamint a kevésbé fontos munkaterhelésekhez. A standard szintű HDD-k minden Azure-régióban elérhetők, és minden Azure-beli virtuális géppel használhatók.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Tranzakciók

A standard HDD-k esetében minden egyes IO-művelet egyetlen tranzakciónak tekintendő, az I/O-mérettől függetlenül. Ezek a tranzakciók számlázási hatással vannak.

## <a name="billing"></a>Számlázás

A felügyelt lemezek használatakor az alábbi számlázási szempontokat kell figyelembe venni:

- Lemez típusa
- felügyelt lemez mérete
- Pillanatképek
- Kimenő adatforgalom
- Tranzakciók száma

**Felügyelt lemez mérete**: a felügyelt lemezek számlázása a kiosztott méret alapján történik. Az Azure leképezi a kiosztott méretet (felfelé kerekítve) a legközelebbi felajánlott lemez méretére. A felkínált lemezterület részleteiért tekintse meg az előző táblázatokat. Minden lemez egy támogatott kiosztott lemezre mutat, és ennek megfelelően történik a számlázás. Ha például egy 200 GiB-standard SSD kiépített, az a E15 (256 GiB) lemez méretére vonatkozó ajánlatot képezi le. A kiosztott lemezek számlázása óránként, az Premium Storage ajánlat havi díja alapján történik. Ha például kiépített egy E10 lemezt, és 20 óra elteltével törölte azt, akkor a E10-ajánlat díja 20 óra. Ez a lemezre írt tényleges adatok mennyiségétől függetlenül.

**Pillanatképek**: a pillanatképek számlázása a felhasznált méret alapján történik. Ha például létrehoz egy pillanatképet egy felügyelt lemezről, amely 64 GiB kiosztott kapacitással rendelkezik, és a tényleges felhasznált adatok mérete 10 GiB, a pillanatkép számlázása csak a 10 GiB felhasznált adatméretre történik.
