---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7139ec67536a1c0e41c991db6d867b956f995c11
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Hálózati vezérlő által, NCv2, NCv3 és NT-sorozat - NVIDIA CUDA illesztőprogramok

CUDA illesztőprogram információkat az alábbi táblázatban az aktuális időpontban kiadvány. A legújabb CUDA illesztőprogramokat, látogasson el a [NVIDIA](https://developer.nvidia.com/cuda-zone) webhelyet. Győződjön meg arról, hogy telepíteni, vagy frissítse a legújabb CUDA illesztőprogramokat a terjesztéshez. 

> [!TIP]
> A Linux virtuális gép manuális CUDA illesztőprogram telepítése helyett, telepíthet egy Azure [adatok tudományos virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. Ubuntu 16.04 LTS vagy CentOS 7.4 DSVM kiadásai a telepítés előtti NVIDIA CUDA illesztőprogramokat, a CUDA mély Neurális hálózat könyvtár és más eszközök.

| Disztribúció | Illesztőprogram |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 vagy 7.4<br/><br/> CentOS-alapú 7.3 vagy 7.4, CentOS-alapú 7.4 HPC | NVIDIA CUDA 9.1, illesztőprogram fiókirodai R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>Portok HV-sorozat - NVIDIA rács illesztőprogramok

Microsoft újraterjeszti NVIDIA rács illesztőprogram telepítők Permanens virtuális gépekhez. Csak rács illesztőprogramok telepítése Azure Permanens virtuális gépeken. Ezeket az illesztőprogramokat tartalmazza, a licencelés rács virtuális GPU szoftver az Azure-ban.

| Disztribúció | Illesztőprogram |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 vagy 7.4<br/><br/>CentOS-alapú 7.3 vagy 7.4 | NVIDIA rács 6.0 illesztőprogram fiókirodai R390|



> [!WARNING] 
> A külső gyártótól származó szoftverek Red Hat termékekre történő telepítése befolyásolhatja a Red Hat támogatási feltételeit. Tekintse meg a vonatkozó [cikket a Red Hat tudásbázisában](https://access.redhat.com/articles/1067).
>
