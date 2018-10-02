---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/24/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4da90cf636ab2010d7c369f4c13e45190dc6b2db
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48020861"
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nvidia-tesla-cuda-drivers"></a>Az NVIDIA Tesla (CUDA) illesztőprogramok

A hálózati vezérlő, NCv2, az NCv3 és ND sorozatú virtuális gépek (NV-sorozat nem kötelező) az NVIDIA Tesla (CUDA) illesztőprogramok csak az alábbi táblázatban felsorolt operációs rendszerek támogatottak. Illesztőprogram letöltési hivatkozásai a rendszer az aktuális időpontjában aktuálisak. A legújabb illesztőprogramokért látogasson el az [NVIDIA](http://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server virtuális gép manuális CUDA illesztőprogram telepítése helyett, üzembe helyezése Azure-beli [adatelemző virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. A DSVM-kiadások, a Windows Server 2016-hoz a telepítés előtti NVIDIA CUDA illesztőprogramokat, a CUDA részletes Neurális hálózati könyvtár és egyéb eszközökkel.


| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>Az NVIDIA GRID illesztőprogramok

A Microsoft újraterjeszti NVIDIA GRID illesztőprogramok telepítők NV amd NVv2 sorozatú virtuális gépek virtuális munkaállomásait használt vagy a virtuális alkalmazásokhoz. Csak GRID illesztőprogramok telepítése Azure virtuális gépeken NV, csak az alábbi táblázatban felsorolt operációs rendszerek a. Ezeket az illesztőprogramokat tartalmazza a licencelési rács virtuális GPU szoftverek az Azure-ban.

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [RÁCS (391.81) 6.2](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [RÁCS (391.81) 6.2](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |