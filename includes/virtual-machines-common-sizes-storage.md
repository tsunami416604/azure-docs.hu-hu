---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: c5e6a44409e082f10a532759e3403f6b5801fdca
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551546"
---
Tárolási optimalizált Virtuálisgép-méretek magas lemez-adatátviteli és i/o-e, és ideálisak a Big Data, SQL, nosql-alapú adatbázisok, az adattárházak és nagy tranzakciós adatbázisok.  Ilyenek például Cassandra, MongoDB, Cloudera vagy Redis. Ez a cikk ismerteti a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint helyi tároló átviteli sebesség és a hálózati sávszélesség optimalizált méreteire vonatkoztatva számát.

A Lsv2 sorozat funkciók nagy teljesítményű, kis késésű, közvetlenül hozzá van rendelve helyi futó NVMe-tároló a [AMD EPYC &trade; 7551 processzor](https://www.amd.com/en/products/epyc-7000-series) -az összes mag boost 2.55 GHz-es és a egy maximális kiemelése 3.0 GHz-es. Az Lsv2 sorozatú virtuális gépek elérhetőek 8 és 80 vCPU-s méretekben egy párhuzamos többszálas konfigurációban.  A vCPU-nkénti memória 8 GiB, és 8 vCPU-nkét egy 1,92 TB-os NVMe SSD M.2 eszköz, és az L80s v2-nél akár 19,2 TB (10x1,92 TB) is elérhető.

> [!NOTE]
> A Lsv2 sorozatú virtuális gépek közvetlenül a virtuális Géphez csatolt adatlemezek tartós használata helyett a csomóponton a helyi lemez használatára van optimalizálva. Ez lehetővé teszi a nagyobb IOPs és átviteli sebességet a számítási feladatokhoz. A Lsv2 sorozat nem támogatja a tartós adatlemezek által elérhető IOPS növelése érdekében a helyi gyorsítótárat a létrehozása. A magas teljesítmény és a helyi lemez IOPS teszi NoSQL-tárolókat, mint például az Apache Cassandra- és MongoDB replikálhatja adatait, így több virtuális gép folyamataik megőrzésére a meghiúsulása esetén egyetlen virtuális Gépet, amely ideális a Lsv2 sorozatú virtuális gépek.

## <a name="lsv2-series"></a>Lsv2 sorozat

ACU: 150-175

Prémium szintű Storage: Támogatott

Prémium szintű Storage gyorsítótárazást: Nem támogatott

| Méret          | vCPU | Memória (GiB) | Ideiglenes lemez<sup>1</sup> (GiB) | NVMe lemezeket<sup>2</sup> | NVMe lemezteljesítmény<sup>3</sup> (olvasási IOPS / MBps) | Maximális lemezteljesítmény adatok (IOPs vagy MBps)<sup>4</sup> | Adatlemezek maximális száma | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 400,000 / 2,000 | 8,000/160 | 16 | 2 / 3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 800,000 / 4,000 | 16,000/320 | 32 | 4 / 6,400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1,5 MILLIÓ / 8000    | 32,000/640 | 32 | 8 / 12,800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.9 M / 16 000   | 64,000/1,280 | 32 | 8 / 25,600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1.92TB   | 3.8 M / 20 000   | 80,000/1,400 | 32 | 8 / 32,000 |

<sup>1</sup> Lsv2 sorozatú virtuális gépek egy standard SCSI-alapú ideiglenes erőforrás lemez az operációs rendszer stránkování/felcserélés fájl használata (a Windows, Linux rendszeren /dev/sdb D:) rendelkezik. Ezt a lemezt biztosít a tároló 80 GB, 4 000 iops-t, és 80 MB/s átviteli sebesség a minden 8 Vcpu (pl. Standard_L80s_v2 biztosít 800 GiB 40 000 IOPS és 800 MB/s). Ez biztosítja, hogy az NVMe-meghajtókkal teljes dedikálhatja alkalmazás használatát. Ez a lemez elmúló, és minden adat el fog veszni a Leállítás/felszabadítás.

<sup>2</sup> helyi NVMe lemezeket a rövid élettartamú, az adatok a lemezeken lévő elvesznek, ha, állítsa le vagy szabadítsa fel a virtuális Gépet.

<sup>3</sup> Hyper-V NVMe közvetlen technológia leszabályozás férhet hozzá biztonságosan a Vendég virtuális gép terén leképezett helyi NVMe-meghajtókkal.  A maximális teljesítmény eléréséhez szükséges vagy a legújabb WS2019 build vagy Ubuntu 18.04, 16.04 használatával az Azure Marketplace-ről.  Írási teljesítmény i/o-mérete, a meghajtó terhelés és a tárolókapacitás kihasználtságát alapján változik.

<sup>4</sup> Lsv2-sorozat virtuális gépei nem biztosít gazdagép gyorsítótár adatlemez nem tudják igénybe a Lsv2 számítási feladatokhoz.  Azonban Lsv2 virtuális gépek az Azure virtuális gép rövid élettartamú operációsrendszer lemez lehetőséget (akár 30 GiB) képes kezelni.

## <a name="size-table-definitions"></a>Mérettábla definíciói

- A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
- A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
- Ha azt szeretné, a virtuális gépek számára a legjobb teljesítmény, a vCPU / 2 lemezt az adatlemezek száma korlátozza.
- **Várható hálózati sávszélesség** maximális összesített értéket jelenít meg [Virtuálisgép-típusonként kiosztott sávszélesség](../articles/virtual-network/virtual-machine-network-throughput.md) összes hálózati adapteren az összes célhelyre. A felső határértékek nem garantáltak, csak útmutatóul szolgálnak a kívánt alkalmazásra megfelelő VM-típus kiválasztásához. A tényleges hálózati teljesítmény számos tényezőtől függ, többek között a hálózat túlterhelésétől, az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól. A hálózati átviteli sebesség optimalizálásával kapcsolatos információkért lásd: [A hálózati átviteli sebesség optimalizálása Windows és Linux rendszeren](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Linux vagy Windows rendszeren a várt hálózati teljesítmény eléréséhez egy adott verzió kiválasztására vagy a virtuális gép optimalizálására lehet szükség. További információkért lásd: [Virtuális gépek átviteli sebességének megbízható tesztelése](../articles/virtual-network/virtual-network-bandwidth-testing.md).
