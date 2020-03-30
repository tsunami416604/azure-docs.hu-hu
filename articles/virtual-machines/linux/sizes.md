---
title: Linux os virtuális gépek mérete az Azure-ban
description: A Linux-alapú virtuális gépek hez az Azure-ban elérhető különböző méretek listája.
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
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 46fad0dd2e39f1f563ed248458da622ad83f061e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161116"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>A Linux rendszerű virtuális gépek méretei az Azure-ban

Ez a cikk ismerteti a rendelkezésre álló méretek és lehetőségek az Azure virtuális gépek linuxos alkalmazások és számítási feladatok futtatásához használható. Azt is előírja, hogy üzembe helyezési szempontokat, hogy tudatában legyenek, ha azt tervezi, hogy használja ezeket az erőforrásokat. Ez a cikk [a Windows virtuális gépekhez](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)is elérhető.

| Típus | Méretek | Leírás |
|------|-------|-------------|
| [Általános célú](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2  | Kiegyensúlyozott CPU-memória arány. Ideális választások a teszteléshez és a fejlesztéshez, a kicsi és közepes adatbázisokhoz, illetve az alacsony és közepes forgalmú webkiszolgálókhoz. |
| [Számításra optimalizált](../sizes-compute.md) | Fsv2 között | Magas CPU-memória arány. Jó közepes forgalmú webkiszolgálókhoz, hálózati készülékekhez, kötegelt folyamatokhoz és alkalmazáskiszolgálókhoz. |
| [Memóriaoptimalizált](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Magas memória-CPU arány. Ideális választások a relációs adatbázisok kiszolgálóihoz, a közepes és nagy gyorsítótárakhoz, valamint a memóriában végzett elemzésekhez.                 |
| [Tárolásra optimalizált](../sizes-storage.md) | Lsv2 között | Nagy lemezátható és i/o ideális Big Data, SQL, NoSQL adatbázisok, adattárház és nagy tranzakciós adatbázisok.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (Előzetes), NV, NVv3, NVv4 | Speciális virtuális gépek, amelyek a nehéz grafikus renderelésre és videoszerkesztésre, valamint a modelloktatásra és -következtetésre (ND) irányulnak mély tanulással. Egy vagy több GPU-val érhető el. |
| [Nagy teljesítményű számítás](../sizes-hpc.md) | HB, HBv2, HC, H | Leggyorsabb és legerősebb CPU virtuális gépeink opcionális, nagy átmenőteljesítményű hálózati interfészekkel (RDMA). |

- A különböző méretű árakról a [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)című témakörben talál további információt. 
- A virtuális gép méretei nek az Azure-régiókban való elérhetőségéről a [Termékek régiónként elérhető.](https://azure.microsoft.com/regions/services/)
- Az Azure virtuális gépek általános korlátozásait az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások megtekintéséről](../../azure-subscription-service-limits.md)olvassa el.
- További információ arról, hogy [az Azure számítási egységei (ACU)](../acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.

## <a name="rest-api"></a>REST API

A REST API virtuális gépméretek lekérdezéséhez való használatáról az alábbi témakörökben talál további információt:

- [A virtuális gépek átméretezéséhez rendelkezésre álló méretek listája](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Előfizetéshez rendelkezésre álló virtuális gépméretek listája](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [A rendelkezésre állási csoportban elérhető virtuális gépméretek listája](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

További információ arról, hogy [az Azure számítási egységei (ACU)](../acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

További információ a Linux-alapú virtuális gépek számítási teljesítményéről a [CoreMark teljesítménypontszámainak](compute-benchmark-scores.md)használatával.

## <a name="next-steps"></a>További lépések

További információ a rendelkezésre álló virtuálisgép-méretekről:

- [Általános célú](../sizes-general.md)
- [Számításra optimalizált](../sizes-compute.md)
- [Memóriaoptimalizált](../sizes-memory.md)
- [Tárolásra optimalizált](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Nagy teljesítményű számítás](../sizes-hpc.md)
- Ellenőrizze az [Előző generációs](../sizes-previous-gen.md) lapon az A Standard, Dv1 (D1-4 és D11-14 v1) és Az A8-A11 sorozatot