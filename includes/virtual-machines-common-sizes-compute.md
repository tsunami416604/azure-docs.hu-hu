---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 5a4495dd675b662273715b5c13a5594adc87fceb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333813"
---
<!-- F-series, Fs-series* -->

A számítási optimalizált Virtuálisgép-méretek rendelkezik a magas CPU-memória arány és közepes adatforgalmú webkiszolgálók, hálózati berendezések, kötegfolyamatok és alkalmazáskiszolgálók számára hasznos. Ez a cikk ismerteti a vcpu-k, az adatlemezeket, és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélességet az ennél a csoportosításnál méreteire vonatkoztatva számát.

Fsv2 sorozat az Intel® Xeon® Platinum 8168 processzor alapul, egy tartós, amely az összes alapvető 3.4GHz turbó órajele és 3,7 GHz-es maximális egymagos turbo gyakorisága. Az Intel® AVX-512 utasításokat, amelyek új, méretezhető Intel processzorral, akár egy 2 X teljesítmény boost vektor-feldolgozási feladataikat az egyetlen és a dupla pontosságú lebegőpontos művelet a biztosít. Más szóval azok nagyon gyors számára bármilyen számítási feladatot. 

Listaáron egy alacsonyabb óránkénti Fsv2-sorozat képviseli a legjobb ár – teljesítmény arányt az Azure-portfólióban az az Azure számítási egységek (ACU) vCPU-alapú. 

Az F-sorozat a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, amelynek az órajele akár 3,1 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Ez ugyanakkora teljesítményt jelent, mint a Dv2-sorozat virtuális gépei esetében.  

Az F-sorozat virtuális gépei remek választásnak bizonyulnak az olyan számítási feladatokhoz, amelyekhez gyorsabb processzor szükséges, azonban kisebb a vCPU-nkénti memória- vagy ideiglenes tárterületigényük.  Az analitikai alkalmazások, játékkiszolgálók, webkiszolgálók vagy kötegelt feldolgozások számítási feladatai számára előnyös az F-sorozat használata.

Az Fs-sorozat az F-sorozat összes előnyét biztosítja a prémium szintű tárterület mellett.

## <a name="fsv2-series-sup1sup"></a>Fsv2 sorozat <sup>1</sup>

ACU: 195 - 210

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS / MBps (gyorsítótár mérete GiB-ban) | Nem gyorsítótárazott lemezek max. teljesítménye: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1,750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3,500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7,000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14,000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28,000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30,000              |


<sup>1</sup> Fsv2-sorozatú virtuális gépek Intel® Hiperszálkezelési technológiát funkció

<sup>2</sup> több mint 64 vCPU esetében a támogatott vendég operációs rendszerek egyikét: A Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 és Red Hat Enterprise Linux, CentOS 7.3 vagy Oracle Linux 7.3 + lis 4.2.1

<sup>3</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.

## <a name="fs-series-sup1sup"></a>FS sorozatú <sup>1</sup>

ACU: 210 - 250

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS / MBps (gyorsítótár mérete GiB-ban) | Nem gyorsítótárazott lemezek max. teljesítménye: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4,000 / 32 (12) |3,200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8,000 / 64 (24) |6,400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16,000 / 128 (48) |12,800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32,000 / 256 (96) |25,600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64,000 / 512 (192) |51,200 / 768 |8 / 12000 |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

<sup>1</sup> a maximális lemezteljesítményét (IOPS vagy MBps) használhatóságát egy Fs sorozatú virtuális gépek előfordulhat, hogy a a száma, mérete és szétosztottsága befolyásolhatja a csatolt lemezek mennyisége.  További információkért lásd: [magas teljesítmény-központú tervezés](../articles/virtual-machines/windows/premium-storage-performance.md).


<br>

## <a name="f-series"></a>F-sorozat

ACU: 210 - 250

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / olvasási MBps / írási MBps | Adatlemezek max. száma / átviteli sebesség: IO | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |


<br>


