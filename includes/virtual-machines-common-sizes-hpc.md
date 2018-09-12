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
ms.openlocfilehash: 16e2a9cfbd9f08428fddade290117b27bc3401f7
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44369349"
---
Az Azure H-sorozatú virtuális gépek a nagy teljesítményű feldolgozás, virtuális gépek kezelése a számítási feladatok, például a kötegelt feldolgozásra, analytics, molekuláris modellezés és folyadékdinamika irányuló legújabb. Ezek a 8 és 16 vCPU virtuális gépek az Intel Haswell E5-2667 V3 processzor technológia esetében DDR4 memóriával és SSD-alapú ideiglenes tárolási lesz épülnek. 

A jelentős CPU-teljesítmény mellett a H-sorozat különféle lehetőségeket kínál a kis késésű RDMA-hálózatkezeléshez az FDR InfiniBand használatával, valamint számos memóriakonfigurációt is a memóriaigényes számítási követelmények támogatására.



## <a name="h-series"></a>H-sorozat

ACU: 290–300

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Lemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32x500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64x500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32x500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64x500 |4  |
| Standard h16r méretű <sup>1</sup> |16 |112 |2000 |64 |64x500 |4  |
| Standard h16mr méretű <sup>1</sup> |16 |224 |2000 |64 |64x500 |4 |

<sup>1</sup> MPI-alkalmazások, a dedikált RDMA-háttérhálózatot engedélyezve van FDR InfiniBand hálózat, amely ultra rendkívül alacsony késést és magas sávszélességet kínál.

<br>






