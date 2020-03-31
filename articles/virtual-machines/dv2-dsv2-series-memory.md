---
title: Memóriaoptimalizált Dv2 és DSv2 sorozatú virtuális gépek – Azure virtuális gépek
description: A Dv2 és DSv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 66e415070e60676df5602078aff50c7b68920f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914041"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Memória optimalizált Dv2 és Dsv2 sorozatú sorozat

A Dv2 és Dsv2 sorozat, amely az eredeti D-sorozat folytatása, erősebb CPU-val rendelkezik. A DSv2 sorozatú méretek az Intel® Xeon® a 8171M 2,1 GHz(Skylake) vagy az Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2,4 GHz-es (Haswell) processzorokon futnak. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

## <a name="dv2-series-11-15"></a>Dv2 sorozat ú 11-15

A Dv2 sorozatú méretek az Intel® Xeon® 8171M 2,1 GHz(Skylake) vagy az Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processzorokon futnak.

ACU: 210–250

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális ideiglenes tárolási átviteli sebesség: IOPS/Olvasás i.BBps/Írás i.BBps | Maximális adatlemezek/átviteli sebesség: IOPS | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/12000 |
| <sup>Standard_D15_v2 1.</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> Példány egyetlen ügyfélnek dedikált hardverhez van elkülönítve.
<sup>2</sup> 25000 Mbps gyorsított hálózatkezeléssel.

## <a name="dsv2-series-11-15"></a>DSv2 sorozatú 11-15

A DSv2 sorozatú méretek az Intel® Xeon® a 8171M 2,1 GHz(Skylake) vagy az Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2,4 GHz-es (Haswell) processzorokon futnak.

ACU: 210 - 250 <sup>1</sup>

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott gyorsítótárazási és ideiglenes tárolási átviteli sebesség: IOPS/MBps (gyorsítótár mérete GiB-ben) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| <sup>Standard_DS11_v2 3.</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2/1500  |
| Standard_DS12_v2 <sup>3.</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4/3000  |
| 3. Standard_DS13_v2 <sup>3.</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8/6000  |
| Standard_DS14_v2 <sup>3.</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8/12000 |
| <sup>Standard_DS15_v2 2.</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> A DSv2 sorozatú virtuális gépeken lehetséges maximális lemezátviteli sebességet (IOPS vagy Mb/s) korlátozhatja a csatlakoztatott lemez(ek) száma, mérete és csíkozása.  További információt a [Tervezés nagy teljesítményhez](./windows/premium-storage-performance.md)című témakörben talál.
<sup>2</sup> Példány van elválasztva az Intel Haswell alapú hardver és szentelt egyetlen ügyfél.  
<sup>3</sup> Korlátozott magméretek állnak rendelkezésre.  
<sup>4</sup> 25000 Mbps gyorsított hálózatkezeléssel.

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
