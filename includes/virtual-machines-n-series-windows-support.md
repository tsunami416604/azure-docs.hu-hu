---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 426340a5b452124dcba4f51f2ec2c1e2ec0f54b2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>Hálózati vezérlő által, NCv2, NCv3 és NT-sorozat - NVIDIA Tesla (CUDA) illesztőprogramok

Illesztőprogram letöltési hivatkozásokat az alábbi táblázatban a kiadvány időpontjában naprakészek. A legújabb illesztőprogramokért látogasson el az [NVIDIA](http://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server virtuális gép manuális CUDA illesztőprogram telepítése helyett, telepíthet egy Azure [adatok tudományos virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. A Windows Server 2016 DSVM kiadásai a telepítés előtti NVIDIA CUDA illesztőprogramokat, a CUDA mély Neurális hálózat könyvtár és más eszközök.


| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>Portok HV-sorozat - NVIDIA rács illesztőprogramok

Microsoft újraterjeszti NVIDIA rács illesztőprogram telepítők Permanens virtuális gépekhez. Csak rács illesztőprogramok telepítése Azure Permanens virtuális gépeken. Ezeket az illesztőprogramokat tartalmazza, a licencelés rács virtuális GPU szoftver az Azure-ban.

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [5.2 (386.09) rács](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [5.2 (386.09) rács](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |