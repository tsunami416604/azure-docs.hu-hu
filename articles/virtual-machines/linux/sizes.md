---
title: Linux rendszerű virtuális gépek méretei az Azure-ban
description: Felsorolja az Azure-beli Linux rendszerű virtuális gépekhez elérhető különböző méreteket.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: jonbeck
ms.openlocfilehash: 5e10c0905237f3cc28dd0ac113dfaf844e781bac
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646659"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>A Linux rendszerű virtuális gépek méretei az Azure-ban
Ez a cikk a Linux-alkalmazások és-munkaterhelések futtatására használható Azure-beli virtuális gépek elérhető méreteit és lehetőségeit ismerteti. Emellett üzembe helyezési megfontolásokat is biztosít, hogy a rendszer mikor tervezi ezeket az erőforrásokat. Ez a cikk a Windows rendszerű [virtuális gépekhez](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)is elérhető.


| Type (Típus)                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](sizes-general.md)          | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC  | Kiegyensúlyozott processzor-memória arány. Ideális választás tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, valamint kis és közepes adatforgalmú webkiszolgálókhoz. |
| [Számításra optimalizált](sizes-compute.md)        | Fsv2           | Magas processzor-memória arány. Alkalmas közepes adatforgalmú webkiszolgálók, hálózati berendezések, kötegfolyamatok és alkalmazáskiszolgálók számára.        |
| [Memóriaoptimalizált](sizes-memory.md)         | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2  | Magas memória-CPU arány. Ideális választás relációs adatbázis-kiszolgálókhoz, közepes és nagy gyorsítótárakhoz és memóriabeli elemzésekhez.                 |
| [Tárolásra optimalizált](sizes-storage.md)        | Lsv2                | A nagy adatátviteli sebesség és az i/o ideális megoldás a Big Database, az SQL, a NoSQL adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok számára.  |
| [GPU](sizes-gpu.md)            | NC, NCv2, NCv3, ND, NDv2 (előzetes verzió), NV, NVv3  | A speciális virtuális gépek nagy grafikai renderelést és videoszerkesztőt céloznak meg, valamint a képzési és következtetési (ND) modellek mély tanulással. Egyetlen vagy több GPU-val is elérhető.       |
| [Nagy teljesítményű számítás](sizes-hpc.md) | HB, HC, H | A leggyorsabb és leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű (távoli közvetlen memória-hozzáférést lehetővé tevő) hálózati adapterrel. |

<br>

- További információ a különböző méretek díjszabásáról: [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Az Azure-régiókban található virtuálisgép-méretek elérhetőségét lásd: [régiók által elérhető termékek](https://azure.microsoft.com/regions/services/).
- Az Azure-beli virtuális gépekre vonatkozó általános korlátokat lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.


## <a name="rest-api"></a>REST API

A virtuális gépek méretének lekérdezéséhez REST API használatáról a következő témakörben tájékozódhat:

- [Az átméretezéshez rendelkezésre álló virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Az előfizetéshez elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Rendelkezésre álló virtuálisgép-méretek listázása rendelkezésre állási csoportokban](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

További információ a Linux rendszerű virtuális gépek számítási teljesítményéről a [Megjegyzések összehasonlítási pontjainak](compute-benchmark-scores.md)használatával.

## <a name="next-steps"></a>Következő lépések

További információ a rendelkezésre álló különböző virtuálisgép-méretekről:
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- A standard, a Dv1 (D1-4 és D11-14 v1), valamint az A8-A11 sorozat [előző generációs](sizes-previous-gen.md) oldalának keresése



