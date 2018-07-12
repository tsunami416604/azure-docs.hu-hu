---
title: Az Azure N-sorozat illesztőinek Windows |} A Microsoft Docs
description: Az N sorozatú virtuális gépek Windows Server vagy a Windows Azure-ban futó NVIDIA GPU-illesztők beállítása
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/19/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50d9ea88afc0e7d96d71b2ab26c8a8489ae41fee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38719650"
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows"></a>Az N sorozatú virtuális gépek Windows rendszerű GPU-illesztők beállítása 
Windows rendszerű Azure N-sorozatú virtuális gépek a GPU-funkciókkal kihasználásához NVIDIA GPU-illesztőprogramokat kell telepíteni. A [NVIDIA GPU illesztőprogramjának bővítmény](../extensions/hpccompute-gpu-windows.md) telepíti a megfelelő NVIDIA CUDA vagy GRID illesztőprogramok-N-sorozatú virtuális gépen. Telepítse, vagy a bővítmény az Azure portal vagy az eszközök, például az Azure PowerShell vagy az Azure Resource Manager-sablonok használata kezelheti. Tekintse meg a [NVIDIA GPU illesztőprogramjának bővítmény dokumentáció](../extensions/hpccompute-gpu-windows.md) támogatott operációs rendszerek és a telepítés lépéseit.

Ha manuálisan telepíti a GPU-illesztőprogramokat, a cikk ismerteti a támogatott operációs rendszerek, az illesztőprogramok és a telepítés és ellenőrzés lépésein. Telepítési információk manuális illesztőprogram érhető el is [Linux rendszerű virtuális gépek](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alapvető adatait tartalmazza, a tárolási kapacitások és a lemez adatai: [GPU Windows Virtuálisgép-méretek](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Eszközillesztők telepítése

1. Csatlakozzon az egyes N-sorozatú virtuális géphez a távoli asztal.

2. Töltse le, csomagolja ki, és telepítse a Windows operációs rendszer támogatott illesztőprogramját.

Azure virtuális gépeken NV újraindításra szükség az illesztőprogram telepítése után. A hálózati vezérlő virtuális gépek a számítógép újraindítását, nem szükséges.

## <a name="verify-driver-installation"></a>Ellenőrizze az illesztőprogram telepítése

Eszközillesztők telepítése az Eszközkezelőben ellenőrizheti. Az alábbi példa bemutatja a Tesla K80-kártya sikeres konfiguráció-beli hálózati vezérlő virtuális gépen.

![GPU-illesztőprogram tulajdonságai](./media/n-series-driver-setup/GPU_driver_properties.png)

A GPU-Eszközállapot lekérdezéséhez futtathatja az [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogram az illesztőprogram telepítve.

1. Nyisson meg egy parancssort, és módosítsa a **C:\Program Files\NVIDIA Corporation\NVSMI** könyvtár.

2. Futtassa az `nvidia-smi` parancsot. Ha az illesztőprogram telepítve van a következőhöz hasonló kimenetet fog látni. Vegye figyelembe, hogy **GPU-Util** látható **0 %-os** , kivéve, ha a virtuális gép jelenleg fut egy GPU számítási feladatot. Az illesztőprogram verziója és a GPU-részletek látható eltérő lehet.

![NVIDIA eszköz állapota](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

RDMA hálózati kapcsolat RDMA-kompatibilis az N sorozatú virtuális gépek például NC24r helyezze üzembe, ugyanazon rendelkezésre állási csoportban vagy egy Virtuálisgép-méretezési csoport egyetlen elhelyezési csoport engedélyezhető. A HpcVmDrivers bővítmény telepítéséhez a Windows hálózati eszközillesztőket, amelyek lehetővé teszik az RDMA-kapcsolattal hozzá kell adni. A Virtuálisgép-bővítmény egy RDMA-kompatibilis N-sorozatú virtuális gépek hozzáadásához használja [Azure PowerShell-lel](/powershell/azure/overview) parancsmagok az Azure Resource Manager.

Telepítse a legújabb verziót 1.1-es HpcVMDrivers futtatására szolgáló bővítmény egy meglévő RDMA-kompatibilis virtuális gép az USA nyugati régiójában myVM nevű:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  További információkért lásd: [virtuálisgép-bővítmények és szolgáltatások Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Az RDMA hálózati forgalom Message Passing Interface (MPI) támogatja a futó alkalmazások [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) vagy az Intel MPI 5.x. 


## <a name="next-steps"></a>További lépések

* Fejlesztők számára a gyorsított GPU alkalmazások esetében az NVIDIA Tesla gpu-kat is töltse le és telepítse a legújabb [CUDA eszközkészlet](https://developer.nvidia.com/cuda-downloads). További információkért lásd: a [CUDA telepítési útmutató](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


