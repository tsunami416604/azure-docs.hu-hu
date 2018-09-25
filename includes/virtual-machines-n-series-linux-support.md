---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 09/24/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: dc03f49a684bfeb43ffd8bac9f551a67f034a04f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042877"
---
## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-illesztőprogramok

NVIDIA CUDA-illesztőprogramok a hálózati vezérlő, NCv2, az NCv3 és ND sorozatú virtuális gépek (NV-sorozat nem kötelező) csak a Linux-disztribúció, az alábbi táblázatban felsorolt támogatottak. CUDA illesztőprogram-adatok az aktuális időpontjában aktuálisak. A legújabb CUDA-illesztőprogramokat, látogasson el a [NVIDIA](https://developer.nvidia.com/cuda-zone) webhelyén. Győződjön meg arról, hogy telepítése vagy frissítése a legújabb illesztőprogramokat a CUDA-a disztribúció. 

> [!TIP]
> Ahelyett, hogy manuális CUDA-illesztőprogram telepítése egy Linux rendszerű virtuális gépen, üzembe helyezhet egy Azure [adatelemző virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. A DSVM-kiadások, az Ubuntu 16.04 LTS vagy CentOS 7.4 előre a NVIDIA CUDA illesztőprogramokat, a CUDA részletes Neurális hálózati könyvtár és egyéb eszközök telepítéséhez.

| Disztribúció | Illesztőprogram |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.4 vagy a 7.3<br/><br/> CentOS-alapú 7.3-as és 7.4-es, CentOS-alapú 7.4-es HPC | NVIDIA CUDA 10.0, illesztőprogram-ág R410 |

### <a name="nvidia-grid-drivers"></a>Az NVIDIA GRID illesztőprogramok

A Microsoft újraterjeszti az NVIDIA GRID illesztőprogramok telepítők NV és virtuális munkaállomásait használt NVv2 sorozatú virtuális gépek vagy a virtuális alkalmazásokhoz. Csak GRID illesztőprogramok telepítése Azure virtuális gépeken NV, csak a szerepel a következő táblázat a disztribúciók. Ezeket az illesztőprogramokat tartalmazza a licencelési rács virtuális GPU szoftverek az Azure-ban.

| Disztribúció | Illesztőprogram |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.4 vagy a 7.3<br/><br/>CentOS-alapú 7.3 vagy 7.4 | Az NVIDIA GRID 6.2, illesztőprogram-ág R390|



> [!WARNING] 
> A külső gyártótól származó szoftverek Red Hat termékekre történő telepítése befolyásolhatja a Red Hat támogatási feltételeit. Tekintse meg a vonatkozó [cikket a Red Hat tudásbázisában](https://access.redhat.com/articles/1067).
>
