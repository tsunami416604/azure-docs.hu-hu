---
title: Mv2 sorozat - Azure virtuális gépek
description: Az Mv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6fb0f93d3ac124b21bbc52ddc57bc720de6406e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163896"
---
# <a name="mv2-series"></a>Mv2 sorozat

Az Mv2 sorozat nagy átviteli sebességű, alacsony késleltetésű platformja egy hiperszálas Intel® Xeon® Platinum 8180M 2.5GHz (Skylake) processzoron fut, amelynek minden magalapfrekvenciája 2,5 GHz és a maximális turbó frekvenciája 3,8 GHz. Az Mv2 sorozatú virtuálisgépek összes mérete szabványos és prémium szintű állandó lemezeket is használhat. Az Mv2 sorozatú példányok memóriaoptimalizált virtuálisgép-méretek, amelyek páratlan számítási teljesítményt nyújtanak a nagy memóriabeli adatbázisok és munkaterhelések támogatásához, magas memória-és CPU-aránymellett, amely ideális a relációs adatbázis-kiszolgálókhoz, a nagy gyorsítótárakhoz és a memórián belüli Analytics.

Mv2-sorozat VM funkció Intel® Hyper-Threading technológia

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

Írási gyorssegéd: [Támogatott](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Max hálózati adapterek / várható hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1.</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| <sup>1.</sup> Standard_M208s_v2 | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2 sorozatú virtuális gépek csak generációs 2. Ha Linuxot használ, olvassa el [a 2.](./linux/generation-2.md)

<sup>2</sup> A M416ms_v2 és M416s_v2 méretek esetén vegye figyelembe, hogy a következő lemezkép csak a következő: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 SAP-alkalmazásokhoz."

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
