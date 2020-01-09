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
ms.openlocfilehash: 6f0aa8a08b8a502edbd15d3ede157b78a13b8588
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479357"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Az AMD GPU-illesztőprogramok telepítése a Windows rendszerű N sorozatú virtuális gépeken

Az új Azure NVv4 sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az AMD GPU-illesztőprogramokat. Az AMD-illesztőprogram bővítmény az elkövetkező hetekben lesz elérhető. Ez a cikk a támogatott operációs rendszereket, illesztőprogramokat és manuális telepítési és ellenőrzési lépéseket tartalmazza.

Az alapszintű specifikációk, a tárolási kapacitások és a lemezek részleteiért lásd: [GPU Windowsos virtuális gépek méretei](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows 10 EVD – 1903-es Build <br/><br/>Windows 10 – Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19. q 4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (. exe) |

## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Kapcsolódjon Távoli asztal az egyes NVv4-sorozatú virtuális gépekhez.

1. Töltse le, bontsa ki és telepítse a Windows operációs rendszerhez támogatott illesztőprogramot.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Az illesztőprogram telepítését a Eszközkezelő ellenőrizheti. Az alábbi példa a Radeon ösztön MI25 kártya sikeres konfigurálását mutatja be egy Azure NVv4 virtuális gépen.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/device-manager.png)

A Dxdiag használatával ellenőrizheti a GPU megjelenítési tulajdonságait, beleértve a videó RAM-ját is. Az alábbi példa egy Azure NVv4 virtuális gépen a Radeon ösztön MI25 kártya 1/8th partícióját mutatja be.
<br />
![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/dxdiag.png)
