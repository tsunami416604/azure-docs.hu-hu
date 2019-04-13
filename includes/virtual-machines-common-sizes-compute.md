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
ms.openlocfilehash: cd06326b22b227490798b2b89c0439940cb4575f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551474"
---
<!-- F-series, Fs-series* -->

A számítási optimalizált Virtuálisgép-méretek rendelkezik a magas CPU-memória arány és közepes adatforgalmú webkiszolgálók, hálózati berendezések, kötegfolyamatok és alkalmazáskiszolgálók számára hasznos. Ez a cikk ismerteti a vcpu-k, az adatlemezeket, és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélességet az ennél a csoportosításnál méreteire vonatkoztatva számát.

Fsv2 sorozat az Intel® Xeon® Platinum 8168 processzor alapul, egy tartós, amely az összes alapvető 3.4GHz turbó órajele és 3,7 GHz-es maximális egymagos turbo gyakorisága. Az Intel® AVX-512 utasításokat, amelyek új, méretezhető Intel processzorral, akár egy 2 X teljesítmény boost vektor-feldolgozási feladataikat az egyetlen és a dupla pontosságú lebegőpontos művelet a biztosít. Más szóval azok nagyon gyors számára bármilyen számítási feladatot. 

Listaáron egy alacsonyabb óránkénti Fsv2-sorozat képviseli a legjobb ár – teljesítmény arányt az Azure-portfólióban az az Azure számítási egységek (ACU) vCPU-alapú.

## <a name="fsv2-series-sup1sup"></a>Fsv2 sorozat <sup>1</sup>

ACU: 195 - 210

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
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