---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5bfe077a99a3e89efe5fe4257a646484fb3810da
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96537056"
---
## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-illesztőprogramok

Az NC-, NCv2-, NCv3-, ND-és NDv2-sorozatú virtuális gépek NVIDIA CUDA-illesztőprogramjai csak az alábbi táblázatban felsorolt Linux-disztribúciókban támogatottak. A CUDA illesztőprogram-információi a kiadvány aktuális időpontjában jelennek meg. A legújabb CUDA-illesztőprogramok és a támogatott operációs rendszerek az [NVIDIA](https://developer.nvidia.com/cuda-zone) webhelyén érhetők el. Győződjön meg arról, hogy telepíti vagy frissíti a legújabb CUDA-illesztőprogramokat a terjesztéshez. 

> [!TIP]
> Egy Linux rendszerű virtuális gépen a CUDA-illesztőprogram manuális telepítésének alternatívájaként üzembe helyezhet egy Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) rendszerképet. Az Ubuntu 16,04 LTS vagy CentOS 7,4 DSVM kiadásai az NVIDIA CUDA-illesztőprogramok, a CUDA Deep neurális hálózati kódtár és más eszközök előzetes telepítését követően telepíthetők.


### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-illesztőprogramok

A Microsoft a virtuális munkaállomásként vagy virtuális alkalmazásként használt NV-és NVv3-sorozatú virtuális gépek NVIDIA GRID-illesztőprogram-telepítőit terjeszti ki. Csak az alábbi táblázatban felsorolt operációs rendszereken telepítse ezeket a GRID-illesztőprogramokat az Azure NV virtuális gépeken. Ezek az illesztőprogramok közé tartoznak az Azure-beli GRID virtuális GPU-szoftverek licencelése. Nincs szükség az NVIDIA vGPU szoftverlicenc-kiszolgáló beállítására.

Az Azure által újraterjesztett GRID-illesztőprogramok nem működnek a nem NV sorozatú virtuális gépeken, például az NC, a NCv2, a NCv3, az ND és a NDv2 sorozatú virtuális gépeken.

|Disztribúció|Illesztő|
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,6 – 7,8, 8,0, 8,1<br/><br/>CentOS-alapú 7,6, 7,7, 8,0, 8 (1911)<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 11,2, illesztőprogram-ág [R450](https://go.microsoft.com/fwlink/?linkid=874272)(. exe)|

Az összes korábbi NVIDIA GRID-illesztőprogram-hivatkozás teljes listájáért látogasson el a [githubra](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) .

> [!WARNING] 
> A külső gyártótól származó szoftverek Red Hat termékekre történő telepítése befolyásolhatja a Red Hat támogatási feltételeit. Tekintse meg a vonatkozó [cikket a Red Hat tudásbázisában](https://access.redhat.com/articles/1067).
>
