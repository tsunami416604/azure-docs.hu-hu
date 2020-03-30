---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386095"
---
## <a name="premium-ssd"></a>Prémium SSD

Az Azure prémium szintű SSD-k nagy teljesítményű és kis késésű lemeztámogatást nyújtanak a bemeneti/kimeneti (IO) nagy terhelésű számítási feladatokat tartalmazó virtuális gépekhez. A prémium szintű tárolólemezek sebességének és teljesítményének kihasználása érdekében áttelepítheti a meglévő virtuálisgép-lemezeket prémium szintű SSD-kre. A prémium SSD-k létfontosságú éles alkalmazásokhoz alkalmasak. Prémium szintű SSD-k csak prémium szintű tárolás-kompatibilis vm sorozattal használhatók.

Ha többet szeretne tudni az egyes virtuálisgép-típusokról és -méretekről a Windows-alapú Azure-ban, beleértve a prémium szintű tárhelyet is, olvassa el a Windows virtuális gépek méretei című [témakört.](../articles/virtual-machines/windows/sizes.md) Ha többet szeretne megtudni az egyes virtuálisgép-típusokról és -méretekről a Linux-alapú Azure-ban, beleértve a prémium szintű storage-kompatibilis méreteket, olvassa el a Linux virtuális gépek méretei című [témakört.](../articles/virtual-machines/linux/sizes.md) Ezek közül a cikkek közül bármelyik, ellenőriznie kell minden egyes virtuális gép mérete cikk et annak megállapítására, hogy prémium szintű storage-kompatibilis.From either of those articles, you need to check each each individual VM size article to determine if it is premium storage-compatible.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Prémium szintű tárolólemez kiépítésekor a normál tárolóval ellentétben garantáltan a lemez kapacitása, IOPS-a és átviteli kapacitása. Ha például létrehoz egy P50-lemezt, az Azure 4095 GB-os tárolókapacitást, 7500 IOPS-t és 250 MB/s átviteli kapacitást biztosít a lemezhez. Az alkalmazás a kapacitás és a teljesítmény egészét vagy egy részét használhatja. A prémium szintű SSD-lemezek alacsony egyszámjegyű ezredmásodperces késéseket és cél IOPS-t és átviteli frekvenciát biztosítanak az előző táblázatban az idő 99,9%-ában.

## <a name="bursting"></a>Tele

A P30-nál kisebb prémium szintű SSD-méretek mostantól lemezfelszakítást kínálnak, és akár 3500-ig is feltörhetik az IOPS-t, és akár 170 Mbps sávszélességet is biztosíthatnak. A bursting automatizált, és egy kreditrendszeren alapul. A kreditek automatikusan egy burst gyűjtőben halmozódnak fel, ha a lemezforgalom a kiosztott teljesítménycél alatt van, és a rendszer automatikusan felhasználja a krediteket, amikor a forgalom a célon túl, a maximális burst korlátig tart. A maximális burst limit határozza meg a felső határ a lemez IOPS & sávszélesség akkor is, ha burst hitelek fogyasztani. A lemezfelszakítás jobb toleranciát biztosít az IO-minták előre nem látható változásaival szemben. Tudod a legjobb emelőerő ez részére OS korong csomagtartó és pályázatokat -val tüskés forgalom.    

A lemezek felszakítási támogatása alapértelmezés szerint engedélyezve lesz a megfelelő lemezméretekkel rendelkező új telepítéseken, felhasználói beavatkozás nélkül. A meglévő lemezek a megfelelő méretű, engedélyezheti a felszakítás két lehetőség közül választhat: leválasztja, és újra csatlakoztatja a lemezt, vagy állítsa le, és indítsa újra a csatlakoztatott virtuális gép. Minden burst vonatkozó lemezméretek indul egy teljes burst hitel gyűjtő, ha a lemez csatlakozik egy virtuális gép, amely támogatja a maximális időtartam csúcs burst limit 30 mins. Ha többet szeretne megtudni arról, hogyan működik a bursting az Azure Disks-en, olvassa el [a prémium szintű SSD-sorozat.](../articles/virtual-machines/linux/disk-bursting.md) 

### <a name="transactions"></a>Tranzakciók

Prémium szintű SSD-k esetén minden 256 KiB átviteli képesvagy azzal egyenlő I/O-művelet egyetlen I/O-műveletnek minősül. A 256 KiB-nél nagyobb I/O-műveletek több 256 KiB méretű I/O-nak számítanak.

## <a name="standard-ssd"></a>Standard SSD

