---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3d4c58cd9e7ee0674f42d776c8df6e96faba1ab1
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886862"
---
## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-illesztőprogramok

NVIDIA CUDA illesztőprogramjait NC, NCv2, az NCv3, ND és NDv2-sorozat virtuális gépei (NV-sorozat nem kötelező) csak a Linux-disztribúció, az alábbi táblázatban felsorolt támogatottak. CUDA illesztőprogram-adatok az aktuális időpontjában aktuálisak. A legújabb CUDA-illesztőprogramokat, látogasson el a [NVIDIA](https://developer.nvidia.com/cuda-zone) webhelyén. Győződjön meg arról, hogy telepítése vagy frissítése a legújabb illesztőprogramokat a CUDA-a disztribúció. 

> [!TIP]
> Ahelyett, hogy manuális CUDA-illesztőprogram telepítése egy Linux rendszerű virtuális gépen, üzembe helyezhet egy Azure [adatelemző virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. A DSVM-kiadások, az Ubuntu 16.04 LTS vagy CentOS 7.4 előre a NVIDIA CUDA illesztőprogramokat, a CUDA részletes Neurális hálózati könyvtár és egyéb eszközök telepítéséhez.

| Disztribúció | Illesztőprogram |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> CentOS-alapú 7.3, 7.4, 7.5, 7.6, CentOS-alapú 7.4-es HPC | NVIDIA CUDA 10.1, driver branch R418 |

### <a name="nvidia-grid-drivers"></a>Az NVIDIA GRID illesztőprogramok

A Microsoft újraterjeszti az NVIDIA GRID illesztőprogramok telepítők NV és virtuális munkaállomásait használt NVv2 sorozatú virtuális gépek vagy a virtuális alkalmazásokhoz. Csak GRID illesztőprogramok telepítése Azure virtuális gépeken NV, csak az alábbi táblázatban felsorolt operációs rendszerek a. Ezeket az illesztőprogramokat tartalmazza a licencelési rács virtuális GPU szoftverek az Azure-ban. Nem kell NVIDIA vGPU software license kiszolgáló beállítása.

| Disztribúció | Illesztőprogram |
| --- | -- |
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/>CentOS-alapú 7.3, 7.4, 7.5, 7.6 | NVIDIA GRID 7.1, driver branch R410|

> [!WARNING] 
> A külső gyártótól származó szoftverek Red Hat termékekre történő telepítése befolyásolhatja a Red Hat támogatási feltételeit. Tekintse meg a vonatkozó [cikket a Red Hat tudásbázisában](https://access.redhat.com/articles/1067).
>
