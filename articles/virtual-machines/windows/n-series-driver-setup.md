---
title: Windows Azure N-sorozat illesztőprogram beállítása |} Microsoft Docs
description: Windows Server vagy a Windows Azure-ban futó N sorozatú virtuális gépek NVIDIA GPU illesztőprogramok beállítása
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
ms.date: 04/04/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: efa8c2603d6ff4493656cda41306a5dad46bc5f3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows"></a>Windows rendszerű N sorozatú virtuális gépek GPU illesztőprogramok beállítása 
A Windows Server vagy a Windows támogatott verzióját futtató Azure N sorozatú virtuális gépek GPU lehetőségeinek kihasználásához, NVIDIA video-illesztőprogramok telepíteni kell. Ez a cikk lépéseit illesztőprogram beállítása az N-sorozatú virtuális gép telepítése után. Telepítési információk illesztőprogram érhető el is [Linux virtuális gépek](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alapvető specifikációk, tárolási kapacitás és a lemez adatai: [GPU Windows Virtuálisgép-méretek](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Kapcsolódás az egyes N sorozatú virtuális gépek távoli asztalt.

2. Töltse le, bontsa ki és telepítse a Windows operációs rendszer támogatott illesztőprogramját.

Azure virtuális gépeken portok HV újraindításra szükség az illesztőprogram telepítése után. A hálózati vezérlő által virtuális gépeken újraindításra szükség.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Illesztőprogram-telepítőfájl az Eszközkezelőben ellenőrizheti. A következő példa bemutatja a Tesla K80 kártya sikeres konfigurációs egy Azure NC virtuális gépen.

![GPU illesztőprogram tulajdonságai](./media/n-series-driver-setup/GPU_driver_properties.png)

A GPU eszköz állapotát lekérdező, futtassa a [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogram illesztőprogrammal telepítve.

1. Nyisson meg egy parancssort, és módosítsa a **C:\Program Files\NVIDIA Corporation\NVSMI** könyvtár.

2. Futtassa az `nvidia-smi` parancsot. Ha az illesztőprogram telepítve van a következőhöz hasonló kimenetet fog látni. Vegye figyelembe, hogy **GPU-Util** látható **0 %** kivéve, ha meg van nyitva egy GPU munkaterhelés a virtuális Gépen. A illesztőprogramjának verziószámát és a GPU részletek látható eltérő lehet.

![NVIDIA eszköz állapota](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

RDMA hálózati kapcsolatot az RDMA-kompatibilis N sorozatú virtuális gépeken futó engedélyezhető, mint például az azonos rendelkezésre állási csoport vagy a Virtuálisgép-méretezési készlet NC24r telepítve. Telepítse a Windows hálózati illesztőprogramokat, amelyek lehetővé teszik az RDMA-kapcsolatot a HpcVmDrivers bővítmény szerepelnie kell. A Virtuálisgép-bővítmény adhat hozzá egy RDMA-kompatibilis N sorozatú virtuális gépre [Azure PowerShell](/powershell/azure/overview) az Azure Resource Manager parancsmagok.

A legújabb 1.1-es verziójának telepítése HpcVMDrivers bővítmény egy meglévő RDMA-kompatibilis virtuális gépen az USA nyugati régiója régióban myVM nevű virtuális:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  További információkért lásd: [virtuálisgép-bővítmények és a Windows szolgáltatások](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Az RDMA hálózati Message Passing Interface (MPI) forgalmat támogatja a futó alkalmazások [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) vagy Intel MPI 5.x. 


## <a name="next-steps"></a>További lépések

* A fejlesztők a NVIDIA Tesla Feldolgozóegységekkel a GPU-gyorsított alkalmazások is letöltheti és telepítheti a [CUDA eszközkészlet 9.1](https://developer.nvidia.com/cuda-downloads). További információkért lásd: a [CUDA a telepítési útmutató](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


