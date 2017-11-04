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
ms.date: 07/07/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b480d10df777a2757c073ff77e1845d33d63163a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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

2. Futtatás **nvidia-smi**. Ha az adatforrásnak alatt hasonló kimenetet fog látni. Vegye figyelembe, hogy **GPU-Util** látható **0 %** kivéve, ha meg van nyitva egy GPU munkaterhelés a virtuális Gépen.

![NVIDIA eszköz állapota](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>RDMA hálózati NC24r virtuális gépekhez

Az azonos rendelkezésre állási készlet telepített NC24r virtuális gépek RDMA hálózati kapcsolat engedélyezhető. Telepítse a Windows hálózati illesztőprogramokat, amelyek lehetővé teszik az RDMA-kapcsolatot a HpcVmDrivers bővítmény szerepelnie kell. A Virtuálisgép-bővítmény adhat hozzá egy NC24r VM [Azure PowerShell](/powershell/azure/overview) az Azure Resource Manager parancsmagok.

> [!NOTE]
> Jelenleg csak Windows Server 2012 R2 támogatja az RDMA hálózati NC24r virtuális gépeken.
> 

A legújabb 1.1-es verziójának telepítése HpcVMDrivers bővítmény egy meglévő RDMA-kompatibilis virtuális gépen az USA nyugati régiója régióban myVM nevű virtuális:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  További információkért lásd: [virtuálisgép-bővítmények és a Windows szolgáltatások](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Az RDMA hálózati Message Passing Interface (MPI) forgalmat támogatja a futó alkalmazások [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) vagy Intel MPI 5.x. 


## <a name="next-steps"></a>Következő lépések

* Az N-sorozatú virtuális gépeken a NVIDIA Feldolgozóegységekkel kapcsolatos további információkért lásd:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (az Azure NC virtuális gépek)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (az Azure Permanens virtuális gépek)

* Alkalmazások GPU-az elérését gyorsítja fel a NVIDIA Tesla Feldolgozóegységekkel a fejlesztők is letöltheti és telepítheti a CUDA eszközkészlet 8 [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) vagy [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe). További információkért lásd: a [CUDA a telepítési útmutató](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


