---
title: Windows virtuális gép méretét, az Azure-ban |} Microsoft Docs
description: A Windows virtuális gépek Azure-ban elérhető különböző méretű sorolja fel.
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
ms.date: 05/22/2018
ms.author: jonbeck
ms.openlocfilehash: d4e1b56ba695f3305d1be023c4352a3c4bcfe27d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658349"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Az Azure-ban a Windows virtuális gépek méretei

Ez a cikk ismerteti az elérhető méretek és a beállítások a Windows-alkalmazások és munkafolyamatok futtatásához használhatja az Azure virtuális gépekhez. Telepítési szempontok érdemes figyelembe vennie, amikor arra készül használni ezeket az erőforrásokat is biztosít.  Ez a cikk érhető el is [Linux virtuális gépek](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Típus                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2 | Kiegyensúlyozott processzor-memória arány. Ideális választás tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, valamint kis és közepes adatforgalmú webkiszolgálókhoz. |
| [Számításra optimalizált](sizes-compute.md)        | Fsv2, Fs, F             | Magas processzor-memória arány. Alkalmas közepes adatforgalmú webkiszolgálók, hálózati berendezések, kötegfolyamatok és alkalmazáskiszolgálók számára.        |
| [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Magas memória-CPU aránya. Ideális választás relációs adatbázis-kiszolgálókhoz, közepes és nagy gyorsítótárakhoz és memóriabeli elemzésekhez.                 |
| [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)        | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](sizes-gpu.md)            | Portok HV, NC, NCv2, NCv3, ND            | Speciális virtuális gépek nagy mennyiségű grafikus megjelenítési és videó szerkesztése szánt, valamint a modell betanítási és inferencing (NT) részletes Learning segítségével. Egy vagy több Feldolgozóegységekkel érhető el.       |
| [Nagy teljesítményű számítás](sizes-hpc.md) | H       | A leggyorsabb és leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű (távoli közvetlen memória-hozzáférést lehetővé tevő) hálózati adapterrel. 


<br> 

- A különböző méretű árazással kapcsolatos információkért lásd: [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Általános korlátozások az Azure virtuális gépeken futó, olvassa el [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../../azure-subscription-service-limits.md).
- A tárolási díjak számítása ettől külön történik a tárfiókban használt oldalak mennyisége alapján. További információkért [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).
- További tudnivalók [Azure számítási egység (ACU)](acu.md) segíthetnek a számítási teljesítmény összehasonlítása Azure termékváltozatok mentén.


## <a name="rest-api"></a>REST API

A lekérdezés REST API-t használ a Virtuálisgép-méretek információkért tekintse át a következőket:

- [Átméretezéséhez elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Az előfizetéshez elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)
- [A rendelkezésre állási csoportok elérhető virtuálisgép-méretek listázása](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

További tudnivalók [Azure számítási egység (ACU)](acu.md) segíthetnek a számítási teljesítmény összehasonlítása Azure termékváltozatok mentén.

## <a name="benchmark-scores"></a>Teljesítményteszt pontszámai

Ismerje meg többet a számítási teljesítmény Windows virtuális gépek használatához a [CoreMark teljesítményteszt pontszámok](compute-benchmark-scores.md).

## <a name="next-steps"></a>További lépések

További információ a különböző Virtuálisgép-méretek érhetők el:
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)
- [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- Ellenőrizze a [előző generációs](sizes-previous-gen.md) lapját A szabványos d.v.1 (D1-4 és D11-14 v1), és A8-A11 sorozat




