---
title: Eav4-sorozatú és Easv4-sorozatú – Azure virtuális gépek
description: Az Eav4 és Easv4 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: e05a2b97a25ce4cab7d619f4b62d405765cb08db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164389"
---
# <a name="eav4-and-easv4-series"></a>Eav4 és Easv4 sorozat

Az Eav4 sorozatú és Easv4-sorozatú az AMD 2,35 GHz-es EPYC<sup>TM</sup> 7452 processzorát használja többszálas konfigurációban, akár 256 MB L3 gyorsítótárral, növelve a legtöbb memóriaoptimalizált munkaterhelés futtatásának lehetőségeit. Az Eav4 és Easv4 sorozat ugyanolyan memória- és lemezkonfigurációval rendelkezik, mint az Ev3 & Esv3 sorozat.

## <a name="eav4-series"></a>Eav4-sorozat

ACU: 230 - 260

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

Az Eav4 sorozat méretei a 2,35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely képes elérni a 3,35 GHz-es maximális frekvenciát, és prémium SSD-t használ. Az Eav4 sorozatú méretek ideálisak a memóriaigényes nagyvállalati alkalmazásokhoz. Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. A prémium szintű SSD használatához használja az Easv4 sorozat méreteit. Az Easv4-méretek díjszabási és számlázási mérője megegyezik az Eav3-sorozatdíj-mérővel.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Maximális hálózati adapterek / várható hálózati sávszélesség (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| Szabványos\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| Szabványos\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| Szabványos\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Szabványos\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Szabványos\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| Szabványos\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| Szabványos\_E48a\_v4<sup>**</sup> |48|384|1200|32| | |
| Szabványos\_E64a\_v4<sup>**</sup> |64|512|1600|32| | |
| Szabványos\_E96a\_v4<sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>Ezek a méretek előzetes verzióban vannak. Ha érdekli kipróbálni ezeket a nagyobb méretű, iratkozzon fel a [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="easv4-series"></a>Easv4-sorozat

ACU: 230 - 260

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

Az Easv4 sorozat méretei a 2,35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely képes elérni a 3,35 GHz-es maximális frekvenciát, és prémium SSD-t használ. Az Easv4 sorozatú méretek ideálisak a memóriaigényes nagyvállalati alkalmazásokhoz.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Maximális hálózati adapterek / várható hálózati sávszélesség (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_E48as_v4<sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4<sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4<sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>Ezek a méretek előzetes verzióban vannak. Ha érdekli kipróbálni ezeket a nagyobb méretű, iratkozzon fel a [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

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
