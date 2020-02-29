---
title: Windows rendszerű virtuális gépek mérete az Azure-ban
description: Felsorolja az Azure-beli Windows rendszerű virtuális gépekhez elérhető különböző méreteket.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 398575cc5c3dea96aa644533eb6ce8a262d1981c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164491"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>A Windows rendszerű virtuális gépek méretei az Azure-ban

Ez a cikk a Windows-alkalmazások és-munkaterhelések futtatására használható Azure-beli virtuális gépek rendelkezésre álló méreteit és lehetőségeit ismerteti. Emellett üzembe helyezési megfontolásokat is biztosít, hogy a rendszer mikor tervezi ezeket az erőforrásokat.  Ez a cikk Linux rendszerű [virtuális gépekhez](../linux/sizes.md)is elérhető.

| Típus | Méretek | Leírás |
|------|-------|-------------|
| [Általános célú](../sizes-general.md) | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2 | Kiegyensúlyozott processzor-memória arány. Ideális teszteléshez és fejlesztéshez, kis-és közepes méretű adatbázisokhoz, valamint alacsony és közepes forgalmú webkiszolgálókhoz. |
| [Számításra optimalizált](../sizes-compute.md) | Fsv2 | Magas processzor-memória arány. Jó a közepes forgalmú webkiszolgálók, a hálózati berendezések, a kötegelt folyamatok és az alkalmazáskiszolgáló számára. |
| [Memóriaoptimalizált](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Magas memória-CPU arány. Kiválóan használható a kapcsolódó adatbázis-kiszolgálók, a közepes és nagy méretű gyorsítótárak, valamint a memórián belüli elemzések számára. |
| [Tárolásra optimalizált](../sizes-storage.md)  | Lsv2 | A nagy adatátviteli sebesség és az i/o ideális megoldás a Big Database, az SQL, a NoSQL adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok számára.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (előzetes verzió), NV, NVv3, NVv4 | A speciális virtuális gépek nagy grafikai renderelést és videoszerkesztőt céloznak meg, valamint a képzési és következtetési (ND) modellek mély tanulással. Egyetlen vagy több GPU-val is elérhető. |
| [Nagy teljesítményű számítás](../sizes-hpc.md) | HB, HBv2, HC, H | A leggyorsabb és leghatékonyabb CPU-alapú virtuális gépek, amelyek opcionális nagy átviteli sebességű hálózati adapterrel (RDMA) rendelkeznek. |

- További információ a különböző méretek díjszabásáról: [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
- Az Azure-beli virtuális gépekre vonatkozó általános korlátokat lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-subscription-service-limits.md).
- A tárolási díjak számítása ettől külön történik a tárfiókban használt oldalak mennyisége alapján. Részletekért lásd az [Azure Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).
- További információ arról, hogy az [Azure számítási egységei (ACU)](../acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

## <a name="rest-api"></a>REST API

A virtuális gépek méretének lekérdezéséhez REST API használatáról a következő témakörben tájékozódhat:

- [Az átméretezéshez rendelkezésre álló virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Az előfizetéshez elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Rendelkezésre álló virtuálisgép-méretek listázása rendelkezésre állási csoportokban](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

További információ arról, hogy az [Azure számítási egységei (ACU)](../acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

További információ a Windows rendszerű virtuális gépek számítási teljesítményéről a [megjegyzésekkel kapcsolatos teljesítménytesztek pontszámai](compute-benchmark-scores.md)alapján.

## <a name="next-steps"></a>Következő lépések

További információ a rendelkezésre álló különböző virtuálisgép-méretekről:

- [Általános célú](../sizes-general.md)
- [Számításra optimalizált](../sizes-compute.md)
- [Memóriaoptimalizált](../sizes-memory.md)
- [Tárolásra optimalizált](../sizes-storage.md)
- [GPU-optimalizált](../sizes-gpu.md)
- [Nagy teljesítményű számítás](../sizes-hpc.md)
- A standard, a Dv1 (D1-4 és D11-14 v1), valamint az A8-A11 sorozat [előző generációs](../sizes-previous-gen.md) oldalának keresése
