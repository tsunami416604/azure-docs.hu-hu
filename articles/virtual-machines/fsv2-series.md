---
title: Fsv2 sorozat - Azure virtuális gépek
description: Az Fsv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164848"
---
# <a name="fsv2-series"></a>Fsv2 sorozat

Az Fsv2 sorozat az Intel® Xeon® Platinum 8168 processzoron alapul. Ez jellegét meghatározza egy tartós minden magtok Turbo órajel -ból 3.4 GHz és egy legfelső határ egymagos turbó frekvencia -ból 3.7 GHz. Intel® AVX-512 utasítások újak az Intel skálázható processzorok. Ezek az utasítások akár kétszeres teljesítménynövekedést biztosítanak a vektoros feldolgozási számítási feladatokhoz mind az egy- és kétszeres pontosságú lebegőpontos műveleteken. Más szóval, ezek nagyon gyors a számítási számítási feladatokhoz.

Az Fsv2 sorozatú virtuális gépek Intel® Hyper-Threading technológiával rendelkeznek.

ACU: 195 - 210

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU-k | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott gyorsítótárazási és ideiglenes tárolási átviteli sebesség: IOPS/MBps (gyorsítótár mérete GiB-ben) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> A 64-nél több vCPU használatához a támogatott vendég operációs rendszerek egyikére van szükség:

- Windows Server 2016 vagy újabb
- Ubuntu 16.04 LTS vagy újabb, Azure hangolt kernellel (4.15 kernel vagy újabb)
- SLES 12 SP2 vagy újabb
- RHEL vagy CentOS 6.7-től 6.10-ig, a Microsoft által biztosított 4.3.1-es (vagy újabb) LIS-csomaggal telepítve
- RHEL vagy CentOS 7.3-as verzió, a Microsoft által biztosított 4.2.1-es (vagy újabb) LIS-csomaggal telepítve
- RHEL vagy CentOS 7.6-os vagy újabb verzió
- Oracle Linux UEK4 vagy újabb verzióval
- Debian 9 a backport kernellel, Debian 10 vagy újabb
- CoreOS 4.14-es vagy újabb kernellel

<sup>2</sup> Példány egyetlen ügyfélnek dedikált hardverhez van elkülönítve.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
