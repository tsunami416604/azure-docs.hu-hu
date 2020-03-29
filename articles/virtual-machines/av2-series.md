---
title: Av2-sorozat - Azure virtuális gépek
description: Az Av2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: fe27a1cc78d1f37d535f364c03803a5196090a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163947"
---
# <a name="av2-series"></a>Av2-sorozat

Az Av2 sorozatú virtuális gépek számos hardvertípusra és processzorra telepíthetők. Az Av2 sorozatú virtuális gépek cpu-teljesítmény- és memóriakonfigurációval rendelkeznek, amelyek a legalkalmasabbak a belépő szintű számítási feladatokhoz, például a fejlesztéshez és a teszteléshez. A méret szabályozása egyenletes processzorteljesítményt biztosít a futó példány számára, függetlenül attól, hogy milyen hardveren van telepítve. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről. Néhány példa a fejlesztési és tesztelési kiszolgálók, az alacsony forgalmú webkiszolgálók, a kis és közepes adatbázisok, a fogalomigazolások és a kódtárolók.

ACU: 100

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális ideiglenes tárolási átviteli sebesség: IOPS/Olvasás i.BBps/Írás i.BBps | Maximális adatlemezek/átviteli sebesség: IOPS | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2/250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

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
