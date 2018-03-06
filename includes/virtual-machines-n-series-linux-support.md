---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Hálózati vezérlő által, NCv2, NCv3 és NT-sorozat - NVIDIA CUDA illesztőprogramok
| Disztribúció | Illesztőprogram |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 vagy 7.4<br/><br/> 7.3 vagy 7.4 centOS | NVIDIA CUDA 9.1, illesztőprogram fiókirodai R390 |

> [!IMPORTANT]
> Győződjön meg arról, hogy telepíteni, vagy frissítse a legújabb CUDA illesztőprogramokat a terjesztéshez. R390 verziónál régebbi illesztőprogram frissített Linux kernelek problémák léphetnek fel.
>

### <a name="nv-series---nvidia-grid-drivers"></a>Portok HV-sorozat - NVIDIA rács illesztőprogramok

| Disztribúció | Illesztőprogram |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS-alapú 7.3 | NVIDIA rács 5.2 illesztőprogram fiókirodai R384|

> [!NOTE]
> Microsoft újraterjeszti NVIDIA rács illesztőprogram telepítők Permanens virtuális gépekhez. Csak rács illesztőprogramok telepítése Azure Permanens virtuális gépeken. Ezeket az illesztőprogramokat tartalmazza, a licencelés rács virtuális GPU szoftver az Azure-ban.
>

> [!WARNING] 
> A külső gyártótól származó szoftverek Red Hat termékekre történő telepítése befolyásolhatja a Red Hat támogatási feltételeit. Tekintse meg a vonatkozó [cikket a Red Hat tudásbázisában](https://access.redhat.com/articles/1067).
>
