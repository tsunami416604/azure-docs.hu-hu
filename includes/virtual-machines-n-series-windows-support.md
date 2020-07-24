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
ms.openlocfilehash: 04b085d2e990a580ddc99acb3b83ac8bd8ac2db3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "86998977"
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) illesztőprogramok

Az NC-, NCv2-, NCv3-, ND-és NDv2-sorozatú virtuális gépek NVIDIA Tesla-(CUDA-) illesztőprogramjai csak az alábbi táblázatban felsorolt operációs rendszereken támogatottak. Az illesztőprogram-letöltési hivatkozások a kiadvány aktuális időpontjában jelennek meg. A legújabb illesztőprogramokért látogasson el az [NVIDIA](https://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server rendszerű virtuális gépeken a CUDA-illesztőprogram manuális telepítésének alternatívájaként üzembe helyezhet egy Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) rendszerképet. A Windows Server 2016 DSVM kiadásai NVIDIA CUDA-illesztőprogramokat, a CUDA Deep neurális hálózati könyvtárat és más eszközöket telepítenek előre.


| Operációs rendszer | Illesztő |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (. exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-illesztőprogramok

A Microsoft a virtuális munkaállomásként vagy virtuális alkalmazásként használt NV-és NVv3-sorozatú virtuális gépek NVIDIA GRID-illesztőprogram-telepítőit terjeszti ki. Csak az alábbi táblázatban felsorolt operációs rendszereken telepítse ezeket a GRID-illesztőprogramokat az Azure NV-sorozatú virtuális gépeken. Ezek az illesztőprogramok közé tartoznak az Azure-beli GRID virtuális GPU-szoftverek licencelése. Nincs szükség az NVIDIA vGPU szoftverlicenc-kiszolgáló beállítására.

Az Azure által újraterjesztett GRID-illesztőprogramok nem működnek a nem NV sorozatú virtuális gépeken, például az NC, a NCv2, a NCv3, az ND és a NDv2 sorozatú virtuális gépeken.

Vegye figyelembe, hogy az NVIDIA-bővítmény mindig telepíti a legújabb illesztőprogramot. Az előző verzióra mutató hivatkozásokat biztosítunk azon ügyfelek számára, akik egy régebbi verziótól függenek.

A Windows Server 2019, a Windows Server 2016 és a Windows 10 rendszerhez (akár Build 2004):
- [11. rács (451,48)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Rács 10,1 (442,06)](https://download.microsoft.com/download/b/8/f/b8f5ecec-b8f9-47de-b007-ac40adc88dc8/442.06_grid_win10_64bit_international_whql.exe) (. exe) 

Windows Server 2012 R2 esetén: 
- [11. rács (451,48)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)
- [Rács 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  
