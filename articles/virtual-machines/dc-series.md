---
title: DC sorozat – Azure Virtual Machines
description: A DC sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8f36220ab81c0fd9533bf2f025b801b37823da5f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493812"
---
# <a name="dc-series"></a>DC sorozat

A [DC sorozat](#dc-series) az Azure-beli virtuális gépek egyik családja, amely a nyilvános felhőben feldolgozott adatok és kódok titkosságának és integritásának védelmét segíti. Ezeket a gépeket a 3,7 GHz-es Intel XEON E-2176G processzor SGX ENKLÁVÉHOZ technológiával támogatja. Az Intel Turbo Boost technológiával ezek a gépek akár 4,7 GHz-re is felléphetnek. A DC sorozat példányai lehetővé teszik, hogy az ügyfelek biztonságos enklávé-alapú alkalmazásokat hozzanak létre a kódok és az adataik használat közbeni védelméhez.

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DC2s | 2 | 8  | 100 | 2 | 4000 / 32 (43) | 3200 /48 | 2 / 1500 |
| Standard_DC4s | 4 | 16 | 200 | 4 | 8000 / 64 (86) | 6400 /96 | 2 / 3000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.