---
title: Az Azure N sorozatú AMD GPU-illesztőprogram beállítása a Windows rendszerhez
description: Az AMD GPU-illesztőprogramok beállítása Az Azure-ban Windows Server vagy Windows rendszert futtató N sorozatú virtuális gépekhez
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 02213feb507e9a032a50241fddf31714b9dfd7ee
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011069"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>AMD GPU-illesztőprogramok telepítése Windows rendszerű N sorozatú virtuális gépekre

Az új Azure NVv4 sorozatú Windows rendszerű virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az AMD GPU-illesztőprogramokat. Az AMD illesztőprogram-bővítmény az elkövetkező hetekben lesz elérhető. Ez a cikk támogatott operációs rendszereket, illesztőprogramokat, valamint manuális telepítési és ellenőrzési lépéseket tartalmaz.

Az alapvető specifikációkat, a tárolási kapacitást és a lemezadatokat a [GPU Windows vm-méretei című](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)témakörben találja.



## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows 10 EVD - Build 1903 <br/><br/>Windows 10 – Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Csatlakozzon a Távoli asztallal az egyes NVv4 sorozatú virtuális gépekhez.

2. Ha Ön NVv4 előzetes verziójú ügyfél, akkor állítsa le a virtuális gép, és várja meg, hogy átáll a Leállítva (Felszabadított) állapotba.

3. Indítsa el a virtuális gép és töltse le a legújabb [AMD Cleanup Utility](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Távolítsa el a meglévő illesztőprogramot az "amdcleanuputility-x64.exe" futtatásával. Ne használjon olyan meglévő tisztítási segédprogramot, amely az előző illesztőprogrammal volt telepítve.  

4. Töltse le és telepítse a legújabb illesztőprogramot.

5. Indítsa újra a virtuális gépet.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Az eszközkezelőben ellenőrizheti az illesztőprogram telepítését. A következő példa bemutatja a Radeon Instinct MI25-kártya sikeres konfigurálását egy Azure NVv4 virtuális számítógépen.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/device-manager.png)

A dxdiag segítségével ellenőrizheti a GPU kijelzőtulajdonságait, beleértve a videoRAM-ot is. A következő példa a Radeon Instinct MI25-kártya 1/2-es partícióját mutatja be egy Azure NVv4 virtuális gépen.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/dxdiag-output.png)

Ha Windows 10 build 1903 vagy újabb rendszert futtat, akkor a dxdiag nem jelenik meg a "Megjelenítés" lapon. Kérjük, használja az "Összes információ mentése" opciót az alján, és a kimeneti fájl megmutatja az AMD MI25 GPU-val kapcsolatos információkat.

![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/dxdiag-details.png)


