---
title: DV4 és Dsv4 sorozat – Azure Virtual Machines
description: A DV4 és a Dsv4 sorozatú virtuális gépek specifikációi.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: df78d11ce19c1d1fbbb04d2cd1718be74401cd83
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182156"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 és Dsv4 sorozat

A DV4 és a Dsv4 sorozat az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) processzorokon fut egy Hyper-threaded konfigurációban, és jobb ár-arányt biztosít a legtöbb általános célú munkaterheléshez. A szolgáltatás a teljes Turbo órajel 3,4 GHz-es folyamatos elérését is tartalmazza. 

> [!NOTE]
> A gyakori kérdésekért tekintse meg a  [helyi Temp Disk nélküli Azure](azure-vms-no-temp-disk.md)-beli virtuálisgép-méreteket.
## <a name="dv4-series"></a>DV4 sorozat

A DV4-sorozat méretei az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade-tó) futnak. A DV4-sorozat méretei a vCPU, a memória és a távoli tárolási lehetőségek kombinációját nyújtják a legtöbb éles számítási feladathoz. Az DV4 sorozatú virtuális gépek [Intel &reg; Hyper-Threading technológiával](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)rendelkeznek.

A távoli adatlemezek tárterületét a rendszer külön számlázza a virtuális gépektől. A Premium Storage-lemezek használatához használja a Dsv4-méreteket. A Dsv4-méretek díjszabása és számlázási mérőszámai azonosak a DV4 sorozattal.

ACU: 195-210

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Élő áttelepítés: támogatott

Memória megőrzésének frissítései: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Csak a távtároló | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Csak a távtároló | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Csak a távtároló | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Csak a távtároló | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Csak a távtároló | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Csak a távtároló | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Csak a távtároló | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4 sorozat

A Dsv4-sorozat méretei az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade-tó) futnak. A DV4-sorozat méretei a vCPU, a memória és a távoli tárolási lehetőségek kombinációját nyújtják a legtöbb éles számítási feladathoz. Az Dsv4 sorozatú virtuális gépek [Intel &reg; Hyper-Threading technológiával](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)rendelkeznek. A távoli adatlemezek tárterületét a rendszer külön számlázza a virtuális gépektől.

ACU: 195-210

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Élő áttelepítés: támogatott

Memória megőrzésének frissítései: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Csak a távtároló | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Csak a távtároló | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Csak a távtároló | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Csak a távtároló | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Csak a távtároló | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Csak a távtároló | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Csak a távtároló | 32 | 80000/1200 | 8|30000 |
