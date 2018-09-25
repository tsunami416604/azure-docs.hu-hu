---
title: Windows virtuális gépek méretei az Azure-ban |} A Microsoft Docs
description: Az Azure-beli Windows virtuális gépek számára elérhető különböző méretű sorolja fel.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jonbeck
ms.openlocfilehash: 47253fd05cb1df96841b30357ac6e7cfe75c12c5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039684"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Az Azure-beli Windows virtuális gépek méretei

Ez a cikk ismerteti az elérhető méretek és a beállításokat az Azure virtuális gépeket, a Windows-alkalmazások és számítási feladatok futtatására használhatja. Érdemes figyelembe vennie, ha tervezi az ezekkel az erőforrásokkal való telepítésével kapcsolatos megfontolások is tartalmazza.  Ez a cikk érhető el is [Linux rendszerű virtuális gépek](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Típus                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](sizes-general.md)          | B, Dsv3, Dv3, DSv2, a Dv2, az Av2 | Kiegyensúlyozott processzor-memória arány. Ideális választás tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, valamint kis és közepes adatforgalmú webkiszolgálókhoz. |
| [Számításra optimalizált](sizes-compute.md)        | F Fsv2, az Fs-ben             | Magas processzor-memória arány. Alkalmas közepes adatforgalmú webkiszolgálók, hálózati berendezések, kötegfolyamatok és alkalmazáskiszolgálók számára.        |
| [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)         | Az Esv3, Ev3, M, GS, G, DSv2, a Dv2  | Magas memória – Processzor arány. Ideális választás relációs adatbázis-kiszolgálókhoz, közepes és nagy gyorsítótárakhoz és memóriabeli elemzésekhez.                 |
| [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)        | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](sizes-gpu.md)            | NV, NVv2, NC, NCv2, az NCv3, SD            | Specializált virtuális gépek grafikai renderelésre és videószerkesztésre célzott, valamint modell a betanítási vagy következtetési (ND) használatával deep learninget. Elérhető egy vagy több gpu-kkal.       |
| [Nagy teljesítményű számítás](sizes-hpc.md) | H       | A leggyorsabb és leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű (távoli közvetlen memória-hozzáférést lehetővé tevő) hálózati adapterrel. 


<br> 

- A különböző fürtméretekkel járó díjszabással kapcsolatos információkért lásd: [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Általános korlátozások az Azure virtuális gépekhez, olvassa el [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../../azure-subscription-service-limits.md).
- A tárolási díjak számítása ettől külön történik a tárfiókban használt oldalak mennyisége alapján. További információkért [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).
- Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.


## <a name="rest-api"></a>REST API

Információk a Virtuálisgép-méretek lekérdezéshez REST API használatával tekintse meg a következőket:

- [Átméretezéséhez elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Egy előfizetéshez tartozó elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/virtualmachinesizes/list)
- [Egy rendelkezésre állási csoportban elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

Ismerje meg, hogy többet számítási teljesítményt Windows virtuális gépek használata a [CoreMark számításiteljesítmény-mérési pontszámok](compute-benchmark-scores.md).

## <a name="next-steps"></a>További lépések

További információ a különböző Virtuálisgép-méretek érhetők el:
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)
- [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- Ellenőrizze a [előző generációs](sizes-previous-gen.md) lapját A Standard, a Dv1 (D1-4 és D11-14 v1), és az A8 – A11-sorozat




