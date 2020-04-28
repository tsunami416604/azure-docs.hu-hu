---
title: Az Azure N sorozatú GPU-illesztőprogram beállítása a Windows hoz
description: Az NVIDIA GPU-illesztőprogramok beállítása Windows Server vagy Windows rendszert futtató N sorozatú virtuális gépekhez az Azure-ban
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc11937410bf0307a00895e0ebd1f01a58bd1b1b
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865781"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Nvidia GPU-illesztőprogramok telepítése Windows rendszerű N sorozatú virtuális gépekre 

A Windows rendszert futtató Azure N sorozatú virtuális gépek GPU-funkcióinak kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat. Az [NVIDIA GPU illesztőprogram-bővítmény](../extensions/hpccompute-gpu-windows.md) megfelelő NVIDIA CUDA vagy GRID illesztőprogramokat telepít egy N sorozatú Virtuális gépre. Telepítse vagy kezelje a bővítményt az Azure Portalon vagy az olyan eszközökkel, például az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával, mint például az Azure PowerShell vagy az Azure Resource Manager-sablonok. A támogatott operációs rendszereket és a telepítési lépéseket az [NVIDIA GPU-illesztőprogram-bővítmény dokumentációjában](../extensions/hpccompute-gpu-windows.md) találja.

Ha úgy dönt, hogy manuálisan telepíti a GPU-illesztőprogramokat, ez a cikk támogatott operációs rendszereket, illesztőprogramokat, valamint telepítési és ellenőrzési lépéseket tartalmaz. A [linuxos virtuális gépekhez](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)manuális illesztőprogram-beállítási információk is rendelkezésre állnak.

Az alapvető specifikációkat, a tárolási kapacitást és a lemezadatokat a [GPU Windows vm-méretei című](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)témakörben találja. 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Csatlakozzon a Távoli asztalminden N-sorozatú virtuális géphez.

2. Töltse le, bontsa ki és telepítse a Windows operációs rendszer támogatott illesztőprogramját.

Grid illesztőprogram telepítése után a virtuális gép, újraindítás szükséges. A CUDA illesztőprogram telepítése után nincs szükség újraindításra.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Kérjük, vegye figyelembe, hogy az Nvidia vezérlőpult csak a GRID illesztőprogram telepítésével érhető el. Ha telepítette a CUDA illesztőprogramokat, akkor az Nvidia vezérlőpanel je nem lesz látható.

Az eszközkezelőben ellenőrizheti az illesztőprogram telepítését. A következő példa a Tesla K80-kártya sikeres konfigurálását mutatja be egy Azure NC virtuális gépen.

![GPU-illesztőprogram tulajdonságai](./media/n-series-driver-setup/GPU_driver_properties.png)

A GPU-eszköz állapotának lekérdezéséhez futtassa az illesztőprogrammal telepített [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot.

1. Nyisson meg egy parancssort, és váltson a **C:\Program Files\NVIDIA Corporation\NVSMI** könyvtárra.

2. Futtassa az `nvidia-smi` parancsot. Ha az illesztőprogram telepítve van, a következőhöz hasonló kimenet jelenik meg. A **GPU-Util** **0%-ot** mutat, kivéve, ha jelenleg fut egy GPU-munkaterhelés a virtuális gépen. Az illesztőprogram verziója és a GPU adatai eltérhetnek a megjelenítettektől.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

RdMA hálózati kapcsolat engedélyezhető rdma-képes N-sorozatú virtuális gépek, mint például NC24r telepített ugyanabban a rendelkezésre állási csoportban, vagy egy elhelyezési csoportban egy virtuális gép méretezési csoportban. Az RDMA-kapcsolatot engedélyező Windows hálózati eszközillesztők telepítéséhez hozzá kell adni a HpcVmDrivers kiterjesztést. A virtuálisgép-bővítmény hozzáadása egy RDMA-kompatibilis N-sorozatú virtuális géphez, használja az [Azure PowerShell-parancsmagokat](/powershell/azure/overview) az Azure Resource Managerhez.

A legújabb 1.1-es verzióJú HpcVMDrivers bővítmény telepítése egy meglévő RDMA-kompatibilis virtuális gépre, amelyet myVM-nek neveznek el az USA nyugati régiójában:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  További információt a [Virtuálisgép-bővítmények és -szolgáltatások windowsos témakörben talál.](extensions-features.md)

Az RDMA-hálózat támogatja az MPI-forgalmat a [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) vagy Intel MPI 5.x rendszerrel futó alkalmazások esetében. 


## <a name="next-steps"></a>További lépések

* Az NVIDIA Tesla GPU-k GPU-s gyorsítású alkalmazásait fejlesztő fejlesztők a legújabb [CUDA eszközkészletet](https://developer.nvidia.com/cuda-downloads)is letölthetik és telepíthetik. További információt a [CUDA telepítési útmutatójában talál.](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)


