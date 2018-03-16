---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: c2209a06921ffd6a8efb6fc38dacfa88fc87fa05
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
Tárolási optimalizált Virtuálisgép-méretek nagy átviteli sebesség és IO, és alkalmasak Big Data típusú adatok, az SQL és a NoSQL-adatbázisok. Ez a cikk tájékoztatást ad azokról a Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség minden méretéhez ennél a csoportosításnál száma. 

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.  

## <a name="ls-series"></a>Ls-sorozat

ACU: 180–240
 
| Méret          | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális ideiglenes tárolási teljesítmény: IOPS / MB/s | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5,000 / 125        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 10,000 / 250       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80,000 / 800   | 20,000 / 500       | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Az Ls-sorozatba tartozó virtuális gépek lemezeinek maximális átviteli sebességét a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket. 

<sup>1</sup> példány el különítve az egyetlen ügyfél számára dedikált hardver.

## <a name="size-table-definitions"></a>Mérettábla definíciói

- A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
- A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
- Ls-sorozat adatlemezek gyorsítótárazott üzemmódban nem működik, a gazdagép gyorsítótáras üzemmód értékre kell állítani **nincs**.
- Ha le szeretné kérdezni a legjobb teljesítmény érdekében a virtuális géphez, korlátozza a vCPU / 2 lemezekre adatlemezek száma.
- **Hálózati sávszélesség várt** maximális összesített értéket [VM típusonkénti lefoglalt sávszélesség](../articles/virtual-network/virtual-machine-network-throughput.md) hálózati adapterek összes, az összes cél. A felső határértékek nem garantáltak, csak útmutatóul szolgálnak a kívánt alkalmazásra megfelelő VM-típus kiválasztásához. A tényleges hálózati teljesítmény számos tényezőtől függ, többek között a hálózat túlterhelésétől, az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól. A hálózati átviteli sebesség optimalizálásával kapcsolatos információkért lásd: [A hálózati átviteli sebesség optimalizálása Windows és Linux rendszeren](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Linux vagy Windows rendszeren a várt hálózati teljesítmény eléréséhez egy adott verzió kiválasztására vagy a virtuális gép optimalizálására lehet szükség. További információkért lásd: [Virtuális gépek átviteli sebességének megbízható tesztelése](../articles/virtual-network/virtual-network-bandwidth-testing.md).