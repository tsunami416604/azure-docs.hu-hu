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
ms.openlocfilehash: 16a0fa29c067262f1794528b16abfce662d05605
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748980"
---
<!-- F-series, Fs-series* -->

A számítási optimalizált virtuálisgép-méretek magas CPU-memória aránnyal rendelkeznek. Ezek a méretek kiválóan alkalmasak a közepes forgalmú webkiszolgálók, a hálózati berendezések, a kötegelt folyamatok és az alkalmazások kiszolgálói számára. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számáról nyújt információt. Emellett a tárolási teljesítményre és a hálózati sávszélességre vonatkozó információkat is tartalmaz ebben a csoportosításban.

A Fsv2 sorozat az Intel® Xeon® Platinum 8168 processzoron alapul. A szolgáltatás egy tartós, 3,4 GHz-es, a Turbo órajelét és a 3,7 GHz-es max. Intel® AVX-512 utasítások az Intel skálázható processzorok újdonságai. Ezek az utasítások akár kétszeres teljesítmény-növekedést is biztosítanak a vektoros feldolgozási feladatokhoz egy-és dupla pontosságú lebegőpontos műveletekben. Más szóval a számítási feladatokhoz nagyon gyorsak.

A Fsv2 sorozat a legjobb ár-teljesítmény az Azure-portfólióban, amely az Azure-beli számítási egység (ACU) alapján vCPU.

## <a name="fsv2-series-sup1sup"></a>Fsv2 – <sup>1</sup> . sorozat

ACU: 195 – 210

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31 (32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63 (64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000/127 (128)        | 12800/190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255 (256)        | 25600/380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512 (512)        | 51200/750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 (768)        | 76800/1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000/1024 (1024)     | 80000/1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000/1152 (1520)     | 80000/1100             | 8 / 30000              |

<sup>1</sup> a Fsv2 sorozatú virtuális gépek Intel® Hyper-Threading technológiával rendelkeznek.

<sup>2</sup> a 64-nál több vCPU használata a következő támogatott vendég operációs rendszerek egyikét igényli:
- Windows Server 2016 vagy újabb
- Ubuntu 16,04 LTS vagy újabb, az Azure-ban hangolt kernel (4,15 kernel vagy újabb)
- SLES 12 SP2 vagy újabb
- RHEL vagy CentOS 6,7-es verzió – 6,10, a Microsoft által biztosított LIS-csomag 4.3.1-es (vagy újabb) verziójával
- RHEL vagy CentOS 7,3-es verzió, a Microsoft által biztosított LIS csomag 4.2.1 (vagy újabb) telepítésével
- RHEL vagy CentOS 7,6-es vagy újabb verzió
- Oracle Linux UEK4 vagy újabb verzióval
- Debian 9 a backports rendszermaggal, Debian 10 vagy újabb verzióval
- CoreOS 4,14 kernelsel vagy újabb verzióval

<sup>3</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.
