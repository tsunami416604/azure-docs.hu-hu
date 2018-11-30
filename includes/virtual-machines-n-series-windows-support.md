---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f58d08629fcde791186d27ae09e7417453faf8ad
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52585724"
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nvidia-tesla-cuda-drivers"></a>Az NVIDIA Tesla (CUDA) illesztőprogramok

Az NVIDIA Tesla (CUDA) illesztőprogramjait NC, NCv2, az NCv3, ND és NDv2-sorozat virtuális gépei (NV-sorozat nem kötelező) csak az alábbi táblázatban felsorolt operációs rendszerek támogatottak. Illesztőprogram letöltési hivatkozásai a rendszer az aktuális időpontjában aktuálisak. A legújabb illesztőprogramokért látogasson el az [NVIDIA](http://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server virtuális gép manuális CUDA illesztőprogram telepítése helyett, üzembe helyezése Azure-beli [adatelemző virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. A DSVM-kiadások, a Windows Server 2016-hoz a telepítés előtti NVIDIA CUDA illesztőprogramokat, a CUDA részletes Neurális hálózati könyvtár és egyéb eszközökkel.


| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Az NVIDIA GRID illesztőprogramok

A Microsoft újraterjeszti az NVIDIA GRID illesztőprogramok telepítők NV és virtuális munkaállomásait használt NVv2 sorozatú virtuális gépek vagy a virtuális alkalmazásokhoz. Csak GRID illesztőprogramok telepítése Azure virtuális gépeken NV, csak az alábbi táblázatban felsorolt operációs rendszerek a. Ezeket az illesztőprogramokat tartalmazza a licencelési rács virtuális GPU szoftverek az Azure-ban. Nem kell NVIDIA vGPU software license kiszolgáló beállítása.

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [RÁCS (411.81) 7](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [RÁCS (411.81) 7](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