Az Azure standard SSD-k költséghatékony tárolási lehetőségek, amelyek olyan számítási feladatokhoz vannak optimalizálva, amelyek nek konzisztens teljesítményre van szükségük alacsonyabb IOPS-szinteken. Standard SSD kínál egy jó belépő szintű élményt azok számára, akik szeretnének áthelyezni a felhőbe, különösen akkor, ha problémákat tapasztal a számítási feladatok varianciája fut a HDD-megoldások a helyszínen. A szabványos HDD-khez képest a szabványos SSD-k jobb rendelkezésre állást, konzisztenciát, megbízhatóságot és késleltetést biztosítanak. A szabványos SSD-k webkiszolgálókhoz, alacsony IOPS-alkalmazáskiszolgálókhoz, könnyen használt vállalati alkalmazásokhoz és fejlesztési/tesztelési számítási feladatokhoz alkalmasak. A szabványos HDD-khez hasonlóan a szabványos SSD-k is minden Azure-beli virtuális gépen elérhetők.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standard SSD célja, hogy egyszámjegyű ezredmásodperces késések és az IOPS és átviteli keresztül az előző táblázatban leírt határértékeket az idő 99%-ában. A tényleges IOPS és az átviteli forgalom a forgalmi mintáktól függően változhat. A szabványos SSD-k egységesebb teljesítményt nyújtanak, mint az alacsonyabb késleltetéssel rendelkező HDD-lemezek.

### <a name="transactions"></a>Tranzakciók

Szabványos SSD-k esetén minden 256 KiB átviteli képesvagy azzal egyenlő I/O-művelet egyetlen I/O-műveletnek minősül. A 256 KiB-nél nagyobb I/O-műveletek több 256 KiB méretű I/O-nak számítanak. Ezek a tranzakciók számlázási hatással járnak.

## <a name="standard-hdd"></a>Standard HDD

Az Azure standard HDD-k megbízható, alacsony költségű lemeztámogatást nyújtanak a késést nem kioldó számítási feladatokat futtató virtuális gépek számára. A szabványos tárolás esetén az adatok merevlemezeken (HDD- k) tárolódnak. A szabványos HDD-lemezek késése, IOPS- és átviteli fazeka az SSD-alapú lemezekhez képest szélesebb körben eltérő lehet. A szabványos HDD-lemezek 10 ms alatti írási késéseket és 20 ms alatti olvasási késéseket biztosítanak a legtöbb I/O-művelet esetében, azonban a tényleges teljesítmény az IO méretétől és a munkaterhelési mintától függően változhat. Virtuális gépekkel való munka során szabványos HDD-lemezeket használhat fejlesztési és tesztelési forgatókönyvekhez és kevésbé kritikus számítási feladatokhoz. A szabványos HDD-k minden Azure-régióban elérhetők, és az összes Azure-beli virtuális géphez használhatók.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Tranzakciók

Standard HDD-k esetén minden I/O-művelet egyetlen tranzakciónak számít, függetlenül az I/O méretétől. Ezek a tranzakciók számlázási hatással járnak.

## <a name="billing"></a>Számlázás

Felügyelt lemezek használata esetén a következő számlázási szempontok érvényesek:

- Lemeztípus
- felügyelt lemez mérete
- Pillanatképek
- Kimenő adatátvitel
- Tranzakciók száma

**Felügyelt lemez mérete:** a felügyelt lemezek számlázása a kiosztott méret alapján történik. Az Azure leképezi a kiosztott méretet (felfelé kerekítve) a legközelebbi ajánlott lemezméretre. A felajánlott lemezméretekkel kapcsolatos részletekért tekintse meg az előző táblázatokat. Minden lemez leképezi a támogatott kiépített lemez méretajánlat, és ennek megfelelően számlázik. Ha például egy 200 GiB standard SSD-t épített ki, az az E15 (256 GiB) lemezméret-ajánlatához van rendelve. Bármely kiosztott lemez számlázása óránként imitálja a prémium szintű storage-ajánlat havi díját. Ha például kiépített egy E10-lemezt, és 20 óra elteltével törölte, akkor az E10-ajánlat 20 órás arányosításáért kell fizetnie. Ez független a lemezre írt tényleges adatok mennyiségétől.

**Pillanatképek:** A pillanatképek számlázása a használt méret alapján. Ha például egy 64 GiB-kapacitású felügyelt lemezről és 10 GiB tényleges használt adatmérettel rendelkező felügyelt lemez pillanatképét hoz létre, a pillanatkép csak a 10 GiB használt adatméretért kerül számlázásra.
