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
ms.openlocfilehash: 5dccdb6c357635e78b076b1560bf6c0c62c03753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135037"
---
## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA illesztőprogramok

Az NC, NCv2, NCv3, ND és NDv2 sorozatú virtuális gépek (nem kötelező az NV-sorozathoz) NVIDIA CUDA-illesztőprogramok csak az alábbi táblázatban felsorolt Linux-disztribúciókon támogatottak. A CUDA illesztőprogram-információi a közzététel időpontjában aktuálisak. A legújabb CUDA-illesztőprogramokat az [NVIDIA](https://developer.nvidia.com/cuda-zone) webhelyén találja. Győződjön meg arról, hogy telepíti vagy frissíti a legújabb CUDA-illesztőprogramokat a terjesztéshez. 

> [!TIP]
> A Linux virtuális gépen történő manuális CUDA-illesztőprogram-telepítés alternatívájaként üzembe helyezhet egy Azure [Data Science virtuálisgép-lemezképet.](../articles/machine-learning/data-science-virtual-machine/overview.md) Az Ubuntu 16.04 LTS vagy CentOS 7.4 DSVM kiadásai előtelepítik az NVIDIA CUDA illesztőprogramokat, a CUDA Deep Neural Network Library-t és más eszközöket.

| Disztribúció | Illesztőprogram |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> CentOS-alapú 7.3, 7.4, 7.5, 7.6, CentOS-alapú 7.4 HPC | NVIDIA CUDA 10.1, vezető ág R418 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID illesztőprogramok

A Microsoft újraforgalmazza az NVIDIA GRID illesztőprogram-telepítőket a virtuális munkaállomásként vagy virtuális alkalmazásokként használt NV és NVv3 sorozatú virtuális gépekhez. Csak ezeket a GRID-illesztőprogramokat telepítse az Azure NV virtuális gépeken, csak az alábbi táblázatban felsorolt operációs rendszereken. Ezek az illesztőprogramok közé tartozik a GRID Virtual GPU szoftver azure-beli licencelése. Nem kell beállítania egy NVIDIA vGPU szoftverlicenc-kiszolgálót.

| Disztribúció | Illesztőprogram |
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.0-tól 7.6-ig<br/><br/>CentOS-alapú 7,0-7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 10.1, vezető ág R440|

> [!WARNING] 
> A külső gyártótól származó szoftverek Red Hat termékekre történő telepítése befolyásolhatja a Red Hat támogatási feltételeit. Tekintse meg a vonatkozó [cikket a Red Hat tudásbázisában](https://access.redhat.com/articles/1067).
>
