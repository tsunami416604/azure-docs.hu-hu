---
title: Edv4 és Edsv4 sorozat – Azure Virtual Machines
description: A Ev4, a Edv4, a Esv4 és a Edsv4 sorozatú virtuális gépek specifikációi.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: d6f70e00bc33430cba13895b6d0c9a0aac5ef246
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84338685"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 és Edsv4 sorozat

A Edv4 és a Edsv4 sorozat az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) processzorokon fut egy Hyper-threaded konfigurációban, és ideális a különböző, nagy mennyiségű Nagyvállalati alkalmazások számára, és akár 504 GIB RAM, az [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), az [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) és az [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Ezek az új virtuálisgép-méretek 50%-kal nagyobb helyi tárterülettel rendelkeznek, valamint jobb helyi lemez-IOPS is használhatók a [Ev3-/Esv3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) -méretekhez és a Gen2-alapú virtuális [gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) képest.

## <a name="edv4-series"></a>Edv4 sorozat

A Edv4-sorozat méretei az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade-tó) processzorokon futnak. A Edv4 virtuálisgép-méretek akár 504 GiB RAM-mal is rendelkeznek, a gyors és nagyméretű helyi SSD-tárolók mellett (legfeljebb 2 400 GiB). Ezek a virtuális gépek ideálisak a nagy teljesítményű, kis késésű, nagy sebességű helyi tárterületet használó, nagy teljesítményű vállalati alkalmazásokhoz és alkalmazásokhoz. A standard SSD-ket és a standard HDD lemezes tárolást a Edv4 virtuális gépekhez is csatlakoztathatja. 

ACU: 195 – 210

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Élő áttelepítés: támogatott

Memória megőrzésének frissítései: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8/10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8/30000 |


## <a name="edsv4-series"></a>Edsv4 sorozat

A Edsv4-sorozat méretei az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade-tó) processzorokon futnak. A Edsv4 virtuálisgép-méretek akár 504 GiB RAM-mal is rendelkeznek, a gyors és nagyméretű helyi SSD-tárolók mellett (legfeljebb 2 400 GiB). Ezek a virtuális gépek ideálisak a nagy teljesítményű, kis késésű, nagy sebességű helyi tárterületet használó, nagy teljesítményű vállalati alkalmazásokhoz és alkalmazásokhoz.

ACU: 195-210

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Élő áttelepítés: támogatott

Memória megőrzésének frissítései: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120 (50) | 3200/48 | 2/1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242 (100) | 6400/96 | 2/2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485 (200) | 12800/192 | 4/4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968 (400) | 25600/384 | 8/8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211 (500)  | 32000/480  | 8/10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936 (800) | 51200/768  | 8/16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8/24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup> [korlátozott méretű alapméret érhető el)](https://docs.microsoft.com/azure/virtual-machines/windows/constrained-vcpu).


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
