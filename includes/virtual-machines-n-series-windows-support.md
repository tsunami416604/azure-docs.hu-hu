---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9b36a8e90c20c8f0d08ca241e48e9306fb5b10f8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>Hálózati vezérlő által, NCv2, NCv3 és NT-sorozat - NVIDIA Tesla (CUDA) illesztőprogramok

Illesztőprogram letöltési hivatkozásokat az alábbi táblázatban a kiadvány időpontjában naprakészek. A legújabb illesztőprogramokért látogasson el az [NVIDIA](http://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server virtuális gép manuális CUDA illesztőprogram telepítése helyett, telepíthet egy Azure [adatok tudományos virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. A Windows Server 2016 DSVM kiadásai a telepítés előtti NVIDIA CUDA illesztőprogramokat, a CUDA mély Neurális hálózat könyvtár és más eszközök.


| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [391.29](http://us.download.nvidia.com/Windows/Quadro_Certified/391.29/391.29-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [391.29](http://us.download.nvidia.com/Windows/Quadro_Certified/391.29/391.29-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>Portok HV-sorozat - NVIDIA rács illesztőprogramok

Microsoft újraterjeszti NVIDIA rács illesztőprogram telepítők Permanens virtuális gépekhez. Csak rács illesztőprogramok telepítése Azure Permanens virtuális gépeken. Ezeket az illesztőprogramokat tartalmazza, a licencelés rács virtuális GPU szoftver az Azure-ban.

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [RÁCS 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [RÁCS 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |