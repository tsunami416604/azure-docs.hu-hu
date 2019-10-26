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
ms.openlocfilehash: 224180bdebdf94c6d2360df8ea6615add4675921
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902197"
---
## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-illesztőprogramok

Az NC-, NCv2-, NCv3-, ND-és NDv2-sorozatú virtuális gépek NVIDIA CUDA-illesztőprogramjai csak az alábbi táblázatban felsorolt Linux-disztribúciókban támogatottak. A CUDA illesztőprogram-információi a kiadvány aktuális időpontjában jelennek meg. A legújabb CUDA-illesztőprogramokért látogasson el az [NVIDIA](https://developer.nvidia.com/cuda-zone) webhelyére. Győződjön meg arról, hogy telepíti vagy frissíti a legújabb CUDA-illesztőprogramokat a terjesztéshez. 

> [!TIP]
> Egy Linux rendszerű virtuális gépen a CUDA-illesztőprogram manuális telepítésének alternatívájaként üzembe helyezhet egy Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) rendszerképet. Az Ubuntu 16,04 LTS vagy CentOS 7,4 DSVM kiadásai az NVIDIA CUDA-illesztőprogramok, a CUDA Deep neurális hálózati kódtár és más eszközök előzetes telepítését követően telepíthetők.

| Terjesztés | Illesztőprogram |
| --- | -- | 
| Ubuntu 16,04 LTS, 18,04 LTS<br/><br/> Red Hat Enterprise Linux 7,3, 7,4, 7,5, 7,6<br/><br/> CentOS-alapú 7,3, 7,4, 7,5, 7,6, CentOS-alapú 7,4 HPC | NVIDIA CUDA 10,1, illesztőprogram-ág R418 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-illesztőprogramok

A Microsoft a virtuális munkaállomásként vagy virtuális alkalmazásként használt NV-és NVv3-sorozatú virtuális gépek NVIDIA GRID-illesztőprogram-telepítőit terjeszti ki. Csak az alábbi táblázatban felsorolt operációs rendszereken telepítse ezeket a GRID-illesztőprogramokat az Azure NV virtuális gépeken. Ezek az illesztőprogramok közé tartoznak az Azure-beli GRID virtuális GPU-szoftverek licencelése. Nincs szükség az NVIDIA vGPU szoftverlicenc-kiszolgáló beállítására.

| Terjesztés | Illesztőprogram |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 – 7,6<br/><br/>CentOS-alapú 7,0 – 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 9,1, illesztőprogram-ág R430|

> [!WARNING] 
> A külső gyártótól származó szoftverek Red Hat termékekre történő telepítése befolyásolhatja a Red Hat támogatási feltételeit. Tekintse meg a vonatkozó [cikket a Red Hat tudásbázisában](https://access.redhat.com/articles/1067).
>
