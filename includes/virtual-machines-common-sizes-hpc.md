---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/22/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 36d59638984c31a98067b95fded9626f31a7f278
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "34669361"
---
Az Azure H-sorozatú virtuális gépek a legújabb nagy teljesítményű feldolgozás, virtuális gépek célzó magas szintű számítási igényekre, mint a molekuláris modellezés és folyadékdinamika. Ezek a 8 és 16 vCPU virtuális gépek az Intel Haswell E5-2667 V3 processzor technológia esetében DDR4 memóriával és SSD-alapú ideiglenes tárolási lesz épülnek. 

A jelentős CPU-teljesítmény mellett a H-sorozat különféle lehetőségeket kínál a kis késésű RDMA-hálózatkezeléshez az FDR InfiniBand használatával, valamint számos memóriakonfigurációt is a memóriaigényes számítási követelmények támogatására.



## <a name="h-series"></a>H-sorozat

ACU: 290–300

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






