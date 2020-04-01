---
title: Dav4 és Dasv4 sorozatú – Azure virtuális gépek
description: A Dav4 és A Dasv4 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 56e86aa75b153b5cb005c96fca45373d30ffa8b4
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437096"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 és Dasv4 sorozat

A Dav4 sorozat ú Dasv4-es sorozat új méretű, az AMD 2,35 GHz-es EPYC<sup>TM</sup> 7452 processzorát felhasználva többszálas konfigurációban, akár 256 MB L3 gyorsítótárral, amely 8 GB-ot szentel az L3 gyorsítótárból minden 8 magnak, növelve az ügyféllehetőségeket az általános célú munkaterhelések futtatásához. A Dav4 és Dasv4 sorozat ugyanolyan memória- és lemezkonfigurációval rendelkezik, mint a D & Dsv3 sorozat.

## <a name="dav4-series"></a>Dav4-sorozat

ACU: 230-260

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

Dav4-sorozat méretei alapján a 2.35Ghz AMD EPYC<sup>TM</sup> 7452 processzor, amely képes elérni a megnövelt maximális frekvenciája 3.35GHz. A Dav4 sorozatú méretek vCPU, memória és ideiglenes tárolás kombinációját kínálják a legtöbb éles számítási feladathoz. Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. A prémium szintű SSD használatához használja a Dasv4 méreteket. A Dasv4-méretek díjszabási és számlázási mérője megegyezik a Dav4 sorozatdíjával.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Maximális hálózati adapterek / várható hálózati sávszélesség (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000 / 1000 / 500 | 8 / 24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000 / 1000 / 500 | 8 / 30000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000 / 1000 / 500 | 8 / 30000 |

## <a name="dasv4-series"></a>Dasv4-sorozat

ACU: 230-260

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

A Dasv4 sorozat méretei a 2,35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely 3,35 GHz-es maximális frekvenciát képes elérni, és prémium SSD-t használ. A Dasv4 sorozat méretei vCPU, memória és ideiglenes tárolás kombinációját kínálják a legtöbb éles számítási feladathoz.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Maximális hálózati adapterek / várható hálózati sávszélesség (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_D48as_v4|48|192|384|32|96000 / 1020 (1200)|76800 / 1148|8 / 24000 |
| Standard_D64as_v4|64|256|512|32|128000 / 1020 (1600)|80000 / 1200|8 / 30000 | 
| Standard_D96as_v4|96|384|768|32|192000 / 1020 (2400)|80000 / 1200|8 / 30000 |

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
