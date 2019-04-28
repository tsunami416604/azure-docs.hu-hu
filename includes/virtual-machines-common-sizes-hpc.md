---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: a8f0e61a953a2e2471e49d571063f6202b7ab76d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540505"
---
Az Azure H-sorozatú virtuális gépek a nagy teljesítményű feldolgozás, virtuális gépek kezelése a számítási feladatok, például a kötegelt feldolgozásra, analytics, molekuláris modellezés és folyadékdinamika irányuló legújabb. Ezek a 8 és 16 vCPU virtuális gépek az Intel Haswell E5-2667 V3 processzor technológia esetében DDR4 memóriával és SSD-alapú ideiglenes tárolási lesz épülnek. 

A jelentős CPU-teljesítmény mellett a H-sorozat különféle lehetőségeket kínál a kis késésű RDMA-hálózatkezeléshez az FDR InfiniBand használatával, valamint számos memóriakonfigurációt is a memóriaigényes számítási követelmények támogatására.



## <a name="h-series"></a>H-sorozat

ACU: 290-300

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Lemezek max. teljesítménye: IO | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32x500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64x500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32x500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64x500 |4  |
| Standard h16r méretű <sup>1</sup> |16 |112 |2000 |64 |64x500 |4  |
| Standard h16mr méretű <sup>1</sup> |16 |224 |2000 |64 |64x500 |4 |

<sup>1</sup> MPI-alkalmazások, a dedikált RDMA-háttérhálózatot engedélyezve van FDR InfiniBand hálózat, amely ultra rendkívül alacsony késést és magas sávszélességet kínál.

<br>






