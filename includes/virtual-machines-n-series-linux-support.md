---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0264f92fa10bd503a2811ce40ee0b8d4edd5f3b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34669832"
---
## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA illesztőprogramok

Csak a Linux terjesztéseket, a következő táblázatban felsorolt támogatottak NVIDIA CUDA illesztőprogramok NC, NCv2, NCv3 és ND sorozatú virtuális gépek (portok HV-sorozat nem kötelező). CUDA illesztőprogram adatai az aktuális időpontban kiadvány. A legújabb CUDA illesztőprogramokat, látogasson el a [NVIDIA](https://developer.nvidia.com/cuda-zone) webhelyet. Győződjön meg arról, hogy telepíteni, vagy frissítse a legújabb CUDA illesztőprogramokat a terjesztéshez. 

> [!TIP]
> A Linux virtuális gép manuális CUDA illesztőprogram telepítése helyett, telepíthet egy Azure [adatok tudományos virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. Ubuntu 16.04 LTS vagy CentOS 7.4 DSVM kiadásai a telepítés előtti NVIDIA CUDA illesztőprogramokat, a CUDA mély Neurális hálózat könyvtár és más eszközök.

| Disztribúció | Illesztőprogram |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 vagy 7.4<br/><br/> CentOS-alapú 7.3 vagy 7.4, CentOS-alapú 7.4 HPC | NVIDIA CUDA 9.1, illesztőprogram fiókirodai R390 |

### <a name="nvidia-grid-drivers"></a>NVIDIA rács illesztőprogramok

Microsoft újraterjeszti NVIDIA rács illesztőprogram telepítők, virtuális munkaállomásokon használt portok HV-sorozatú virtuális gépek vagy a virtuális alkalmazásokhoz. Csak rács illesztőprogramok telepítése Azure virtuális gépeken portok HV, csak az azokat a terjesztéseket, a következő táblázatban található a. Ezeket az illesztőprogramokat tartalmazza, a licencelés rács virtuális GPU szoftver az Azure-ban.

| Disztribúció | Illesztőprogram |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 vagy 7.4<br/><br/>CentOS-alapú 7.3 vagy 7.4 | NVIDIA rács 6.0 illesztőprogram fiókirodai R390|



> [!WARNING] 
> A külső gyártótól származó szoftverek Red Hat termékekre történő telepítése befolyásolhatja a Red Hat támogatási feltételeit. Tekintse meg a vonatkozó [cikket a Red Hat tudásbázisában](https://access.redhat.com/articles/1067).
>
