---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 21b982389b186e949b21352f4b11bd6b4aa06dcb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279204"
---
<!-- F-series, Fs-series* -->

A számítási optimalizált virtuálisgép-méretek magas CPU-memória aránnyal rendelkeznek. Ezek a méretek kiválóan alkalmasak a közepes forgalmú webkiszolgálók, a hálózati berendezések, a kötegelt folyamatok és az alkalmazások kiszolgálói számára. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számáról nyújt információt. Emellett a tárolási teljesítményre és a hálózati sávszélességre vonatkozó információkat is tartalmaz ebben a csoportosításban.

A Fsv2 sorozat az Intel® Xeon® Platinum 8168 processzoron alapul. A szolgáltatás egy tartós, 3,4 GHz-es, a Turbo órajelét és a 3,7 GHz-es max. Intel® AVX-512 utasítások az Intel skálázható processzorok újdonságai. Ezek az utasítások akár kétszeres teljesítmény-növekedést is biztosítanak a vektoros feldolgozási feladatokhoz egy-és dupla pontosságú lebegőpontos műveletekben. Más szóval a számítási feladatokhoz nagyon gyorsak.

A Fsv2 sorozat a legjobb ár-teljesítmény az Azure-portfólióban, amely az Azure-beli számítási egység (ACU) alapján vCPU.

## <a name="fsv2-series-sup1sup"></a>Fsv2 – <sup>1</sup> . sorozat

ACU: 195 – 210

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63 (64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000 / 768 (768)        | 76800 / 1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |

<sup>1</sup> a Fsv2 sorozatú virtuális gépek Intel® Hyper-Threading technológiával rendelkeznek.

<sup>2</sup> a több mint 64 vCPU használata a következő támogatott vendég operációs rendszerek egyikét igényli: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2, valamint Red Hat Enterprise Linux, CentOS 7,3 vagy Oracle Linux 7,3, lis 4.2.1.

<sup>3</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.
