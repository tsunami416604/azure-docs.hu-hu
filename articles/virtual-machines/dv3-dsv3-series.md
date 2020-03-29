---
title: Dv3 és Dsv3 sorozatú – Azure virtuális gépek
description: A Dv3 és Dsv3 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164406"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 és DSv3 sorozat

A Dv3 sorozat az Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processzorokon fut, hiperszálas konfigurációban, ami jobb értéket biztosít a legtöbb általános cél számítási feladatok. A memória kibővült (~3,5 GiB/vCPU-ról 4 GiB/vCPU-ra), míg a lemez- és hálózati korlátokat magonként módosították, hogy igazodjanak a hyperthreading-ra való áttéréshez. A Dv3-sorozat már nem rendelkezik a D/Dv2 sorozat nagy memória virtuálisgép-méreteivel, azokat a memóriára optimalizált [Ev3 és Esv3 sorozatba](ev3-esv3-series.md)helyezték át.

Példa a D-sorozathasználati esetek közé tartoznak a nagyvállalati szintű alkalmazások, relációs adatbázisok, a memóriában belüli gyorsítótárazás és az analitika.

## <a name="dv3-series"></a>Dv3-sorozat

A Dv3 sorozatú méretek intel® Xeon® 8171M 2,1 GHz-es (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz(Broadwell) vagy Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processzorokon futnak Intel Turbo Boost Technology 2.0 processzorokkal. A Dv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához Dsv3-méreteket vegyen igénybe. A Dsv3-méretek díjszabása és számlázási mérőszámai megegyeznek a Dv3-sorozatéval.

A Dv3 sorozatú virtuális gépek Intel® hyper-Threading technológiával rendelkeznek.

ACU: 160–190

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális ideiglenes tárolási átviteli sebesség: IOPS/Olvasás i.BBps/Írás i.BBps | Maximális hálózati adapterek/hálózati sávszélesség |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-sorozat

A Dsv3 sorozatú méretek az Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processzorokon futnak Intel Turbo Boost Technology 2.0 technológiával, és prémium szintű tárhelyet használnak. A Dsv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

A Dsv3 sorozatú virtuális gépek Intel® hyper-Threading technológiával rendelkeznek.

ACU: 160–190

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott gyorsítótárazási és ideiglenes tárolási átviteli sebesség: IOPS/MBps (gyorsítótár mérete GiB-ben) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

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
