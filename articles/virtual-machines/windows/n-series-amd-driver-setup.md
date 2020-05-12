---
title: Azure N sorozatú AMD GPU illesztőprogram-telepítő Windows rendszerhez
description: AMD GPU-illesztőprogramok beállítása az Azure-ban Windows Servert vagy Windowst futtató N sorozatú virtuális gépekhez
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 1bcc13db3f503c80fda71a2104d0ff8d99e67df6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197999"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Az AMD GPU-illesztőprogramok telepítése a Windows rendszerű N sorozatú virtuális gépeken

Az új Azure NVv4 sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az AMD GPU-illesztőprogramokat. Az [AMD GPU illesztőprogram-bővítmény](../extensions/hpccompute-amd-gpu-windows.md) egy NVv4-sorozatú virtuális gépen telepíti az AMD GPU-illesztőprogramokat. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [AMD GPU illesztőprogram-bővítmény dokumentációját](../extensions/hpccompute-amd-gpu-windows.md) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez.

Ha manuálisan telepíti az AMD GPU-illesztőprogramokat, ez a cikk a támogatott operációs rendszereket, illesztőprogramokat és telepítési és ellenőrzési lépéseket tartalmazza.

A NVv4 virtuális gépeken csak a Microsoft által közzétett GPU-illesztőprogramok támogatottak. Ne telepítse a GPU-illesztőprogramokat bármilyen más forrásból.

Az alapszintű specifikációk, a tárolási kapacitások és a lemezek részleteiért lásd: [GPU Windowsos virtuális gépek méretei](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows 10 EVD – 1903-es Build <br/><br/>Windows 10 – Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Kapcsolódjon Távoli asztal az egyes NVv4-sorozatú virtuális gépekhez.

2. Ha Ön a NVv4 előzetes verziójának ügyfelét használja, állítsa le a virtuális gépet, és várjon, amíg a leállítva (fel nem szabadítva) állapotba kerül.

3. Indítsa el a virtuális gépet, és töltse le a legújabb [AMD-karbantartó segédprogramot](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Távolítsa el a meglévő illesztőprogramot a "amdcleanuputility-x64. exe" futtatásával. Ne használjon olyan meglévő karbantartási segédprogramot, amely az előző illesztőprogrammal lett telepítve.  

4. Töltse le és telepítse a legújabb illesztőprogramot.

5. Indítsa újra a virtuális gépet.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Az illesztőprogram telepítését a Eszközkezelő ellenőrizheti. Az alábbi példa a Radeon ösztön MI25 kártya sikeres konfigurálását mutatja be egy Azure NVv4 virtuális gépen.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/device-manager.png)

A Dxdiag használatával ellenőrizheti a GPU megjelenítési tulajdonságait, beleértve a videó RAM-ját is. Az alábbi példa egy Azure NVv4 virtuális gépen található Radeon ösztön MI25 kártya 1/2-es partícióját mutatja be.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/dxdiag-output.png)

Ha a Windows 10 Build 1903-es vagy újabb verzióját futtatja, akkor a Dxdiag nem jelenít meg információt a "Display" (megjelenítés) lapon. Használja az összes információ mentése lehetőséget az alján, és a kimeneti fájl megjeleníti az AMD MI25 GPU-val kapcsolatos információkat.

![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/dxdiag-details.png)


