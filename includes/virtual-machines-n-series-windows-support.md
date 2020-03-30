---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135160"
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) illesztőprogramok

Az NC, NCv2, NCv3, ND és NDv2 sorozatú virtuális gépek (nvv2 sorozatú virtuális gépek) NVIDIA Tesla (CUDA) illesztőprogramjai csak az alábbi táblázatban felsorolt operációs rendszereken támogatottak. Az illesztőprogram letöltési hivatkozásai a közzététel időpontjában aktuálisak. A legújabb illesztőprogramokért látogasson el az [NVIDIA](https://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server virtuális gépen történő manuális CUDA-illesztőprogram-telepítés alternatívájaként üzembe helyezhet egy Azure [Data Science virtuálisgép-lemezképet.](../articles/machine-learning/data-science-virtual-machine/overview.md) A Windows Server 2016 DSVM kiadásai előtelepítik az NVIDIA CUDA illesztőprogramokat, a CUDA Deep Neural Network Library-t és egyéb eszközöket.


| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID illesztőprogramok

A Microsoft újraforgalmazza az NVIDIA GRID illesztőprogram-telepítőket a virtuális munkaállomásként vagy virtuális alkalmazásokként használt NV és NVv3 sorozatú virtuális gépekhez. Csak ezeket a GRID-illesztőprogramokat telepítse az Azure NV-sorozatú virtuális gépekre, csak az alábbi táblázatban felsorolt operációs rendszereken. Ezek az illesztőprogramok közé tartozik a GRID Virtual GPU szoftver azure-beli licencelése. Nem kell beállítania egy NVIDIA vGPU szoftverlicenc-kiszolgálót.

Kérjük, vegye figyelembe, hogy az Nvidia kiterjesztés mindig telepíti a legújabb illesztőprogramot. Itt az előző verzióra mutató hivatkozásokat biztosítunk olyan ügyfelek számára, akik egy régebbi verziótól függenek.

Windows Server 2019, Windows Server 2016 és Windows 10 esetén (1909-es összeállításig):
- [RÁCS 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [RÁCS 10.0 (441.66)](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Windows Server 2012 R2, Windows Server 2008 R2, Windows 8 és Windows 7 esetén: 
- [RÁCS 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [RÁCS 10.0 (441.66)](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (.exe)  
