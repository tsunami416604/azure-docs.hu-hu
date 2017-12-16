---
title: "Windows Azure N-sorozat illesztőprogram beállítása |} Microsoft Docs"
description: "Windows Azure-ban futó N sorozatú virtuális gépek NVIDIA GPU illesztőprogramok beállítása"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58ca4ea85b6097f7210a21db45791bb43b0e99ea
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Windows Server rendszert futtató N sorozatú virtuális gépek GPU illesztőprogramok beállítása
A Windows Server 2016 vagy a Windows Server 2012 R2 rendszert futtató Azure N sorozatú virtuális gépek GPU lehetőségeinek kihasználásához, telepítse a támogatott NVIDIA video-illesztőprogramok. Ez a cikk lépéseit illesztőprogram beállítása az N-sorozatú virtuális gép telepítése után. Telepítési információk illesztőprogram érhető el is [Linux virtuális gépek](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

RDMA hálózati kapcsolat engedélyezhető RDMA N sorozatú virtuális gépek engedélyezve van, például az azonos rendelkezésre állási készlet NC24r telepítve. Telepítse a Windows hálózati illesztőprogramokat, amelyek lehetővé teszik az RDMA-kapcsolatot a HpcVmDrivers bővítmény szerepelnie kell. A Virtuálisgép-bővítmény adhat hozzá egy RDMA-kompatibilis N sorozatú virtuális gépre [Azure PowerShell](/powershell/azure/overview) az Azure Resource Manager parancsmagok.

> [!NOTE]
> Jelenleg csak Windows Server 2012 R2 támogatja az RDMA hálózati N sorozatú virtuális gépeken.
> 

A legújabb 1.1-es verziójának telepítése HpcVMDrivers bővítmény egy meglévő RDMA-kompatibilis virtuális gépen az USA nyugati régiója régióban myVM nevű virtuális:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  További információkért lásd: [virtuálisgép-bővítmények és a Windows szolgáltatások](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Az RDMA hálózati Message Passing Interface (MPI) forgalmat támogatja a futó alkalmazások [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) vagy Intel MPI 5.x. 


## <a name="next-steps"></a>Következő lépések

* A fejlesztők a NVIDIA Tesla Feldolgozóegységekkel a GPU-gyorsított alkalmazások is letöltheti és telepítheti a [CUDA eszközkészlet 9.1](https://developer.nvidia.com/cuda-downloads). További információkért lásd: a [CUDA a telepítési útmutató](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


