---
title: Dv2 és Dsv2 sorozatú – Azure virtuális gépek
description: A Dv2 és Dsv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164423"
---
# <a name="dv2-and-dsv2-series"></a>Dv2 és DSv2 sorozat

A Dv2 és Dsv2 sorozat, amely az eredeti D sorozat folytatása, nagyobb teljesítményű CPU-val és optimális CPU-to-memory konfigurációval rendelkezik, így a legtöbb éles számítási feladathoz alkalmas. A Dv2-sorozat körülbelül 35%-kal gyorsabb, mint a D-sorozat. A Dv2 sorozat az Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processzorokon fut az Intel Turbo Boost Technology 2.0 technológiával. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

## <a name="dv2-series"></a>Dv2-sorozat

Az Intel® Xeon® 8171M 2.1GHz (Skylake) vagy az Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processzorokon futnak Intel Turbo Boost Technology 2.0 vagy Intel Turbo Boost Technology 2.0 processzorokkal.

ACU: 210–250

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális ideiglenes tárolási átviteli sebesség: IOPS/Olvasás i.BBps/Írás i.BBps | Adatlemezek max. száma | Átviteli: IOPS | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4x500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>DSv2-sorozat

A DSv2 sorozatú méretek az Intel® Xeon® a 8171M 2.1GHz (Skylake) vagy az Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processzorokon futnak Intel Turbo Boost Technology 2.0 és prémium szintű tárolóval.

ACU: 210–250

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott gyorsítótárazási és ideiglenes tárolási átviteli sebesség: IOPS/MBps (gyorsítótár mérete GiB-ben) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
