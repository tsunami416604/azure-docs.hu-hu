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
ms.openlocfilehash: 680bf282c2ab269bad19654c6602e4543a6e92ca
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53748467"
---
Tárolási optimalizált Virtuálisgép-méretek magas lemez-adatátviteli és i/o-e, és ideálisak a Big Data, SQL, nosql-alapú adatbázisok, az adattárházak és nagy tranzakciós adatbázisok.  Ilyenek például Cassandra, MongoDB, Cloudera vagy Redis. Ez a cikk ismerteti a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint helyi tároló átviteli sebesség és a hálózati sávszélesség optimalizált méreteire vonatkoztatva számát.

A Lsv2 sorozat funkciók nagy teljesítményű, kis késésű, közvetlenül hozzá van rendelve helyi futó NVMe-tároló a [AMD EPYC<sup>TM</sup> 7551 processzor](https://www.amd.com/en/products/epyc-7000-series) -az összes mag boost 2.55 GHz-es és a egy maximális kiemelése 3.0 GHz-es. A méretek 8 egyidejű többszálas konfigurációban 80 vCPU jár a Lsv2 sorozatú virtuális gépek.  Nincs 8 GiB vCPU-memória és a egy 1.92TB NVMe SSD M.2 készülékenként 8 Vcpu, és akár 19,2 TB (10x1.92TB) a L80s v2 elérhető.

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.

> [!NOTE]
> A Lsv2 sorozatú virtuális gépek közvetlenül a virtuális Géphez csatolt adatlemezek tartós használata helyett a csomóponton a helyi lemez használatára van optimalizálva.  Ez lehetővé teszi a nagyobb IOPs és átviteli sebességet a számítási feladatokhoz.  A Lsv2 és Ls-sorozat nem támogatja az elérhető IOPS tartós adatlemezek-kal növelni a helyi gyorsítótárat a létrehozása. A magas teljesítmény és a helyi lemez IOPS lehetővé teszi a Lsv2 és Ls-sorozat virtuális gépei NoSQL-tárolókat, mint például az Apache Cassandra- és MongoDB replikálhatja adatait, így több virtuális gép folyamataik megőrzésére a meghiúsulása esetén egyetlen virtuális Gépet, amely ideális. 

## <a name="lsv2-series"></a>Lsv2 sorozat
ACU: 150-175

Prémium szintű Storage: Támogatott

Gyorsítótárazás Preminu Storage: Nem támogatott

| Méret          | vCPU | Memória (GiB) | Ideiglenes lemez<sup>1</sup> (GiB) | NVMe lemezeket | NVMe lemezteljesítmény<sup>2</sup> (olvasási IOPS / MBps) | Gazdagép gyorsítótár mérete<sup>3</sup> | Adatlemezek maximális száma | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 340,000 / 2000 | – | 16 | 2 / 3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 680,000 / 4,500 | – | 32 | 4 / 6400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1.4-ES M / 9000    | – | 32 | 8 / 12 800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.7 M / 18,000   | – | 32 | 8 / 25 600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1.92TB   | 3.4 M / 22,000   | – | 32 | 8 / 32000 |
 
<sup>1</sup> Lsv2 sorozatú virtuális gépek egy standard SCSI-alapú ideiglenes erőforrás lemez az operációs rendszer stránkování/felcserélés fájl használata (a Windows, Linux rendszeren /dev/sdb D:) rendelkezik. Ezt a lemezt biztosít a tároló 80 GB, 4 000 iops-t, és 80 MB/s átviteli sebesség a minden 8 Vcpu (pl. Standard_L80s_v2 biztosít 800 GiB 40 000 IOPS és 800 MB/s). Ez biztosítja, hogy az NVMe-meghajtókkal teljes dedikálhatja alkalmazás használatát.

<sup>2</sup> Hyper-V NVMe közvetlen technológia biztonságosan leképezve a Vendég virtuális gép terén NVMe-meghajtókkal leszabályozás hozzáférést biztosít.  A maximális teljesítmény eléréséhez szükséges vagy a legújabb WS2019 build vagy Ubuntu 18.04, 16.04 használatával az Azure Marketplace-ről.  Írási teljesítmény i/o-mérete, a meghajtó terhelés és a tárolókapacitás kihasználtságát alapján változik.

<sup>3</sup> Lsv2-sorozat virtuális gépei nem biztosít gazdagép gyorsítótár adatlemez nem tudják igénybe a Lsv2 számítási feladatokhoz.  Azonban Lsv2 virtuális gépek az Azure virtuális gép rövid élettartamú operációsrendszer lemez lehetőséget (akár 30 GiB) képes kezelni. 



## <a name="ls-series"></a>Ls-sorozat
ACU: 180-240

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott
 
| Méret          | vCPU | Memória (GiB) | Ideiglenes tárhely (GB) | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye (IOPS vagy MBps) | Maximális lemezteljesítmény (IOPS vagy MBps) | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000 / 500 | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Az Ls-sorozat virtuális gépei lehetséges maximális lemezteljesítményét lehet, hogy korlátozzák a száma, mérete és szétosztottsága csatlakoztatott lemezeket. További információkért lásd: [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md).

<sup>1</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.

## <a name="size-table-definitions"></a>Mérettábla definíciói

- A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
- A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
- Ha azt szeretné, a virtuális gépek számára a legjobb teljesítmény, a vCPU / 2 lemezt az adatlemezek száma korlátozza.
- **Várható hálózati sávszélesség** maximális összesített értéket jelenít meg [Virtuálisgép-típusonként kiosztott sávszélesség](../articles/virtual-network/virtual-machine-network-throughput.md) összes hálózati adapteren az összes célhelyre. A felső határértékek nem garantáltak, csak útmutatóul szolgálnak a kívánt alkalmazásra megfelelő VM-típus kiválasztásához. A tényleges hálózati teljesítmény számos tényezőtől függ, többek között a hálózat túlterhelésétől, az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól. A hálózati átviteli sebesség optimalizálásával kapcsolatos információkért lásd: [A hálózati átviteli sebesség optimalizálása Windows és Linux rendszeren](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Linux vagy Windows rendszeren a várt hálózati teljesítmény eléréséhez egy adott verzió kiválasztására vagy a virtuális gép optimalizálására lehet szükség. További információkért lásd: [Virtuális gépek átviteli sebességének megbízható tesztelése](../articles/virtual-network/virtual-network-bandwidth-testing.md).
