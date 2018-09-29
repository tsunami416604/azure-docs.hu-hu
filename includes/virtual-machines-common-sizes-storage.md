---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 961f82cd4970abfdd11a30b2847a14f8ff1880b0
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454510"
---
Tárolási optimalizált Virtuálisgép-méretek magas lemez-adatátviteli és i/o-e, és ideálisak a Big Data, az SQL és NoSQL-adatbázisok. Ez a cikk ismerteti a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélességet az ennél a csoportosításnál méreteire vonatkoztatva számát. 

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.  Ls-sorozat virtuális gépek ideálisak a nagy alkalmazásokhoz, amelyek alacsony késleltetésű, nagy átviteli sebességet és nagy méretű helyi lemezes tárterületet igényelnek. 

Használati Példák többek között a NoSQL-adatbázisok, például Cassandra, MongoDB, Cloudera vagy Redis, adattárházak, és a nagy tranzakciós adatbázisok.

> [!NOTE]
> Az Ls-sorozat használatát az ideiglenes lemez tartós adatlemezek használata helyett a virtuális gép csatlakoztatva van optimalizálva. A nagy átviteli sebesség és a egy ideiglenes lemez IOPS teszi NoSQL-tárolókat, mint például az Apache Cassandra- és MongoDB replikálhatja adatait, így több virtuális gép folyamataik megőrzésére a meghiúsulása esetén egyetlen virtuális Gépet, amely ideális az Ls-sorozat. Az Ls-sorozat nem támogatja a helyi gyorsítótárral a növeli az iops-érték elérhető tartós adatlemezek létrehozását.

## <a name="ls-series"></a>Ls-sorozat

ACU: 180–240

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: Nem támogatott
 
| Méret          | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / MBps | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5,000 / 125        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 10,000 / 250       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80,000 / 800   | 20,000 / 500       | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Az Ls-sorozat virtuális gépei lehetséges maximális lemezteljesítményét lehet, hogy korlátozzák a száma, mérete és szétosztottsága csatlakoztatott lemezeket. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket.

<sup>1</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.

## <a name="size-table-definitions"></a>Mérettábla definíciói

- A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
- A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
- Ha azt szeretné, a virtuális gépek számára a legjobb teljesítmény, a vCPU / 2 lemezt az adatlemezek száma korlátozza.
- **Várható hálózati sávszélesség** maximális összesített értéket jelenít meg [Virtuálisgép-típusonként kiosztott sávszélesség](../articles/virtual-network/virtual-machine-network-throughput.md) összes hálózati adapteren az összes célhelyre. A felső határértékek nem garantáltak, csak útmutatóul szolgálnak a kívánt alkalmazásra megfelelő VM-típus kiválasztásához. A tényleges hálózati teljesítmény számos tényezőtől függ, többek között a hálózat túlterhelésétől, az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól. A hálózati átviteli sebesség optimalizálásával kapcsolatos információkért lásd: [A hálózati átviteli sebesség optimalizálása Windows és Linux rendszeren](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Linux vagy Windows rendszeren a várt hálózati teljesítmény eléréséhez egy adott verzió kiválasztására vagy a virtuális gép optimalizálására lehet szükség. További információkért lásd: [Virtuális gépek átviteli sebességének megbízható tesztelése](../articles/virtual-network/virtual-network-bandwidth-testing.md).
