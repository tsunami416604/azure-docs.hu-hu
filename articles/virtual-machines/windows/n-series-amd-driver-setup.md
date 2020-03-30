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
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269436"
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

3. Indítsa el a virtuális gép, majd távolítsa el az előnézeti illesztőprogramot a "...\AMDCleanUninstallUtility" mappában található "amdcleanuputility-x64.exe" futtatásával. A pontos elérési út az előző illesztőprogram-telepítőfájlok tartózkodási helyének megfelelően változik.  

4. Töltse le és telepítse a legújabb illesztőprogramot.

5. Indítsa újra a virtuális gépet.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Az eszközkezelőben ellenőrizheti az illesztőprogram telepítését. A következő példa bemutatja a Radeon Instinct MI25-kártya sikeres konfigurálását egy Azure NVv4 virtuális számítógépen.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/device-manager.png)

A dxdiag segítségével ellenőrizheti a GPU kijelzőtulajdonságait, beleértve a videoRAM-ot is. A következő példa a Radeon Instinct MI25-kártya 1/8-as partícióját mutatja be egy Azure NVv4 virtuális gépen.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/dxdiag.png)
