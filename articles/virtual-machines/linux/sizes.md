---
title: Az Azure-beli Linuxos virtuális gépek méreteit |} A Microsoft Docs
description: A Linux rendszerű virtuális gépek az Azure-ban elérhető különböző méretű sorolja fel.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: jonbeck
ms.openlocfilehash: 7445b0ce2cc80a899ef5d34c01f37564d255b5ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708757"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Az Azure-ban Linux rendszerű virtuális gépek méretei
Ez a cikk ismerteti az elérhető méretek és a beállításokat az Azure virtuális gépeket, a Linux-alkalmazások és számítási feladatok futtatására használhatja. Érdemes figyelembe vennie, ha tervezi az ezekkel az erőforrásokkal való telepítésével kapcsolatos megfontolások is tartalmazza. Ez a cikk érhető el is [Windows virtuális gépek](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Type                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Kiegyensúlyozott processzor-memória arány. Ideális választás tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, valamint kis és közepes adatforgalmú webkiszolgálókhoz. |
| [Számításra optimalizált](sizes-compute.md)        | Fsv2           | Magas processzor-memória arány. Alkalmas közepes adatforgalmú webkiszolgálók, hálózati berendezések, kötegfolyamatok és alkalmazáskiszolgálók számára.        |
| [Memóriaoptimalizált](sizes-memory.md)         | Az Esv3, Ev3, Mv2, M, DSv2, a Dv2  | Magas memória – Processzor arány. Ideális választás relációs adatbázis-kiszolgálókhoz, közepes és nagy gyorsítótárakhoz és memóriabeli elemzésekhez.                 |
| [Tárolásra optimalizált](sizes-storage.md)        | Lsv2                | Magas lemez-adatátviteli és i/o ideális Big Data, SQL, nosql-alapú adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok.  |
| [GPU](sizes-gpu.md)            | Hálózati vezérlő, NCv2, az NCv3, ND, NDv2 (előzetes verzió), NV, NVv3 (előzetes verzió) | Specializált virtuális gépek grafikai renderelésre és videószerkesztésre célzott, valamint modell a betanítási vagy következtetési (ND) használatával deep learninget. Elérhető egy vagy több gpu-kkal.       |
| [Nagy teljesítményű számítás](sizes-hpc.md) | HB, HC,  H | A leggyorsabb és leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű (távoli közvetlen memória-hozzáférést lehetővé tevő) hálózati adapterrel. |

<br>

- A különböző fürtméretekkel járó díjszabással kapcsolatos információkért lásd: [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- A Virtuálisgép-méretek az Azure-régiók rendelkezésre állás érdekében tekintse meg a [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/).
- Általános korlátozások az Azure virtuális gépekhez, olvassa el [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../../azure-subscription-service-limits.md).
- Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.


## <a name="rest-api"></a>REST API

Információk a Virtuálisgép-méretek lekérdezéshez REST API használatával tekintse meg a következőket:

- [Átméretezéséhez elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Egy előfizetéshez tartozó elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Egy rendelkezésre állási csoportban elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

Ismerje meg bővebben számítási teljesítmény, a Linux rendszerű virtuális gépek a [CoreMark számításiteljesítmény-mérési pontszámok](compute-benchmark-scores.md).

## <a name="next-steps"></a>További lépések

További információ a különböző Virtuálisgép-méretek érhetők el:
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- Ellenőrizze a [előző generációs](sizes-previous-gen.md) lapját A Standard, a Dv1 (D1-4 és D11-14 v1), és az A8 – A11-sorozat



