---
title: VM-méretek az Azure-ban
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző méreteket.
author: ju-shim
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a60200bd35d142b8b55a40d6912f946f9421f52e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207659"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>A Windows rendszerű virtuális gépek méretei az Azure-ban

Ez a cikk a Windows-alkalmazások és-munkaterhelések futtatására használható Azure-beli virtuális gépek rendelkezésre álló méreteit és lehetőségeit ismerteti. Emellett üzembe helyezési megfontolásokat is biztosít, hogy a rendszer mikor tervezi ezeket az erőforrásokat.  Ez a cikk a Windows és a Linux rendszerű virtuális gépekre is vonatkozik.

| Típus | Méretek | Leírás |
|------|-------|-------------|
| [Általános célú](../sizes-general.md) | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, DV4, Dsv4, Ddv4, Ddsv4 | Kiegyensúlyozott processzor-memória arány. Ideális választások a teszteléshez és a fejlesztéshez, a kicsi és közepes adatbázisokhoz, illetve az alacsony és közepes forgalmú webkiszolgálókhoz. |
| [Számításoptimalizált](../sizes-compute.md) | Fsv2 | Magas processzor-memória arány. Jó a közepes forgalmú webkiszolgálók, a hálózati berendezések, a kötegelt folyamatok és az alkalmazáskiszolgáló számára. |
| [Memóriaoptimalizált](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Magas memória-CPU arány. Ideális választások a relációs adatbázisok kiszolgálóihoz, a közepes és nagy gyorsítótárakhoz, valamint a memóriában végzett elemzésekhez. |
| [Tároptimalizált](../sizes-storage.md)  | Lsv2 | A nagy adatátviteli sebesség és az i/o ideális megoldás a Big Database, az SQL, a NoSQL adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok számára.  |
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

## <a name="manage-costs"></a>Költségek kezelése

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>További lépések

További információ a rendelkezésre álló különböző virtuálisgép-méretekről:

- [Általános célú](../sizes-general.md)
- [Számításoptimalizált](../sizes-compute.md)
- [Memóriaoptimalizált](../sizes-memory.md)
- [Tároptimalizált](../sizes-storage.md)
- [GPU-optimalizált](../sizes-gpu.md)
- [Nagy teljesítményű számítás](../sizes-hpc.md)
- A standard, a Dv1 (D1-4 és D11-14 v1), valamint az A8-A11 sorozat [előző generációs](../sizes-previous-gen.md) oldalának keresése
