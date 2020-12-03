---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: caad292f06710fca8d6f64476eead5dfcb164e9d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96536915"
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) illesztőprogramok

Az NC, NCv2, NCv3, NCasT4_v3, ND és NDv2 sorozatú virtuális gépek NVIDIA Tesla-(CUDA-) illesztőprogramjai csak az alábbi táblázatban felsorolt operációs rendszereken támogatottak. Az illesztőprogram-letöltési hivatkozások a kiadvány aktuális időpontjában jelennek meg. A legújabb illesztőprogramokért látogasson el az [NVIDIA](https://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server rendszerű virtuális gépeken a CUDA-illesztőprogram manuális telepítésének alternatívájaként üzembe helyezhet egy Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) rendszerképet. A Windows Server 2016 DSVM kiadásai NVIDIA CUDA-illesztőprogramokat, a CUDA Deep neurális hálózati könyvtárat és más eszközöket telepítenek előre.


| Operációs rendszer | Illesztő |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-illesztőprogramok

A Microsoft a virtuális munkaállomásként vagy virtuális alkalmazásként használt NV-és NVv3-sorozatú virtuális gépek NVIDIA GRID-illesztőprogram-telepítőit terjeszti ki. Csak az alábbi táblázatban felsorolt operációs rendszereken telepítse ezeket a GRID-illesztőprogramokat az Azure NV-sorozatú virtuális gépeken. Ezek az illesztőprogramok közé tartoznak az Azure-beli GRID virtuális GPU-szoftverek licencelése. Nincs szükség az NVIDIA vGPU szoftverlicenc-kiszolgáló beállítására.

Az Azure által újraterjesztett GRID-illesztőprogramok nem működnek a nem NV sorozatú virtuális gépeken, például az NC, a NCv2, a NCv3, az ND és a NDv2 sorozatú virtuális gépeken. Az egyetlen kivétel a NCas_T4_V3 VM-sorozat, ahol a GRID-illesztőprogramok lehetővé teszik az NV-sorozathoz hasonló grafikus funkciókat.

Vegye figyelembe, hogy az NVIDIA-bővítmény mindig telepíti a legújabb illesztőprogramot. Az előző verzióra mutató hivatkozásokat biztosítunk azon ügyfelek számára, akik egy régebbi verziótól függenek.

A Windows Server 2019, a Windows Server 2016 és a Windows 10 rendszerhez (akár Build 2004):
- [Rács 11,2 (452,57)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Rács 11,1 (452,39)](https://download.microsoft.com/download/9/9/1/99186e1b-d27d-47d5-9957-175c88f4efbe/452.39_grid_win10_64bit_whql.exe) (. exe) 

Windows Server 2012 R2 esetén: 
- [Rács 11,0 (451,48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (. exe) 
- [Rács 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  

Az összes korábbi NVIDIA GRID-illesztőprogram-hivatkozás teljes listájáért látogasson el a [githubra](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
