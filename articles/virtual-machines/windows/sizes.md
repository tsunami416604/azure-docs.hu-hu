---
title: Windows rendszerű virtuális gépek méretei az Azure-ban | Microsoft Docs
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
ms.date: 08/08/2019
ms.author: jonbeck
ms.openlocfilehash: 093e990365c2b33a873236f5d0ad6a1395e5e8fc
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967568"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>A Windows rendszerű virtuális gépek méretei az Azure-ban

Ez a cikk a Windows-alkalmazások és-munkaterhelések futtatására használható Azure-beli virtuális gépek rendelkezésre álló méreteit és lehetőségeit ismerteti. Emellett üzembe helyezési megfontolásokat is biztosít, hogy a rendszer mikor tervezi ezeket az erőforrásokat.  Ez a cikk Linux rendszerű [virtuális gépekhez](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)is elérhető.


| Type                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](sizes-general.md)          | B, Dsv3, Dv3, Dasv3, Dav3, DSv2, Dv2, Av2, DC  | Kiegyensúlyozott processzor-memória arány. Ideális választás tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, valamint kis és közepes adatforgalmú webkiszolgálókhoz. |
| [Számításra optimalizált](sizes-compute.md)        | Fsv2           | Magas processzor-memória arány. Alkalmas közepes adatforgalmú webkiszolgálók, hálózati berendezések, kötegfolyamatok és alkalmazáskiszolgálók számára.        |
| [Memóriaoptimalizált](sizes-memory.md)         | Esv3, Ev3, Easv3, Eav3, Mv2, M, DSv2, Dv2  | Magas memória-CPU arány. Ideális választás relációs adatbázis-kiszolgálókhoz, közepes és nagy gyorsítótárakhoz és memóriabeli elemzésekhez.                 |
| [Tárolásra optimalizált](sizes-storage.md)        | Lsv2                | A nagy adatátviteli sebesség és az i/o ideális megoldás a Big Database, az SQL, a NoSQL adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok számára.  |
| [GPU](sizes-gpu.md)            | NC, NCv2, NCv3, ND, NDv2 (előzetes verzió), NV, NVv3 | A speciális virtuális gépek nagy grafikai renderelést és videoszerkesztőt céloznak meg, valamint a képzési és következtetési (ND) modellek mély tanulással. Egyetlen vagy több GPU-val is elérhető.       |
| [Nagy teljesítményű számítás](sizes-hpc.md) | HB, HC, H | A leggyorsabb és leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű (távoli közvetlen memória-hozzáférést lehetővé tevő) hálózati adapterrel. |


<br> 

- További információ a különböző méretek díjszabásáról: [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Az Azure-beli virtuális gépekre vonatkozó általános korlátokat lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-subscription-service-limits.md).
- A tárolási díjak számítása ettől külön történik a tárfiókban használt oldalak mennyisége alapján. Részletekért lásd az [Azure Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).
- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.


## <a name="rest-api"></a>REST API

A virtuális gépek méretének lekérdezéséhez REST API használatáról a következő témakörben tájékozódhat:

- [Az átméretezéshez rendelkezésre álló virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Az előfizetéshez elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Rendelkezésre álló virtuálisgép-méretek listázása rendelkezésre állási csoportokban](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

További információ a Windows rendszerű virtuális gépek számítási teljesítményéről a [megjegyzésekkel kapcsolatos teljesítménytesztek pontszámai](compute-benchmark-scores.md)alapján.

## <a name="next-steps"></a>További lépések

További információ a rendelkezésre álló különböző virtuálisgép-méretekről:
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)
- [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- A standard, a Dv1 (D1-4 és D11-14 v1), valamint az A8-A11 sorozat [előző generációs](sizes-previous-gen.md) oldalának keresése




