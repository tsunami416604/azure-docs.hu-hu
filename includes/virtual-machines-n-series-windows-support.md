---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 8f3d57f8f1f3631421618e31e943606a16e6bf5b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670019"
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nvidia-tesla-cuda-drivers"></a>Illesztőprogramok NVIDIA Tesla (CUDA)

NVIDIA Tesla (CUDA) illesztőprogramok NC, NCv2, NCv3 és ND sorozatú virtuális gépek (portok HV-sorozat nem kötelező) csak a következő táblázatban felsorolt operációs rendszerek támogatottak. Illesztőprogram letöltési hivatkozásokat a kiadvány időpontjában naprakészek. A legújabb illesztőprogramokért látogasson el az [NVIDIA](http://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server virtuális gép manuális CUDA illesztőprogram telepítése helyett, telepíthet egy Azure [adatok tudományos virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. A Windows Server 2016 DSVM kiadásai a telepítés előtti NVIDIA CUDA illesztőprogramokat, a CUDA mély Neurális hálózat könyvtár és más eszközök.


| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>NVIDIA rács illesztőprogramok

Microsoft újraterjeszti NVIDIA rács illesztőprogram telepítők, virtuális munkaállomásokon használt portok HV-sorozatú virtuális gépek vagy a virtuális alkalmazásokhoz. Csak rács illesztőprogramok telepítése Azure virtuális gépeken portok HV, csak a következő táblázatban felsorolt operációs rendszerek. Ezeket az illesztőprogramokat tartalmazza, a licencelés rács virtuális GPU szoftver az Azure-ban.

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [RÁCS 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [RÁCS 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |