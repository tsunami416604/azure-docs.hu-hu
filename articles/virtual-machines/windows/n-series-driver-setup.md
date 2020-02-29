---
title: Azure N-Series GPU illesztőprogram-telepítő Windows rendszerhez
description: NVIDIA GPU-illesztőprogramok beállítása az Azure-ban Windows Servert vagy Windowst futtató N sorozatú virtuális gépekhez
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 631266f983886e3ca34d609b425f8a71b808b39f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919396"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>NVIDIA GPU-illesztőprogramok telepítése a Windows rendszerű N sorozatú virtuális gépeken 

Az Azure N sorozatú, Windows rendszerű virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat. Az [NVIDIA GPU illesztőprogram-bővítmény](../extensions/hpccompute-gpu-windows.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](../extensions/hpccompute-gpu-windows.md) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez.

Ha a GPU-illesztőprogramokat manuálisan telepíti, ez a cikk a támogatott operációs rendszereket, illesztőprogramokat és telepítési és ellenőrzési lépéseket tartalmazza. A Linux rendszerű [virtuális gépekhez](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)a manuális illesztőprogram beállítási információi is elérhetők.

Az alapszintű specifikációk, a tárolási kapacitások és a lemezek részleteiért lásd: [GPU Windowsos virtuális gépek méretei](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Kapcsolódjon Távoli asztal az egyes N sorozatú virtuális gépekhez.

2. Töltse le, bontsa ki és telepítse a Windows operációs rendszerhez támogatott illesztőprogramot.

Miután a GRID-illesztőprogram telepítése egy virtuális gépre megtörténik, újraindítás szükséges. A CUDA-illesztőprogram telepítése után a rendszer nem igényel újraindítást.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Vegye figyelembe, hogy az NVIDIA Vezérlőpult csak a GRID-illesztőprogram telepítésével érhető el. Ha telepítette a CUDA-illesztőprogramokat, az NVIDIA Vezérlőpult nem lesz látható.

Az illesztőprogram telepítését a Eszközkezelő ellenőrizheti. Az alábbi példa a Tesla K80 kártya sikeres konfigurálását mutatja be egy Azure NC virtuális gépen.

![GPU-illesztőprogram tulajdonságai](./media/n-series-driver-setup/GPU_driver_properties.png)

A GPU-eszköz állapotának lekéréséhez futtassa az illesztőprogrammal telepített [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot.

1. Nyisson meg egy parancssort, és váltson a **C:\Program Files\NVIDIA Corporation\NVSMI** könyvtárba.

2. Futtassa az `nvidia-smi` parancsot. Ha az illesztőprogram telepítve van, az alábbihoz hasonló kimenet jelenik meg. A **GPU-util** **0%-ot** mutat, ha jelenleg nem fut GPU-munkaterhelés a virtuális gépen. Az illesztőprogram verziószáma és a GPU adatai eltérhetnek a megjelenített adatoktól.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

A RDMA hálózati kapcsolat engedélyezhető a RDMA-kompatibilis N sorozatú virtuális gépeken, például az azonos rendelkezésre állási csoportokban vagy a virtuálisgép-méretezési csoportok egyetlen elhelyezési csoportjában telepített NC24r. A HpcVmDrivers bővítményt hozzá kell adni a RDMA-kapcsolatot engedélyező Windows hálózati eszközillesztők telepítéséhez. Ha hozzá szeretné adni a virtuálisgép-bővítményt egy RDMA-kompatibilis N sorozatú virtuális géphez, használja a Azure Resource Manager [Azure PowerShell](/powershell/azure/overview) parancsmagokat.

A legújabb 1,1 HpcVMDrivers bővítmény telepítése egy myVM nevű meglévő RDMA-kompatibilis virtuális gépen az USA nyugati régiójában:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  További információ: [virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](extensions-features.md).

A RDMA-hálózat támogatja a [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) vagy az Intel MPI 5. x verzióban futó alkalmazások üzenet-átadási felületének (MPI) forgalmát. 


## <a name="next-steps"></a>Következő lépések

* Az NVIDIA Tesla GPU-k számára készült GPU-gyorsított alkalmazásokat fejlesztő fejlesztők is letölthetik és telepíthetik a legújabb [CUDA-eszközkészletet](https://developer.nvidia.com/cuda-downloads). További információ: a [CUDA telepítési útmutatója](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


