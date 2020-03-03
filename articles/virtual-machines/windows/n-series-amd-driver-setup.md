---
title: Azure N sorozatú AMD GPU illesztőprogram-telepítő Windows rendszerhez
description: AMD GPU-illesztőprogramok beállítása az Azure-ban Windows Servert vagy Windowst futtató N sorozatú virtuális gépekhez
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
ms.openlocfilehash: 6dc8c54b9d138ab62e086cca59cd5b4801fa6130
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228337"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Az AMD GPU-illesztőprogramok telepítése a Windows rendszerű N sorozatú virtuális gépeken

Az új Azure NVv4 sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az AMD GPU-illesztőprogramokat. Az AMD-illesztőprogram bővítmény az elkövetkező hetekben lesz elérhető. Ez a cikk a támogatott operációs rendszereket, illesztőprogramokat és manuális telepítési és ellenőrzési lépéseket tartalmazza.

Az alapszintű specifikációk, a tárolási kapacitások és a lemezek részleteiért lásd: [GPU Windowsos virtuális gépek méretei](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows 10 EVD – 1903-es Build <br/><br/>Windows 10 – Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Kapcsolódjon Távoli asztal az egyes NVv4-sorozatú virtuális gépekhez.

2. Ha Ön a NVv4 előzetes verziójának ügyfelét használja, állítsa le a virtuális gépet, és várjon, amíg a leállítva (fel nem szabadítva) állapotba kerül.

3. Indítsa el a virtuális gépet, majd távolítsa el az előzetes verziójú illesztőprogramot a ". ..\AMDCleanUninstallUtility" mappában található "amdcleanuputility-x64. exe" parancs futtatásával. A pontos elérési út attól függően változhat, hogy hol találhatók a korábbi illesztőprogram-telepítési fájlok.  

4. Töltse le és telepítse a legújabb illesztőprogramot.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Az illesztőprogram telepítését a Eszközkezelő ellenőrizheti. Az alábbi példa a Radeon ösztön MI25 kártya sikeres konfigurálását mutatja be egy Azure NVv4 virtuális gépen.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/device-manager.png)

A Dxdiag használatával ellenőrizheti a GPU megjelenítési tulajdonságait, beleértve a videó RAM-ját is. Az alábbi példa egy Azure NVv4 virtuális gépen a Radeon ösztön MI25 kártya 1/8th partícióját mutatja be.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/dxdiag.png)
