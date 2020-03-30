---
title: DC-sorozat - Azure virtuális gépek
description: A DC sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 7834c8a32d4d85fc354bac209e13f19f3b8315fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256929"
---
# <a name="preview-dcsv2-series"></a>Előzetes: DCsv2 sorozat


A DCsv2 sorozat segít megvédeni az adatok és a kód titkosságát és integritását, miközben a nyilvános felhőben van feldolgozva. Ezeket a gépeket az Intel XEON E-2288G processzor legújabb generációja támogatja SGX technológiával. Az Intel Turbo Boost technológiával ezek a gépek akár 5,0 GHz-re is felmehetnek. A DCsv2 sorozatpéldányok lehetővé teszik az ügyfelek számára, hogy biztonságos enklávé alapú alkalmazásokat építsenek a kódjuk és az adataik védelme érdekében, miközben használatban vannak.

A használati esetek közé tartozik például a bizalmas többrésztvevős adatmegosztás, a csalások felderítése, a pénzmosás elleni küzdelem, a blokklánc, a bizalmas használatelemzés, a hírszerzési elemzés és a bizalmas gépi tanulás.

Prémium szintű tárhely: Támogatott*

Prémium szintű tárolási gyorsítótárazás: Támogatott*

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

*Kivéve Standard_DC8_v2



| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Maximális hálózati adapterek / várható hálózati sávszélesség (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- A DCsv2 sorozatú virtuális gépek 2 `Gen2` generációs virtuális gépek, és csak a lemezképeket [támogatják.](./linux/generation-2.md#creating-a-generation-2-vm)
- Jelenleg csak az Egyesült Királyságdéli és Kanada Középső Részén érhető el.
- A bizalmas számítási virtuális gépek előző generációja: [DC sorozat](sizes-previous-gen.md)
- DCsv2 virtuális gépek létrehozása az Azure Portal [létrehozása virtuális gép használatával - Portál](./linux/quick-create-portal.md)



## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
