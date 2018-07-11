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
ms.openlocfilehash: a07f1622f2d41d105c79fa5354d97aa079567e62
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935769"
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nvidia-tesla-cuda-drivers"></a>Az NVIDIA Tesla (CUDA) illesztőprogramok

A hálózati vezérlő, NCv2, az NCv3 és ND sorozatú virtuális gépek (NV-sorozat nem kötelező) az NVIDIA Tesla (CUDA) illesztőprogramok csak az alábbi táblázatban felsorolt operációs rendszerek támogatottak. Illesztőprogram letöltési hivatkozásai a rendszer az aktuális időpontjában aktuálisak. A legújabb illesztőprogramokért látogasson el az [NVIDIA](http://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server virtuális gép manuális CUDA illesztőprogram telepítése helyett, üzembe helyezése Azure-beli [adatelemző virtuális gép](../articles/machine-learning/data-science-virtual-machine/overview.md) kép. A DSVM-kiadások, a Windows Server 2016-hoz a telepítés előtti NVIDIA CUDA illesztőprogramokat, a CUDA részletes Neurális hálózati könyvtár és egyéb eszközökkel.


| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>Az NVIDIA GRID illesztőprogramok

A Microsoft újraterjeszti az NVIDIA GRID illesztőprogramok telepítők használt virtuális munkaállomásait NV-sorozat virtuális gépei vagy a virtuális alkalmazásokhoz. Csak GRID illesztőprogramok telepítése Azure virtuális gépeken NV, csak az alábbi táblázatban felsorolt operációs rendszerek a. Ezeket az illesztőprogramokat tartalmazza a licencelési rács virtuális GPU szoftverek az Azure-ban.

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [RÁCS (391.81) 6.2](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [RÁCS (391.81) 6.2](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |