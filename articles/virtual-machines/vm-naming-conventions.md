---
title: Azure-beli virtuálisgép-méretek elnevezési konvenciói
description: Ismerteti az Azure-beli virtuális gépek méreteihez használt elnevezési konvenciókat
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 13894e534dc8d6dd89baf75ea2bd3b6500b718f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650961"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Az Azure-beli virtuális gépek méretei elnevezési konvenciói

Ez az oldal az Azure-beli virtuális gépekhez használt elnevezési konvenciókat ismerteti. A virtuális gépek ezeket a elnevezési konvenciókat használják a különböző funkciók és specifikációk jelölésére.

## <a name="naming-convention-explanation"></a>Elnevezési konvenció magyarázata

**[Család]**  +  **[Alcsalád *]**  +  **[vCPU száma]**  +  **[Adalékanyag funkciói]**  +  **[Gyorssegéd típusa *]**  +  **[Verzió]**

|Érték | Magyarázat|
|---|---|
| Family (Család) | A virtuálisgép-család adatsorozatát jelzi.| 
| * Alárendelt család | Kizárólag a speciális virtuálisgép-megkülönböztetésekhez használatos|
| vCPU száma| A virtuális gép vCPU számát jelöli. |
| Adalékanyag-funkciók | Egy vagy több kisbetű az adalékanyag funkcióit jelöli, például: <br> a = AMD-alapú processzor <br> d = lemez (a helyi Temp lemez megtalálható); Ez az újabb Azure-beli virtuális gépek esetén: [Ddv4 és Ddsv4 sorozat](./ddv4-ddsv4-series.md) <br> h = hibernálásra alkalmas <br> i = elkülönített méret <br> l = kevés a memória; a memória méretének megfelelőnél kevesebb memóriát igényel <br> m = intenzív memória; a legnagyobb mennyiségű memória egy adott méretben <br> t = kis memória; egy adott méretű memória legkisebb mennyisége <br> r = RDMA-kompatibilis <br> s = Premium Storage képes, beleértve a [Ultra SSD](./disks-types.md#ultra-disk) lehetséges használatát is (Megjegyzés: a s attribútum nélküli néhány újabb méret továbbra is támogatja Premium Storage például M128, M64 stb.).<br> |
| * Gyorssegéd típusa | A speciális/GPU SKU-ban lévő hardveres gyorssegéd típusát jelöli. A (z) 2020-es Q3-ből indított új speciális/GPU-SKU-ra csak a hardveres gyorssegéd lesz a névben. |
| Verzió | A virtuálisgép-család adatsorozatának verzióját jelöli. |

## <a name="example-breakdown"></a>Példa bontás

**[Család]**  +  **[Alcsalád *]**  +  **[vCPU száma]**  +  **[Adalékanyag funkciói]**  +  **[Gyorssegéd típusa *]**  +  **[Verzió]**

### <a name="example-1-m416ms_v2"></a>1. példa: M416ms_v2

|Érték | Magyarázat|
|---|---|
| Family (Család) | M | 
| vCPU száma | 416 |
| Adalékanyag-funkciók | m = intenzív memória <br> s = Premium Storage képes |
| Verzió | v2 |

### <a name="example-2-nv16as_v4"></a>2. példa: NV16as_v4

|Érték | Magyarázat|
|---|---|
| Family (Család) | N | 
| Alárendelt család | V |
| vCPU száma | 16 |
| Adalékanyag-funkciók | a = AMD-alapú processzor <br> s = Premium Storage képes |
| Verzió | v4 |

### <a name="example-3-nc4as_t4_v3"></a>3. példa: NC4as_T4_v3

|Érték | Magyarázat|
|---|---|
| Family (Család) | N | 
| Alárendelt család | C |
| vCPU száma | 4 |
| Adalékanyag-funkciók | a = AMD-alapú processzor <br> s = Premium Storage képes |
| Gyorssegéd típusa | T4 |
| Verzió | v3 |

## <a name="next-steps"></a>További lépések

További információ az elérhető virtuálisgép- [méretekről](./sizes.md) az Azure-ban. 