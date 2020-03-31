---
title: Ev3- és Esv3-sorozat - Azure virtuális gépek
description: Az Ev3 és Esv3 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164372"
---
# <a name="ev3-and-esv3-series"></a>Ev3 és Esv3 sorozat

Az Ev3 és Esv3 sorozatú Intel® Xeon® 8171M 2,1 GHz (Skylake) vagy az Intel® Xeon® E5-2673 v4 2.3 GHz-es (Broadwell) processzor hiperszálas konfigurációban, jobb ár-érték arányt biztosítva a legtöbb általános célú munkaterheléshez, és az Ev3-at a legtöbb más felhő általános célú virtuális gépeivel való igazodásba.  A memória bővült (7 GiB/vCPU-ról 8 GiB/vCPU-ra), míg a lemez- és hálózati korlátokat magonként módosították, hogy igazodjanak a hyperthreading-ra való áttéréshez. Az Ev3 a D/Dv2 családok nagy memóriában lévő virtuálisgép-méreteinek nyomon követése.

## <a name="ev3-series"></a>Ev3-sorozat

Az Ev3 sorozatú példányok az Intel® Xeon® 8171M 2.1 GHz (Skylake) vagy az Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) processzor és az Intel Turbo Boost Technology 2.0 szolgáltatáson alapulnak. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához ESv3-méreteket vegyen igénybe. Az Esv3-méretek díjszabása és számlázási mérőszámai megegyeznek az Ev3-sorozatéval.

Ev3-sorozat VM funkció Intel® Hyper-Threading technology.

ACU: 160–190

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> Korlátozott magméretek állnak rendelkezésre.

<sup>2</sup> Példány egyetlen ügyfélnek dedikált hardverhez van elkülönítve.

## <a name="esv3-series"></a>Esv3-sorozat

Az Esv3 sorozatú példányok az Intel® Xeon® a 8171M 2.1 GHz (Skylake) vagy az Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) processzor, az Intel Turbo Boost Technology 2.0 és a prémium szintű tárhely használatán alapulnak. Az Esv3 sorozatú példányok ideálisak a memóriaigényes nagyvállalati alkalmazásokhoz.

Esv3-sorozat VM funkció Intel® Hyper-Threading technology.

ACU: 160–190

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott gyorsítótárazási és ideiglenes tárolási átviteli sebesség: IOPS/MBps (gyorsítótár mérete GiB-ben) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| <sup>1.</sup> Standard_E4s_v3  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| <sup>Standard_E8s_v3 1.</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1.</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1.</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1.</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1.</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| <sup>Standard_E64is_v3 2.</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup> Korlátozott magméretek állnak rendelkezésre.

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
